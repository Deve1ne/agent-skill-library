# Patterns Multi-LLM — Référence Pratique

---

## Pattern 1 : Routing (Routage intelligent)

**Concept** : Un "router" décide quel LLM appeler selon la nature de la tâche.

**Quand l'utiliser** : Optimiser coût + qualité sur un pipeline à fort volume.

```python
# Exemple : router simple basé sur catégorie de tâche
import anthropic
from openai import OpenAI

def route_task(task: str, content: str) -> str:
    routing_map = {
        "code":      ("claude-sonnet-4-6", "anthropic"),
        "search":    ("gemini-2.0-pro",    "google"),
        "cheap":     ("deepseek-chat",     "deepseek"),
        "creative":  ("claude-opus-4-6",   "anthropic"),
    }
    model, provider = routing_map.get(task, ("claude-sonnet-4-6", "anthropic"))
    
    if provider == "anthropic":
        client = anthropic.Anthropic()
        response = client.messages.create(
            model=model, max_tokens=1024,
            messages=[{"role": "user", "content": content}]
        )
        return response.content[0].text
    
    elif provider == "deepseek":
        # DeepSeek utilise l'API compatible OpenAI
        client = OpenAI(
            api_key="YOUR_DEEPSEEK_KEY",
            base_url="https://api.deepseek.com"
        )
        response = client.chat.completions.create(
            model="deepseek-chat",
            messages=[{"role": "user", "content": content}]
        )
        return response.choices[0].message.content

# Usage
result = route_task("code", "Écris une fonction de tri fusion en Python")
result = route_task("cheap", "Résume ce texte en 3 bullet points: ...")
```

**Coût estimé** : DeepSeek ~10-100x moins cher que Claude/GPT pour les tâches simples.

---

## Pattern 2 : Pipeline en cascade (Spécialisation)

**Concept** : Chaque LLM traite une étape spécialisée, passe le résultat au suivant.

**Quand l'utiliser** : Workflow avec étapes distinctes (recherche → analyse → rédaction).

```python
# Exemple : pipeline veille techno
# Étape 1 : Gemini cherche (meilleur pour recherche temps réel)
# Étape 2 : DeepSeek filtre/structure (moins cher)
# Étape 3 : Claude rédige le rapport final (meilleur pour écriture)

async def pipeline_veille(topic: str) -> str:
    
    # 1. Recherche avec Gemini (Google Search natif)
    raw_search = await gemini_search(
        f"Dernières actualités sur {topic} - 48h",
        tools=[{"google_search": {}}]
    )
    
    # 2. Filtrage/structuration avec DeepSeek (économique)
    structured = await deepseek_complete(
        f"""Extrais les 5 informations les plus importantes de :
        {raw_search}
        
        Format JSON: [{{"titre": "", "impact": "high/medium/low", "résumé": ""}}]"""
    )
    
    # 3. Rédaction finale avec Claude (meilleur pour écriture)
    report = await claude_complete(
        f"""Rédige un rapport de veille professionnel sur {topic} 
        basé sur ces données structurées : {structured}
        
        Format : executive summary + 5 points clés + recommandations"""
    )
    
    return report
```

---

## Pattern 3 : Orchestrateur/Workers

**Concept** : Un LLM "chef d'orchestre" décompose et délègue à des LLMs spécialisés.

**Quand l'utiliser** : Tâches complexes nécessitant plusieurs expertises.

```python
# L'orchestrateur est Claude (meilleur pour raisonnement)
# Les workers sont spécialisés selon leur force

ORCHESTRATOR_SYSTEM = """
Tu es un orchestrateur de tâches IA. 
Pour chaque tâche reçue :
1. Décompose en sous-tâches atomiques
2. Pour chaque sous-tâche, indique le worker approprié :
   - "code_worker" pour tout ce qui est code/technique
   - "research_worker" pour recherche et faits actuels  
   - "writing_worker" pour rédaction créative
   - "analysis_worker" pour analyse de données
3. Synthèse les résultats en output final

Réponds en JSON : {"plan": [...], "synthesis_prompt": "..."}
"""

async def orchestrate(task: str) -> str:
    # Planification par Claude
    plan = await claude_complete(task, system=ORCHESTRATOR_SYSTEM)
    plan_data = json.loads(plan)
    
    # Exécution parallèle des workers
    results = await asyncio.gather(*[
        execute_worker(step["worker"], step["subtask"])
        for step in plan_data["plan"]
    ])
    
    # Synthèse finale
    return await claude_complete(
        plan_data["synthesis_prompt"] + "\n\nRésultats workers : " + str(results)
    )

async def execute_worker(worker_type: str, task: str) -> str:
    workers = {
        "code_worker":     lambda t: claude_complete(t, system="Expert code Python/JS"),
        "research_worker": lambda t: gemini_search(t),
        "writing_worker":  lambda t: claude_complete(t, system="Expert rédaction"),
        "analysis_worker": lambda t: deepseek_complete(t),  # moins cher pour analyse
    }
    return await workers[worker_type](task)
```

---

## Pattern 4 : Ensemble + Vote (Robustesse)

**Concept** : Même tâche envoyée à N LLMs, consolidation des réponses.

**Quand l'utiliser** : Décisions critiques, vérification de facts, code critique.

```python
async def ensemble_vote(question: str, threshold: float = 0.6) -> dict:
    """Envoie à 3 LLMs, consolide si consensus >= threshold"""
    
    responses = await asyncio.gather(
        claude_complete(question),
        openai_complete(question),
        deepseek_complete(question)
    )
    
    # Claude juge le consensus (méta-analyse)
    consensus_prompt = f"""
    Voici 3 réponses à la question : "{question}"
    
    Réponse 1 (Claude) : {responses[0]}
    Réponse 2 (GPT) : {responses[1]}  
    Réponse 3 (DeepSeek) : {responses[2]}
    
    1. Y a-t-il un consensus ? (oui/non)
    2. Niveau de confiance (0.0 à 1.0) ?
    3. Synthèse consensuelle ou points de divergence ?
    
    JSON: {{"consensus": bool, "confidence": float, "synthesis": "..."}}
    """
    
    return json.loads(await claude_complete(consensus_prompt))
```

---

## Frameworks d'orchestration disponibles (2026)

| Framework | Langage | Style | Gratuit | Auto-hébergeable | Stars GitHub |
|-----------|---------|-------|---------|-----------------|-------------|
| **LangGraph** | Python | Graph-based | 💚 | 🏠 | ~40K |
| **CrewAI** | Python | Role-based | 💚 | 🏠 | ~30K |
| **OpenAI Agents SDK** | Python/JS | Handoffs | 💚 | 🏠 | ~20K |
| **Google ADK** | Python | Hierarchical | 💚 | 🏠 | ~15K |
| **n8n** | No-code | Visual | 💛 | 🏠 | ~50K |
| **Flowise** | No-code | Visual | 💚 | 🏠 | ~35K |
| **AutoGen** | Python | Conversational | 💚 | 🏠 | ~35K |

### Choix recommandé selon profil :

- **Dev Python, contrôle maximal** → LangGraph
- **Équipe, rôles définis, lisibilité** → CrewAI  
- **Écosystème OpenAI/Claude mixte** → OpenAI Agents SDK
- **No-code + intégrations** → n8n (auto-hébergé)
- **RAG + agents visuels** → Flowise

---

## LangGraph — Démarrage rapide

```bash
# Installation
pip install langgraph langchain-anthropic

# Minimal example
```

```python
from langgraph.graph import StateGraph, END
from langchain_anthropic import ChatAnthropic
from typing import TypedDict

class AgentState(TypedDict):
    task: str
    result: str
    iteration: int

llm = ChatAnthropic(model="claude-sonnet-4-6")

def researcher(state: AgentState) -> AgentState:
    response = llm.invoke(f"Recherche sur : {state['task']}")
    return {**state, "result": response.content, "iteration": state["iteration"] + 1}

def writer(state: AgentState) -> AgentState:
    response = llm.invoke(f"Rédige un rapport basé sur : {state['result']}")
    return {**state, "result": response.content}

def should_continue(state: AgentState) -> str:
    return "writer" if state["iteration"] >= 1 else "researcher"

# Construction du graph
graph = StateGraph(AgentState)
graph.add_node("researcher", researcher)
graph.add_node("writer", writer)
graph.add_edge("researcher", "writer")
graph.add_edge("writer", END)
graph.set_entry_point("researcher")

app = graph.compile()
result = app.invoke({"task": "Nouveautés Claude 4.6", "result": "", "iteration": 0})
```

---

## CrewAI — Démarrage rapide

```bash
pip install crewai
```

```python
from crewai import Agent, Task, Crew
from langchain_anthropic import ChatAnthropic

llm = ChatAnthropic(model="claude-sonnet-4-6")

# Définir les agents avec leurs rôles
researcher = Agent(
    role="Chercheur",
    goal="Trouver des informations précises et récentes",
    backstory="Expert en recherche d'information",
    llm=llm, verbose=True
)

analyst = Agent(
    role="Analyste",
    goal="Analyser et structurer l'information",
    backstory="Expert en analyse de données",
    llm=llm, verbose=True
)

# Définir les tâches
research_task = Task(
    description="Recherche les dernières nouveautés sur {topic}",
    agent=researcher,
    expected_output="Liste de faits structurés"
)

analysis_task = Task(
    description="Analyse et synthétise les informations trouvées",
    agent=analyst,
    expected_output="Rapport d'analyse avec recommandations",
    context=[research_task]
)

# Assembler et lancer
crew = Crew(agents=[researcher, analyst], tasks=[research_task, analysis_task])
result = crew.kickoff(inputs={"topic": "IA agentique 2026"})
```
