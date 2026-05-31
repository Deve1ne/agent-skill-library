# n8n & Automatisation de Workflows — Guide Complet

> Version mai 2026. Basé sur n8n 2.x avec native LangChain + 70+ nœuds IA.

---

## Table des matières
1. [Concepts fondamentaux](#1-concepts-fondamentaux)
2. [Installation et setup](#2-installation-et-setup)
3. [Nœuds essentiels](#3-nœuds-essentiels)
4. [Patterns de workflows agentiques](#4-patterns-de-workflows-agentiques)
5. [n8n + Claude (MCP + API)](#5-n8n--claude-mcp--api)
6. [Cas concrets pas à pas](#6-cas-concrets-pas-à-pas)
7. [Comparatif n8n / Make / Zapier](#7-comparatif-n8n--make--zapier)
8. [Erreurs classiques et debugging](#8-erreurs-classiques-et-debugging)

---

## 1. Concepts fondamentaux

### Les 4 éléments de base

```
WORKFLOW = canvas d'automatisation
    └── TRIGGER (déclencheur — 1 seul par workflow)
          └── NODE → NODE → NODE ... (chaîne d'actions)
                                        └── OUTPUT
```

**Trigger** : ce qui démarre le workflow. Deux familles :
- **Webhook trigger** : attend un appel HTTP entrant (formulaire, Stripe, GitHub push...)
- **App/Schedule trigger** : écoute un événement app (nouveau mail, cron, nouveau tweet...)

**Node** : une étape. Chaque node fait UNE chose : appeler une API, transformer des données, brancher la logique, appeler un LLM.

**Execution** : une instance de run du workflow. C'est l'unité de facturation sur n8n Cloud.

**Items** : n8n passe les données sous forme de tableaux d'items JSON entre les nodes. Chaque node reçoit `[{json: {...}}, {json: {...}}]` et produit un tableau.

### Le modèle de données n8n

```json
// Ce que reçoit chaque node en entrée
[
  { "json": { "email": "alice@ex.com", "nom": "Alice" } },
  { "json": { "email": "bob@ex.com",   "nom": "Bob"   } }
]
```

Accès aux données dans les expressions : `{{ $json.email }}` ou `{{ $('NomDuNode').item.json.champ }}`

---

## 2. Installation et setup

### Option A — Self-hosted Docker (recommandé dev) 💚 Gratuit

```bash
# Démarrage rapide (données en mémoire, ok pour test)
docker run -d \
  --name n8n \
  -p 5678:5678 \
  -v ~/.n8n:/home/node/.n8n \
  n8nio/n8n

# Avec PostgreSQL (recommandé prod)
# docker-compose.yml :
```

```yaml
version: '3.8'
services:
  n8n:
    image: n8nio/n8n
    ports: ["5678:5678"]
    environment:
      - DB_TYPE=postgresdb
      - DB_POSTGRESDB_HOST=postgres
      - DB_POSTGRESDB_DATABASE=n8n
      - DB_POSTGRESDB_USER=n8n
      - DB_POSTGRESDB_PASSWORD=n8n_password
      - N8N_ENCRYPTION_KEY=ton_secret_32_chars
    volumes: [~/.n8n:/home/node/.n8n]
    depends_on: [postgres]

  postgres:
    image: postgres:15
    environment:
      POSTGRES_DB: n8n
      POSTGRES_USER: n8n
      POSTGRES_PASSWORD: n8n_password
    volumes: [postgres_data:/var/lib/postgresql/data]

volumes:
  postgres_data:
```

```bash
docker compose up -d
# Interface : http://localhost:5678
```

### Option B — Cloud n8n 💛 Freemium
- Starter : €24/mois → 2 500 exécutions
- Pro : €60/mois → 10 000 exécutions
- Self-hosted Community : **gratuit, exécutions illimitées**

### Option C — npm (dev local)
```bash
npm install -g n8n
n8n start
```

---

## 3. Nœuds essentiels

### Triggers (déclencheurs)

| Nœud | Usage | Quand l'utiliser |
|------|-------|-----------------|
| **Webhook** | HTTP POST/GET entrant | Formulaire, API externe, GitHub webhook |
| **Schedule** | Cron / interval | Veille quotidienne, sync périodique |
| **Email Trigger (IMAP)** | Nouveau mail | Support, factures, alertes |
| **HTTP Request** | (en polling) | APIs sans webhook natif |

### Actions les plus utilisées

| Nœud | Usage | Notes |
|------|-------|-------|
| **HTTP Request** | Appeler n'importe quelle API REST | Le couteau suisse |
| **Code** | JavaScript/Python custom | Logique complexe, transformations |
| **Set** | Créer/modifier des champs | Préparer les données |
| **IF** | Branchement conditionnel | Routage selon valeur |
| **Switch** | Branchement multiple | Remplace IF imbriqués |
| **Loop Over Items** | Itérer sur une liste | Traiter N éléments |
| **Merge** | Combiner deux branches | Rejoindre des flux parallèles |
| **Wait** | Pause / attente | Rate limiting, délais intentionnels |

### Nœuds IA (n8n 2.x — 70+ nœuds) ⭐

| Nœud | Usage |
|------|-------|
| **AI Agent** | Agent autonome avec tools et mémoire |
| **Chat Model (Claude/GPT/Gemini/Ollama)** | Appel LLM direct |
| **Text Classifier** | Classer du texte sans prompt complexe |
| **Information Extractor** | Extraire des données structurées d'un texte |
| **Summarization Chain** | Résumer de longs documents |
| **Vector Store (Pinecone/Chroma/Supabase)** | Stockage pour RAG |
| **Embeddings** | Transformer texte en vecteurs |
| **Question & Answer** | RAG sur documents |
| **Memory (Buffer/Summary/Window)** | Mémoire persistante d'agent |

### Nœuds MCP (nouveauté 2025)

| Nœud | Usage |
|------|-------|
| **MCP Server Trigger** | Exposer le workflow comme outil MCP |
| **MCP Client Tool** | Appeler un MCP server externe depuis n8n |

---

## 4. Patterns de workflows agentiques

### Pattern A — Workflow séquentiel classique

```
[Schedule/Webhook] → [Fetch Data] → [Transform] → [AI Analysis] → [Store/Send]
```

**Exemple** : Veille news quotidienne
```
[Schedule 8h] → [HTTP Request: NewsAPI] → [Loop Items] → [Claude: résumer] → [Airtable: stocker] → [Slack: envoyer digest]
```

---

### Pattern B — Agent IA avec tools (le plus puissant)

```
[Trigger] → [AI Agent Node]
                ├── Tool: HTTP Request (chercher)
                ├── Tool: Code (calculer)
                ├── Tool: Postgres (lire DB)
                └── Tool: Slack (envoyer)
```

Le nœud **AI Agent** donne au LLM l'accès aux tools et décide lui-même quels appeler et dans quel ordre. C'est le cœur de l'agentique dans n8n.

```yaml
# Configuration du nœud AI Agent
Chat Model: Claude Sonnet 4.6 (ou GPT-4o, Gemini, Ollama local)
System Message: |
  Tu es un assistant qui répond aux questions sur notre base de données clients.
  Utilise l'outil postgres_query pour interroger la DB.
  Utilise l'outil send_slack si l'utilisateur demande une notification.
Memory: Window Buffer Memory (derniers 10 messages)
Tools:
  - postgres_query (nœud Postgres connecté)
  - send_slack (nœud Slack connecté)
  - web_search (nœud HTTP vers Brave API)
```

---

### Pattern C — Routage intelligent (Router Pattern)

```
[Trigger] → [AI Classifier] → IF catégorie = "urgent" → [Branch A]
                              IF catégorie = "info"   → [Branch B]
                              IF catégorie = "spam"   → [Branch C]
```

**Implémentation concrète** :
```
[Email Trigger (IMAP)]
    ↓
[Claude: classify] — prompt: "Classifie ce mail: urgent/question/spam. JSON: {category: string}"
    ↓
[Switch node sur {{ $json.category }}]
    ├── urgent → [Créer ticket Jira] → [SMS alerte]
    ├── question → [Claude: répondre] → [Send Email]
    └── spam → [Marquer lu] → [Fin]
```

---

### Pattern D — Pipeline multi-LLM (économique)

```
[Trigger]
    ↓
[DeepSeek: draft cheap]  ← 100x moins cher pour première passe
    ↓
[Claude: review & polish] ← seulement pour la finition
    ↓
[Output]
```

**Config dans n8n** :
- Node 1 : "OpenAI" pointé vers DeepSeek (compatible OpenAI) : `base_url: https://api.deepseek.com`
- Node 2 : "Anthropic Claude" pour la révision

---

### Pattern E — Human-in-the-Loop

```
[Agent fait une action] → [Wait node] → [Webhook de confirmation] → [Continuer ou annuler]
```

Le nœud **Wait** interrompt le workflow jusqu'à réception d'une URL de reprise. Parfait pour :
- Validation humaine avant envoi email de masse
- Approbation avant modification en base
- Vérification avant déploiement

```
[Agent génère contenu]
    ↓
[Send Slack: "Contenu prêt, valider ? [OUI](url_resume) [NON](url_cancel)"]
    ↓
[Wait: jusqu'à 24h]
    ↓
[IF: approuvé ?]
    ├── OUI → [Publier]
    └── NON → [Archiver + notifier]
```

---

### Pattern F — Sub-workflows (Modularité)

Décomposer en workflows réutilisables appelés depuis d'autres workflows.

```
[Main Workflow]
    ↓
[Execute Workflow: "Send Notification"] ← sous-workflow réutilisable
    ↓
[Execute Workflow: "Log to DB"]
    ↓
[Execute Workflow: "Update CRM"]
```

**Avantages** :
- DRY (Don't Repeat Yourself) sur les workflows
- Testable indépendamment
- Modifiable sans toucher au workflow principal

---

## 5. n8n + Claude (MCP + API)

### 5.1 — Claude appelle n8n via MCP (sens Claude → n8n)

Claude utilise n8n comme "bras exécutant" via le protocole MCP.

**Setup** : Exposer un workflow n8n comme outil MCP

```
Dans n8n :
1. Créer un workflow
2. Ajouter nœud "MCP Server Trigger" comme déclencheur
3. Configurer : Tool Name, Description, Input Schema (JSON Schema)
4. Construire la logique du workflow
5. Activer le workflow

Dans Claude Code (.claude/settings.json) :
{
  "mcpServers": {
    "n8n": {
      "command": "npx",
      "args": ["-y", "n8n-mcp"],
      "env": {
        "N8N_BASE_URL": "http://localhost:5678",
        "N8N_API_KEY": "ton_api_key_n8n"
      }
    }
  }
}
```

**Résultat** : Claude peut dire "lance le workflow de veille pour le topic X" et n8n l'exécute.

**Projet GitHub** : https://github.com/czlonkowski/n8n-mcp 💚 MIT

---

### 5.2 — n8n appelle Claude (sens n8n → Claude)

n8n orchestre un workflow dans lequel Claude est un des nœuds.

```yaml
# Dans n8n, nœud "Anthropic Chat Model"
API Key: {{ $env.ANTHROPIC_API_KEY }}
Model: claude-sonnet-4-6
Max Tokens: 1000
```

Ou en HTTP Request direct :
```json
{
  "url": "https://api.anthropic.com/v1/messages",
  "method": "POST",
  "headers": {
    "x-api-key": "{{ $env.ANTHROPIC_API_KEY }}",
    "anthropic-version": "2023-06-01",
    "content-type": "application/json"
  },
  "body": {
    "model": "claude-sonnet-4-6",
    "max_tokens": 1024,
    "messages": [{"role": "user", "content": "{{ $json.prompt }}"}]
  }
}
```

---

### 5.3 — Architecture hybride Claude + n8n (le meilleur des deux)

```
Claude (intelligence, raisonnement)
    ↕ MCP
n8n (orchestration, intégrations, scheduling)
    ↕ connecteurs natifs
400+ services (Slack, Notion, GitHub, Postgres, Gmail...)
```

**Exemple concret** :
- Claude.ai reçoit une demande : "Analyse les tickets de support de cette semaine"
- Claude appelle via MCP le workflow n8n "fetch-support-tickets"
- n8n interroge Zendesk, formate les données, les renvoie
- Claude analyse et produit le rapport
- Claude appelle via MCP le workflow n8n "send-report-to-slack"
- n8n envoie dans le bon canal

---

## 6. Cas concrets pas à pas

### CAS-N01 : Agent de veille IA quotidienne 💚 Gratuit

**Objectif** : Chaque matin, scraper les news IA et envoyer un digest Slack
**Stack** : n8n self-hosted + Brave Search API (2000 req/mois gratuit) + Claude API + Slack
**Coût** : ~€0.01/jour (tokens Claude)

**Workflow** :
```
[Schedule: 7h30 chaque jour]
    ↓
[HTTP Request: Brave Search]
  URL: https://api.search.brave.com/res/v1/news/search
  Params: q="intelligence artificielle", freshness=pd (last day)
  Header: X-Subscription-Token: {{ $env.BRAVE_API_KEY }}
    ↓
[Code node: extraire titres + URLs]
  const articles = $input.all().map(item => ({
    titre: item.json.title,
    url: item.json.url,
    extrait: item.json.description
  }));
  return [{json: {articles: articles.slice(0, 10)}}];
    ↓
[Anthropic: analyser]
  Prompt: "Voici 10 news IA. Sélectionne les 5 plus pertinentes pour un dev,
  résume en 2 lignes chacune, indique l'impact (haut/moyen/bas).
  Articles: {{ $json.articles }}"
    ↓
[Slack: envoyer]
  Channel: #veille-ia
  Message: "🤖 Veille IA du {{ $now.format('DD/MM') }}\n{{ $json.message }}"
```

---

### CAS-N02 : Chatbot support avec escalade humaine

**Objectif** : Bot Slack qui répond aux questions, escalade si confiance < 80%
**Stack** : n8n + Claude + Slack
**Coût** : Variable selon volume

**Workflow** :
```
[Slack Event Trigger: message dans #support]
    ↓
[IF: message contient @bot ?]
    ↓ OUI
[AI Agent Node]
  Model: Claude Sonnet
  Tools: [postgres: lire FAQ, HTTP: doc API]
  System: "Réponds aux questions support. Si tu n'es pas sûr (< 80% confiance),
           réponds avec JSON: {response: '...', confidence: 0.X, escalate: true}"
    ↓
[Code: parser la réponse]
  const data = JSON.parse($json.message || '{}');
  return [{json: {
    response: data.response || $json.message,
    escalate: data.escalate || false,
    confidence: data.confidence || 1.0
  }}];
    ↓
[IF: escalate = true]
  ├── OUI → [Slack: notifier @support-humain] → [Wait: réponse humaine]
  └── NON → [Slack: répondre directement]
```

---

### CAS-N03 : Pipeline de contenu multi-LLM économique

**Objectif** : Générer 50 articles/semaine à coût minimal
**Stack** : n8n + DeepSeek (draft) + Claude (révision) + Notion
**Coût** : ~€2/semaine (vs €20 avec Claude seul)

**Workflow** :
```
[Schedule: lundi 9h]
    ↓
[Notion: lire liste de sujets à traiter]
    ↓
[Loop Over Items: pour chaque sujet]
    ↓
[HTTP Request → DeepSeek API] ← Draft initial (0.14$/M tokens)
  body.model: "deepseek-chat"
  body.messages: [{role:"user", content:"Rédige un article de 800 mots sur: {{ $json.sujet }}"}]
    ↓
[Anthropic Claude] ← Révision style et qualité
  Prompt: "Révise et améliore cet article. Garde le fond, améliore le style.
  Article: {{ $json.draft }}"
    ↓
[Notion: créer page avec contenu]
    ↓
[Wait: 2 secondes] ← rate limiting
```

---

### CAS-N04 : Agent de monitoring et alertes intelligentes

**Objectif** : Monitorer une API, alerter avec analyse d'impact si erreur
**Stack** : n8n + Claude + PagerDuty/Slack
**Coût** : 💚 Quasi gratuit

```
[Schedule: toutes les 5 minutes]
    ↓
[HTTP Request: ping API]
    ↓
[IF: status != 200]
    ↓ ERREUR
[Claude: analyser l'erreur]
  Prompt: "Erreur HTTP {{ $json.status }} sur {{ $json.url }}.
  Headers reçus: {{ $json.headers }}.
  Corps: {{ $json.body }}.
  Quel est le problème probable ? Impact estimé ? Actions immédiates ?"
    ↓
[Slack: alerte #ops]
  "🚨 API DOWN\nAnalyse: {{ $json.analysis }}\nURL: {{ $json.url }}"
    ↓
[Notion: créer incident] + [PagerDuty: créer alert si 3 échecs consécutifs]
```

---

## 7. Comparatif n8n / Make / Zapier

### Vue d'ensemble (mai 2026)

| Critère | n8n | Make.com | Zapier |
|---------|-----|----------|--------|
| **Public cible** | Dev technique | Dev/non-tech | Non-technique |
| **Courbe d'apprentissage** | Modérée | Faible-modérée | Faible |
| **Auto-hébergeable** | ✅ Oui (Community) | ❌ Non | ❌ Non |
| **Free tier** | 💚 Illimité (self-hosted) | 💛 1000 ops/mois | 💛 100 tasks/mois |
| **Intégrations** | 400+ | 1500+ | 6000-8000+ |
| **Nœuds IA** | 70+ (LangChain natif) | Basiques | Agents (beta) |
| **Multi-agent natif** | ✅ Oui | ⚠️ Limité | ⚠️ Beta |
| **LLM self-hosted (Ollama)** | ✅ | ❌ | ❌ |
| **MCP natif** | ✅ | ❌ | ❌ |
| **RGPD / data locale** | ✅ 100% | ❌ Cloud seul | ❌ Cloud seul |

### Tarifs à l'échelle (10 000 exécutions/mois)

| Plateforme | Coût mensuel | Remarque |
|-----------|-------------|----------|
| n8n self-hosted | ~€10 (VPS) | Exécutions illimitées |
| n8n Cloud Pro | €60 | 10 000 exécutions |
| Make.com | ~€30 | ~80 000 ops (ratio 8:1) |
| Zapier Professional | €80+ | 50 000 tasks (5-step workflows) |

> **Règle de décision** :  
> Dev + data privée + IA avancée → **n8n self-hosted**  
> Équipe mixte + intégrations larges + pas de serveur → **Make.com**  
> Non-tech + vitesse + 6000 apps → **Zapier**  
> Les trois sont complémentaires en entreprise.

### Les "killer features" de chacun

**n8n uniquement** :
- Nœud AI Agent avec mémoire persistante
- Support LangChain, RAG, vector stores
- MCP Server Trigger (exposer comme outil IA)
- Ollama (LLM local, 0€)
- Code node JavaScript/Python sans restriction
- Self-hosted = RGPD, pas de limite d'exécutions

**Make.com uniquement** :
- Interface visuelle la plus intuitive
- Meilleur rapport ops/€ pour workflows complexes
- Transformations de données avancées (native)

**Zapier uniquement** :
- 6000-8000+ intégrations (le plus large)
- Zapier Agents (IA autonome multi-app)
- AI Copilot (build en langage naturel)

---

## 8. Erreurs classiques et debugging

### Erreurs fréquentes n8n

**1. "Always branch" vs branche spécifique dans IF**
```
IF node → "true" branch exécute si condition vraie
         "false" branch exécute si condition fausse
⚠️ Par défaut les données ne passent QUE dans la branche exécutée.
Pour merger après IF, utiliser le nœud Merge.
```

**2. Loop qui ne passe pas les données au node suivant**
```
Loop Over Items → le dernier nœud de la loop doit retourner les items
Si tu utilises un nœud Code à l'intérieur : toujours return [{json: ...}]
```

**3. Workflow actif vs test**
```
"Test workflow" → run unique avec données de test
"Active" toggle → répond aux vrais triggers
⚠️ Un workflow non activé n'exécute pas ses webhooks/schedules en prod.
```

**4. Rate limits API**
```
Ajouter un nœud "Wait" (1-2 secondes) dans les boucles intensives.
Pour Claude API : max 1000 req/min sur la plupart des plans.
```

**5. Données trop larges pour un nœud LLM**
```
Si tu passes un tableau de 100 articles à Claude → token limit dépassée.
Solution : traiter item par item avec Loop, ou tronquer/résumer d'abord.
```

### Debugging pratique

```
1. Utiliser "Execute step by step" pour voir les données entre chaque nœud
2. Nœud "Set" pour inspecter les données à un point donné
3. Consulter "Executions" → historique complet avec inputs/outputs
4. Activer "Save execution data" pour garder l'historique
5. Nœud "Error Trigger" pour capturer et logger toutes les erreurs
```

### Pattern de gestion d'erreurs recommandé

```
[Workflow principal]
    ↓
[Chaque nœud critique]
    → En cas d'erreur → [Error branch]
                            ↓
                        [Slack: alerter]
                        [Notion: logger l'incident]
                        [Stop And Error: arrêter proprement]
```

```yaml
# Au niveau du workflow :
Settings → Error Workflow → pointer vers un workflow "error-handler" global
```
