# Bibliothèque de Cas Concrets — Agentique

> Organisée par niveau et domaine. Chaque cas a : objectif, stack, coût, difficulté.

---

## Niveau 1 — Agents outillés simples

### CAS-001 : Agent de veille technologique
**Objectif** : Chaque matin, un agent cherche les news IA et envoie un digest
**Stack** : Claude + web_search + email MCP
**Coût** : 💛 Claude Pro (~$20/mois)
**Difficulté** : ⭐⭐☆☆☆

**Prompt système agent :**
```
Tu es un agent de veille IA. 
Chaque fois que tu es invoqué :
1. Recherche les news IA des dernières 24h
2. Sélectionne les 5 plus importantes pour un dev
3. Rédige un digest en 200 mots avec liens
4. Format : titre → résumé → impact → lien
```

---

### CAS-002 : Agent de revue de code
**Objectif** : L'agent analyse un PR GitHub et commente automatiquement
**Stack** : Claude + github-mcp
**Coût** : 💛 Claude API (~$0.01/review)
**Difficulté** : ⭐⭐☆☆☆

---

### CAS-003 : Agent de documentation
**Objectif** : Lire un codebase et générer/mettre à jour la doc
**Stack** : Claude Code + filesystem-mcp
**Coût** : 💛 Claude Pro
**Difficulté** : ⭐⭐⭐☆☆

---

## Niveau 2 — Agents avec MCP

### CAS-010 : Agent SQL en langage naturel
**Objectif** : "Donne-moi les ventes du mois dernier par région" → SQL → résultat
**Stack** : Claude + postgres-mcp (ou sqlite-mcp pour local)
**Coût** : 💚 Gratuit (claude.ai + postgres local)
**Difficulté** : ⭐⭐☆☆☆

**Setup complet :**
```bash
# 1. Lancer postgres local (Docker)
docker run -d -e POSTGRES_PASSWORD=test -p 5432:5432 postgres

# 2. Configurer MCP dans Claude Code
# .claude/settings.json :
{
  "mcpServers": {
    "postgres": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-postgres",
               "postgresql://postgres:test@localhost/ma_db"]
    }
  }
}

# 3. Dans Claude : "Interroge la table orders et donne-moi le CA par mois"
```

---

### CAS-011 : Agent de scraping et structuration
**Objectif** : Scraper des pages web, extraire données, stocker en DB
**Stack** : Claude + puppeteer-mcp + sqlite-mcp
**Coût** : 💚 Gratuit
**Difficulté** : ⭐⭐⭐☆☆

---

### CAS-012 : Agent de gestion de projet
**Objectif** : Créer issues GitHub depuis une description, assigner, labeler
**Stack** : Claude + github-mcp
**Coût** : 💚 Gratuit
**Difficulté** : ⭐⭐☆☆☆

---

## Niveau 3 — Multi-agent simple

### CAS-020 : Pipeline recherche → rapport PDF
**Objectif** : Input : sujet → Output : rapport PDF professionnel complet
**Stack** : Claude orchestrateur + Gemini researcher + Claude writer + WeasyPrint
**Coût** : 💛 ~$0.05 par rapport
**Difficulté** : ⭐⭐⭐☆☆

**Architecture :**
```
Input (sujet)
    ↓
[Gemini] Recherche web (Google natif)
    ↓
[DeepSeek] Structuration (moins cher)
    ↓
[Claude] Rédaction rapport
    ↓
[Script Python] Génération PDF
    ↓
Output (rapport.pdf)
```

---

### CAS-021 : Agent de CI/CD intelligent
**Objectif** : Analyser les échecs de CI, proposer des corrections automatiques
**Stack** : Claude + github-mcp + git-mcp
**Coût** : 💛 Claude API
**Difficulté** : ⭐⭐⭐⭐☆

---

### CAS-022 : Chatbot RAG sur documentation privée
**Objectif** : Chatbot qui répond sur la base de ta doc interne
**Stack** : Claude + filesystem-mcp + ChromaDB (local, gratuit)
**Coût** : 💚 Gratuit (self-hosted)
**Difficulté** : ⭐⭐⭐☆☆

**Stack auto-hébergé :**
```bash
# ChromaDB (vector store local)
pip install chromadb langchain-anthropic

# Flowise (UI visuelle, open source)
npm install -g flowise
flowise start
# → Interface sur http://localhost:3000
```

---

## Niveau 4 — Multi-LLM coordonné

### CAS-030 : Usine de contenu multi-modèles
**Objectif** : Produire du contenu à grande échelle, optimisé coût/qualité
**Stack** : 
- Gemini : recherche + faits actuels
- DeepSeek : premier draft (80% moins cher)
- Claude : révision finale et style
**Coût** : 💛 ~$0.002 par article (vs $0.02 avec Claude seul)
**Difficulté** : ⭐⭐⭐☆☆

---

### CAS-031 : Agent de support multi-LLM avec fallback
**Objectif** : Bot support avec routage intelligent + fallback si LLM down
**Stack** : Claude principal + GPT fallback + DeepSeek pour FAQ cheap
**Coût** : 💛 Variable selon volume
**Difficulté** : ⭐⭐⭐⭐☆

```python
async def smart_support(question: str, context: str) -> str:
    # Classification d'abord (DeepSeek = moins cher)
    category = await classify_question(question)  # DeepSeek
    
    if category == "faq_simple":
        # Questions simples = DeepSeek (100x moins cher)
        return await deepseek_answer(question, FAQ_CONTEXT)
    
    elif category == "technique_complexe":
        # Questions techniques = Claude (meilleur raisonnement)
        try:
            return await claude_answer(question, context)
        except RateLimitError:
            # Fallback sur GPT si Claude rate limited
            return await gpt_answer(question, context)
    
    elif category == "recherche_actuelle":
        # Infos temps réel = Gemini (Google Search natif)
        return await gemini_answer(question)
```

---

## Niveau 5 — Usine agentique

### CAS-040 : Système de veille + traitement + publication automatique
**Objectif** : Veille continue → tri → enrichissement → publication Notion/Blog
**Stack** : n8n (orchestration) + Claude (analyse) + Gemini (recherche) + Notion API
**Coût** : 💚 Gratuit (n8n self-hosted)
**Difficulté** : ⭐⭐⭐⭐⭐

**n8n self-hosted :**
```bash
docker run -d \
  -p 5678:5678 \
  -v ~/.n8n:/home/node/.n8n \
  n8nio/n8n
# → http://localhost:5678
```

---

### CAS-041 : Agent de développement autonome (style Devin)
**Objectif** : Donner une spec → l'agent code, teste, commit
**Stack** : Claude Code (principal) + github-mcp + git-mcp
**Coût** : 💛 Claude Pro/API
**Difficulté** : ⭐⭐⭐⭐⭐

---

### CAS-042 : Monitoring et auto-correction d'agents en prod
**Objectif** : Les agents se surveillent mutuellement, alertent et se corrigent
**Stack** : LangGraph + Langfuse (self-hosted) + Claude
**Coût** : 💚 Gratuit (self-hosted)
**Difficulté** : ⭐⭐⭐⭐⭐

---

## Quick wins : tester en 5 minutes

Ces cas se testent directement dans Claude.ai sans setup :

1. **"Cherche les 5 derniers articles sur LangGraph et résume-les"** → Web search agent
2. **Upload un CSV → "Analyse ces données, trouve les anomalies"** → Data agent
3. **"Écris un script Python qui... teste-le... corrige les erreurs"** → Code agent
4. **"Planifie l'architecture d'un système de veille automatique"** → Planning agent

---

## Projets GitHub à héberger soi-même

| Projet | Usage | Stars | Install |
|--------|-------|-------|---------|
| **Flowise** | RAG + agents visuels | 35K | `npm install -g flowise` |
| **n8n** | Orchestration no-code | 50K | Docker |
| **Open WebUI** | Interface LLM universelle | 45K | Docker |
| **LangFuse** | Monitoring agents | 12K | Docker compose |
| **Dify** | Platform agents | 40K | Docker compose |
| **AnythingLLM** | RAG local | 25K | Docker |
| **Evo AI** | Multi-agent platform | 1.2K | Docker |

---

## Niveau 3-5 — Cas n8n spécifiques

### CAS-N01 : Veille IA quotidienne automatique
**Stack** : n8n self-hosted + Brave Search + Claude API + Slack
**Coût** : 💚 ~€0.01/jour | **Difficulté** : ⭐⭐☆☆☆
→ Détail complet dans `n8n-workflow-automation.md` section CAS-N01

### CAS-N02 : Chatbot support avec escalade humaine
**Stack** : n8n + Slack + Claude AI Agent + Postgres
**Coût** : 💛 Variable | **Difficulté** : ⭐⭐⭐☆☆
→ Détail complet dans `n8n-workflow-automation.md` section CAS-N02

### CAS-N03 : Pipeline contenu multi-LLM (DeepSeek + Claude)
**Stack** : n8n + DeepSeek (draft) + Claude (révision) + Notion
**Coût** : 💛 ~€2/semaine pour 50 articles | **Difficulté** : ⭐⭐⭐☆☆
→ Détail complet dans `n8n-workflow-automation.md` section CAS-N03

### CAS-N04 : Monitoring API avec analyse IA des erreurs
**Stack** : n8n + Claude + Slack/PagerDuty
**Coût** : 💚 Quasi gratuit | **Difficulté** : ⭐⭐☆☆☆
→ Détail complet dans `n8n-workflow-automation.md` section CAS-N04

### CAS-N05 : Claude pilote n8n via MCP
**Stack** : Claude Code + n8n-mcp server + n8n self-hosted
**Coût** : 💚 Gratuit (open source) | **Difficulté** : ⭐⭐⭐☆☆
**GitHub** : https://github.com/czlonkowski/n8n-mcp
→ Détail complet dans `n8n-workflow-automation.md` section 5.1
