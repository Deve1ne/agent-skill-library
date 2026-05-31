# Catalogue MCP Servers Utiles — Mai 2026

> MCP = Model Context Protocol. Ces serveurs permettent à Claude (et autres LLMs compatibles) d'interagir avec des outils externes.

---

## Comment utiliser un MCP server

### Dans Claude.ai
→ Menu "Tools" / "MCP Apps" → Connect → Utiliser directement en conversation

### Dans Claude Code / API
```json
// .claude/settings.json
{
  "mcpServers": {
    "nom-serveur": {
      "command": "npx",
      "args": ["-y", "@nom/package-mcp"],
      "env": {"API_KEY": "..."}
    }
  }
}
```

---

## Bases de données

### 💚 postgres-mcp
- **Usage** : Interroger/écrire dans PostgreSQL
- **Install** : `npx -y @modelcontextprotocol/server-postgres`
- **Config** : `{"connectionString": "postgresql://user:pass@localhost/db"}`
- **GitHub** : https://github.com/modelcontextprotocol/servers/tree/main/src/postgres
- **Coût** : Gratuit (open source)
- **Compatible** : Claude, GPT, Gemini (MCP client)

### 💚 sqlite-mcp
- **Usage** : Base de données locale SQLite
- **Install** : `npx -y @modelcontextprotocol/server-sqlite`
- **Idéal pour** : prototypes, données locales
- **Coût** : Gratuit

### 💚 mysql-mcp  
- **GitHub** : https://github.com/benborla/mcp-server-mysql
- **Coût** : Gratuit

---

## Fichiers et filesystem

### 💚 filesystem-mcp
- **Usage** : Lire/écrire/lister fichiers locaux
- **Install** : `npx -y @modelcontextprotocol/server-filesystem`
- **⚠️ Sécurité** : Limiter le path aux dossiers autorisés
- **Coût** : Gratuit

### 💚 google-drive-mcp
- **Usage** : Lire/écrire Google Drive
- **Install** : `npx -y @modelcontextprotocol/server-gdrive`
- **Coût** : Gratuit (nécessite compte Google)

---

## Recherche et web

### 💚 brave-search-mcp
- **Usage** : Recherche web via Brave Search API
- **Install** : `npx -y @modelcontextprotocol/server-brave-search`
- **Free tier** : 2000 queries/mois gratuit
- **Coût** : 💛 Freemium

### 💚 fetch-mcp
- **Usage** : Fetch n'importe quelle URL, scraping
- **Install** : `npx -y @modelcontextprotocol/server-fetch`
- **Coût** : Gratuit

### 💚 puppeteer-mcp
- **Usage** : Navigateur headless, screenshots, interaction web
- **Install** : `npx -y @modelcontextprotocol/server-puppeteer`
- **Coût** : Gratuit
- **Use case** : automatisation de sites sans API

---

## Git et code

### 💚 git-mcp
- **Usage** : Opérations Git (commit, diff, log, branch...)
- **Install** : `npx -y @modelcontextprotocol/server-git`
- **Coût** : Gratuit

### 💚 github-mcp
- **Usage** : Gestion repos GitHub, issues, PRs
- **Install** : `npx -y @modelcontextprotocol/server-github`
- **Nécessite** : GitHub token
- **Coût** : Gratuit (API GitHub)

---

## Communication et productivité

### 💚 slack-mcp
- **Usage** : Envoyer messages, lire canaux Slack
- **Install** : `npx -y @modelcontextprotocol/server-slack`
- **Coût** : Gratuit (nécessite workspace Slack)

### 💛 gmail-mcp
- **Usage** : Lire/envoyer emails Gmail
- **GitHub** : https://github.com/modelcontextprotocol/servers/tree/main/src/gmail
- **Coût** : Gratuit (quota Google API)

---

## APIs et intégrations

### 💚 zapier-mcp (★ Nouveau 2026)
- **Usage** : Accès à 7000+ apps via Zapier
- **Install** : Via Claude.ai → Apps → Zapier
- **Coût** : 💛 Freemium (Zapier free = limité)
- **Use case** : Connecter Claude à presque n'importe quoi

### 💚 make-mcp (ancien Integromat)
- **Usage** : Automatisation via Make.com
- **Coût** : 💛 Freemium

---

## Monitoring et observabilité agents

### 💚 langfuse (auto-hébergeable)
- **Usage** : Tracer, monitorer, évaluer les appels LLM
- **GitHub** : https://github.com/langfuse/langfuse
- **Install** : `docker compose up` (self-hosted)
- **Coût** : 💚 Gratuit self-hosted / 💛 Cloud freemium
- **Indispensable** en production

### 💚 langsmith (LangChain)
- **Usage** : Debug et monitoring pipelines LangChain/LangGraph
- **URL** : https://smith.langchain.com
- **Coût** : 💛 Freemium (free tier généreux)

---

## Créer son propre MCP server

### Template minimal Python

```python
# my_mcp_server.py
from mcp.server import Server
from mcp.server.stdio import stdio_server
from mcp.types import Tool, TextContent
import asyncio

app = Server("mon-serveur-custom")

@app.list_tools()
async def list_tools():
    return [
        Tool(
            name="ma_fonction",
            description="Décrit ce que fait ma fonction",
            inputSchema={
                "type": "object",
                "properties": {
                    "parametre": {"type": "string", "description": "Description"}
                },
                "required": ["parametre"]
            }
        )
    ]

@app.call_tool()
async def call_tool(name: str, arguments: dict):
    if name == "ma_fonction":
        result = f"Résultat pour : {arguments['parametre']}"
        return [TextContent(type="text", text=result)]

async def main():
    async with stdio_server() as (read_stream, write_stream):
        await app.run(read_stream, write_stream, app.create_initialization_options())

if __name__ == "__main__":
    asyncio.run(main())
```

```bash
# Installation SDK
pip install mcp

# Lancer le serveur
python my_mcp_server.py
```

### Template minimal Node.js

```typescript
import { Server } from "@modelcontextprotocol/sdk/server/index.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";

const server = new Server({ name: "mon-serveur", version: "1.0.0" });

server.setRequestHandler("tools/list", async () => ({
  tools: [{
    name: "ma_fonction",
    description: "Ce que fait ma fonction",
    inputSchema: { type: "object", properties: { query: { type: "string" } } }
  }]
}));

server.setRequestHandler("tools/call", async (request) => {
  const { name, arguments: args } = request.params;
  if (name === "ma_fonction") {
    return { content: [{ type: "text", text: `Résultat: ${args.query}` }] };
  }
});

const transport = new StdioServerTransport();
await server.connect(transport);
```

---

## Catalogue de cas d'usage → MCP recommandé

| Je veux... | MCP à utiliser | Gratuit ? |
|-----------|---------------|-----------|
| Lire ma DB PostgreSQL | postgres-mcp | 💚 |
| Scraper un site web | puppeteer-mcp ou fetch-mcp | 💚 |
| Gérer des fichiers locaux | filesystem-mcp | 💚 |
| Chercher sur le web | brave-search-mcp | 💛 |
| Envoyer des Slack | slack-mcp | 💚 |
| Commiter du code | git-mcp | 💚 |
| Gérer des issues GitHub | github-mcp | 💚 |
| Connecter à n'importe quelle app | zapier-mcp | 💛 |
| Monitorer mes agents | langfuse self-hosted | 💚 |
