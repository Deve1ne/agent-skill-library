# Agent Skill Library

Bibliothèque de skills IA pour Claude Code et agents compatibles (Copilot, Cursor, Codex CLI, Gemini CLI).

Chaque fichier `.skill` est une archive autonome contenant les instructions (`SKILL.md`) et les références documentaires associées. Les skills suivent l'**Agent Skills open standard** : ils sont universels et ne dépendent pas d'une plateforme spécifique.

---

## Installation

### Claude Code

```bash
# Copier le fichier .skill dans le répertoire de skills de Claude Code
cp skills/<nom>.skill ~/.claude/skills/
```

Les skills sont détectés automatiquement au prochain démarrage de session.

---

## Skills disponibles

### `agentic-mastery.skill`

**Professeur expert en IA agentique**

Enseigne la conception et l'implémentation de workflows agentiques : agents outillés, orchestration multi-agent, MCP, tool calling, pipelines IA. S'adapte au niveau et à l'environnement (Claude, API, auto-hébergé).

**Déclencheurs** : `agent`, `agentique`, `MCP`, `multi-agent`, `orchestration`, `automatisation IA`, `pipeline IA`, `A2A`, `IA autonome`, `workflow IA`, `tool calling`, `build an AI agent`

**Références incluses** :
- `mcp-servers-catalogue.md` — catalogue des serveurs MCP courants
- `multi-llm-patterns.md` — patterns d'orchestration multi-LLM
- `n8n-workflow-automation.md` — automatisation avec n8n
- `use-cases-library.md` — bibliothèque de cas d'usage agentiques
- `ia-capabilities-2026.md` — état de l'art des capacités IA

---

### `instructeur.skill`

**Professeur IT Dev / Ops / Infra**

Enseigne l'informatique par la pratique : cas concret, étapes claires, résultat visible. Couvre le développement, les opérations et l'infrastructure. S'adapte à l'OS, à l'IDE et au niveau de l'utilisateur.

**Déclencheurs** : `apprends-moi`, `tuto`, `comment faire`, `hands-on`, `étape par étape`, `teach me`, `how to`, `tutorial`, `step by step`, `configure`, `deploy`, `install`

**Références incluses** :
- `commandes-os.md` — commandes système Windows/Linux/macOS
- `templates-dev.md` — templates de projets Dev
- `templates-ops.md` — templates CI/CD et pipelines
- `templates-infra.md` — templates infrastructure et cloud
- `cheatsheet-template.md` — modèle de cheatsheet
- `config-checklist.md` — checklist de configuration
- `erreurs-courantes.md` — erreurs fréquentes et solutions
- `projets-par-domaine.md` — projets pratiques classés par domaine
- `glossaire.md` — glossaire technique

---

### `skill-interviewer.skill`

**Créateur de skills via interview adaptatif**

Crée des skills IA from scratch grâce à un dialogue progressif en 6 questions. Produit un brief validé puis génère le `SKILL.md` final, universel et compatible tous agents.

**Déclencheurs** : `crée un skill`, `nouveau skill`, `je veux un skill qui`, `build a skill`, `create a skill`, `new SKILL.md`, `skill from scratch`

**Références incluses** :
- `questions-guide.md` — guide des 6 questions d'interview
- `description-guide.md` — guide de rédaction des descriptions de skills

---

### `skill-reviewer.skill`

**Auditeur et optimiseur de SKILL.md**

Analyse et améliore les skills existants : qualité de la description, efficacité token, fiabilité du déclenchement, autonomie, boucles d'auto-raffinement. Opère en passes progressives avec questions systématiques.

**Déclencheurs** : `review my skill`, `improve this skill`, `audit my SKILL.md`, `my skill doesn't trigger`, `affiner mon skill`, `réviser mon skill`, `améliorer mon skill`, `is my skill good?`

**Références incluses** :
- `scoring-grid.md` — grille de notation des skills
- `token-budget-guide.md` — guide de gestion du budget token
- `description-patterns.md` — patterns de descriptions efficaces

---

### `wpf-manager.skill`

**Expert WPF desktop**

Analyse de fichiers XAML, insertion et déplacement de contrôles, scaffolding de ViewModel MVVM pur (`INotifyPropertyChanged` manuel, `RelayCommand`). Compatible tous agents LLM.

**Déclencheurs** : `analyse ce xaml`, `ajoute un contrôle`, `déplace cet élément`, `visibilité conditionnelle WPF`, `DataTrigger`, `Visibility Converter`, `binding WPF`, `RelayCommand`, `INotifyPropertyChanged`, `ObservableCollection`, `DataGrid colonnes CellTemplate`

> Ne se déclenche PAS pour : debug d'erreurs génériques, migration Xamarin/MAUI, CommunityToolkit.Mvvm.

**Références incluses** :
- `datagrid-patterns.md` — patterns DataGrid et CellTemplate
- `visibility-patterns.md` — patterns Converter et Visibility conditionnelle

---

## Structure d'un fichier `.skill`

```
nom-du-skill.skill          ← archive ZIP renommée
└── nom-du-skill/
    ├── SKILL.md            ← instructions principales + frontmatter
    └── references/
        ├── doc1.md         ← documents de référence
        └── doc2.md
```

Le frontmatter YAML du `SKILL.md` définit le `name`, la `description` (utilisée pour le déclenchement automatique) et les `allowed-tools`.

---

## Contribuer

Pour créer un nouveau skill, utiliser le skill `skill-interviewer` qui guide la création via un interview adaptatif, puis soumettre le fichier `.skill` généré.

Pour améliorer un skill existant, utiliser `skill-reviewer` pour obtenir un audit structuré avant de proposer une nouvelle version.

---

## Licence

MIT
