# Description Patterns — Bibliothèque testée

Source : awesome-claude-skills (11k+ stars GitHub), mellanon gist (200+ prompts testés), Anthropic skill-creator officiel.

---

## Pattern 1 : Two-Part (taux de déclenchement : ~50%)

```yaml
description: [CE QUE ÇA FAIT]. Use when [QUAND — phrases concrètes].
```

**Exemple :**
```yaml
description: Extracts text and tables from PDF files, fills forms, merges documents. 
  Use when working with PDF files or when the user mentions PDFs, forms, or document extraction.
```

---

## Pattern 2 : Pushy (recommandé Anthropic — taux : ~72%)

```yaml
description: >
  [Capacité principale]. Utilise ce skill dès que [cas 1], [cas 2], [cas 3] 
  — même si l'utilisateur ne mentionne pas explicitement [nom].
```

**Exemple :**
```yaml
description: >
  Expert reviewer for Claude SKILL.md files. Use this skill whenever the user 
  shares a skill for review, asks why a skill doesn't trigger, or wants to 
  improve any SKILL.md — even if they don't explicitly say "review my skill".
```

---

## Pattern 3 : With Explicit Phrases (taux : ~80-90%)

Lister des phrases exactes que l'utilisateur pourrait écrire.

```yaml
description: >
  [Capacité]. Triggers on: "[phrase 1]", "[phrase 2]", "[phrase 3]", 
  "[phrase en autre langue si pertinent]".
```

**Exemple :**
```yaml
description: >
  Dungeon Master expert for D&D 5e campaigns. Triggers on: "start a campaign", 
  "let's play D&D", "DM for me", "run an adventure", "jouer à D&D", 
  "maître du donjon", or when a D&D adventure book is mentioned.
```

---

## Pattern 4 : Domain + Negative (clarté maximale)

Inclure ce que le skill NE fait PAS pour éviter les faux positifs.

```yaml
description: >
  [Capacité]. Use when [QUAND]. Do NOT use for [CAS EXCLUS].
```

**Exemple :**
```yaml
description: >
  Creates and edits Word .docx documents with professional formatting. 
  Use when user requests a Word document, report, letter, or memo as a .docx file. 
  Do NOT use for PDFs, spreadsheets, or Google Docs.
```

---

## Anti-patterns à signaler systématiquement

| Anti-pattern | Problème | Correction |
|---|---|---|
| `description: I can help you with X` | 1ère personne → undertriggering | Passer à la 3ème personne |
| `description: Useful for X` | Trop vague, 20% de taux | Ajouter "Use when" + phrases |
| `description: A tool that...` | Article indéfini, peu engageant | Commencer par un verbe ou un nom propre |
| Description > 1024 chars | Tronquée par le système | Résumer, déplacer détails dans le SKILL.md |
| Description < 20 chars | Insuffisant pour le routing | Ajouter capacité + trigger |

---

## Calibration par environnement

| Environnement | Recommandation |
|---|---|
| Claude.ai | Patterns 2 ou 3, phrases en langue de l'utilisateur |
| Claude Code | Pattern 1 ou 4, inclure noms de commandes/fichiers |
| API | Pattern 1, très concis, pas de multilingue |
