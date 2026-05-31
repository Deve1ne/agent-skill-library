# Capacités Agentiques par IA — Mai 2026

> Mis à jour : mai 2026. Le skill doit web_search pour vérifier avant tout tutoriel payant.

---

## Tableau comparatif global

| Capacité | Claude (Sonnet/Opus 4.6) | ChatGPT (GPT-5.x) | Gemini (2.0 Pro) | DeepSeek (V4/R2) | Grok (3) | Le Chat (Mistral) |
|----------|--------------------------|-------------------|------------------|------------------|----------|-------------------|
| **Tool use / Function calling** | ✅ Excellent | ✅ Excellent | ✅ Excellent | ✅ Très bon | ✅ Bon | ✅ Bon |
| **MCP client natif** | ✅ Oui (Claude.ai + API) | ✅ Oui (API) | ✅ Oui (API) | ⚠️ Partiel | ❌ Non | ⚠️ En cours |
| **Agent teams intégré** | ✅ Oui (Opus 4.6) | ⚠️ Via Swarm/SDK | ❌ Non natif | ❌ Non | ❌ Non | ❌ Non |
| **Code execution** | ✅ Claude.ai + API | ✅ ChatGPT + API | ✅ Gemini + API | ✅ API | ⚠️ Limité | ⚠️ Limité |
| **Web search natif** | ✅ Claude.ai | ✅ ChatGPT | ✅ Gemini (meilleur) | ✅ Oui | ✅ Excellent | ✅ Oui |
| **Context window** | 200K tokens | 1M tokens | 1M tokens | 1M tokens | 128K | 128K |
| **Computer use** | ✅ API (beta) | ✅ API | ✅ API | ❌ | ❌ | ❌ |
| **Auto-hébergeable** | ❌ Non | ❌ Non | ❌ Non | ✅ Oui (open weights) | ❌ Non | ✅ Oui (open weights) |
| **Raisonnement long** | ✅ Excellent | ✅ Excellent | ✅ Très bon | ✅ Très bon | ✅ Bon | ✅ Bon |

---

## Tarifs API (mai 2026, vérifier avant usage)

| Modèle | Input ($/1M tokens) | Output ($/1M tokens) | Free tier |
|--------|--------------------|--------------------|-----------|
| Claude Sonnet 4.6 | ~$3 | ~$15 | Via claude.ai |
| Claude Opus 4.6 | ~$15 | ~$75 | Non |
| GPT-5.x Thinking | ~$15 | ~$60 | Via chatgpt.com |
| Gemini 2.0 Pro | ~$2 | ~$8 | Via gemini.google.com |
| DeepSeek V4 | ~$0.14 | ~$0.28 | Oui (API) |
| Grok 3 | ~$5 | ~$15 | Via x.com |
| Mistral Large | ~$3 | ~$9 | Via le chat |

> 💡 **DeepSeek = meilleur rapport qualité/prix pour usage API intensif**
> 💡 **Gemini = meilleur pour recherche temps réel via Google**
> 💡 **Claude = meilleur pour raisonnement complexe et code**

---

## Capacités agentiques détaillées

### Claude (Anthropic)
**Points forts agentiques :**
- 🥇 Meilleur pour code complexe et raisonnement multi-étapes
- Agent teams natif dans Opus 4.6 (orchestration d'agents)
- MCP client intégré dans Claude.ai (connexion directe aux outils)
- Computer use via API (contrôle navigateur/desktop)
- Claude Code : environnement agentique terminal complet

**Limites :**
- Non auto-hébergeable (cloud only)
- Coût élevé pour Opus
- Rate limits sur free tier

**Pour le mode agentique :**
```python
# Appel API avec tools
client.messages.create(
    model="claude-sonnet-4-6",
    tools=[{"name": "web_search", ...}, {"name": "code_execution", ...}],
    messages=[...]
)
```

---

### ChatGPT / GPT-5.x (OpenAI)
**Points forts agentiques :**
- Agents SDK (mars 2025) : framework officiel multi-agents
- Computer use natif (contrôle applications desktop)
- Custom GPTs avec actions
- Vaste écosystème de plugins

**Limites :**
- Plus cher (Thinking mode)
- Moins précis sur instructions longues que Claude

**Pour le mode agentique :**
```python
from openai import OpenAI
# Agents SDK
from agents import Agent, Runner
```

---

### Gemini 2.0 Pro (Google)
**Points forts agentiques :**
- 🥇 Meilleur accès temps réel (Google Search intégré)
- ADK (Agent Development Kit) — avr. 2025
- Intégration Workspace (Docs, Sheets, Gmail)
- Multimodal natif
- 1M context window

**Limites :**
- Moins précis sur raisonnement pur
- ADK moins mature que LangGraph

**Pour le mode agentique :**
```python
from google.adk.agents import Agent
```

---

### DeepSeek V4 / R2 (open source)
**Points forts agentiques :**
- 🥇 Meilleur rapport qualité/prix (10-100x moins cher)
- Open weights → auto-hébergeable avec Ollama
- Excellent en raisonnement math/code
- Utilisable localement (confidentialité totale)

**Limites :**
- Pas d'interface agent packagée
- Données sensibles : cloud uniquement si API DeepSeek (serveurs CN)
- Moins bon en créatif/nuances

**Auto-hébergement :**
```bash
# Avec Ollama (gratuit, local)
ollama pull deepseek-r1:70b
ollama serve
```

---

### Grok 3 (xAI)
**Points forts agentiques :**
- Accès temps réel Twitter/X (unique)
- Bonne recherche web
- Raisonnement amélioré

**Limites :**
- Écosystème agentique limité
- Pas de MCP natif
- Moins de tooling disponible

---

### Le Chat (Mistral)
**Points forts agentiques :**
- Modèles open source (Mistral, Mixtral)
- Hébergeable localement
- Bon pour Europe (conformité RGPD)
- Interface API simple

**Limites :**
- Moins puissant que Claude/GPT sur tâches complexes
- Écosystème agent moins développé

---

## Matrice de décision : "Quelle IA pour quel cas ?"

| Besoin | IA recommandée | Pourquoi |
|--------|---------------|---------|
| Code complexe, refactoring | Claude | Meilleur contexte codebase |
| Recherche web temps réel | Gemini | Google Search natif |
| Coût faible, volume élevé | DeepSeek | 100x moins cher |
| Données locales/privées | DeepSeek auto-hébergé | Open weights |
| Multi-agent orchestration | Claude + LangGraph | Agent teams + tooling |
| Analyse Twitter/X | Grok | Accès temps réel |
| Conformité RGPD Europe | Mistral / Le Chat | Hébergement EU |
| Pipeline créatif | Claude ou GPT | Meilleure créativité |
| Copilot Microsoft 365 | Copilot (GPT-4o) | Intégration native |

---

## Matrice "Peut faire ça ?" par IA (mode agentique)

| Action | Claude.ai | ChatGPT | Gemini | DeepSeek API | Copilot |
|--------|-----------|---------|--------|--------------|---------|
| Lire un fichier CSV | ✅ | ✅ | ✅ | ✅ | ✅ |
| Écrire du code + l'exécuter | ✅ | ✅ | ✅ | ✅ | ✅ |
| Naviguer sur le web | ✅ | ✅ | ✅ | ⚠️ | ✅ |
| Se connecter à une DB | ✅ MCP | ✅ Tools | ✅ Tools | ✅ Manual | ✅ |
| Contrôler un navigateur | ✅ API | ✅ API | ✅ API | ❌ | ❌ |
| Déléguer à un sous-agent | ✅ Natif | ✅ SDK | ⚠️ ADK | ❌ | ❌ |
| Lire emails/calendar | ✅ MCP | ✅ Plugins | ✅ Workspace | ❌ | ✅ M365 |
| Envoyer des messages | ✅ MCP | ✅ Actions | ✅ Workspace | ❌ | ✅ Teams |
