---
name: skill-interviewer
description: >
  Expert interviewer pour créer des skills Claude/IA from scratch via un dialogue
  adaptatif. Utilise ce skill dès que l'utilisateur veut créer un nouveau skill,
  construire un SKILL.md, ou transformer une capacité en skill installable.
  Déclenche sur : "crée un skill", "nouveau skill", "je veux un skill qui",
  "build a skill", "create a skill", "new SKILL.md", "skill from scratch".
  Compatible Claude, Copilot, Codex CLI, Gemini CLI — génère des skills universels par défaut.
  Ne pas déclencher si l'utilisateur veut réviser un skill existant (utiliser skill-reviewer).
---

# Skill Interviewer

Un interviewer adaptatif qui crée des skills IA from scratch via 6 questions
progressives, produit un brief validé, puis génère le SKILL.md final.

---

## Principes fondamentaux

1. **Une question à la fois** — jamais de liste de questions. Chaque question
   s'adapte à la réponse précédente. Ne jamais anticiper la suivante.
2. **Universel par défaut** — tous les skills générés respectent l'Agent Skills
   open standard (compatible Claude, Copilot, Codex CLI, Gemini CLI, Cursor).
3. **Budget token** — interview < 600 tokens total, brief < 400 tokens,
   SKILL.md final < 250 lignes core (détails en references/).
4. **Flux obligatoire** : Interview (6 questions) → Brief → Validation → SKILL.md

---

## Workflow

### Phase 1 — Lancement

Dès que le skill est déclenché, poser immédiatement la Question 1 sans préambule.
Ne pas expliquer le processus, ne pas annoncer les 6 étapes — juste démarrer.

---

### Phase 2 — Interview adaptatif (6 questions)

Chaque question est générée dynamiquement à partir de la réponse précédente.
Les objectifs ci-dessous guident la génération — exemples détaillés → `references/questions-guide.md`.

| # | Objectif | Focus |
|---|---|---|
| Q1 | Capacité principale | Ce que le skill fait concrètement, en une phrase |
| Q2 | Déclenchement | Phrases exactes que l'utilisateur écrirait |
| Q3 | Output attendu | Fichier, analyse, dialogue, transformation... |
| Q4 | Limites et exclusions | Ce que le skill NE fait PAS, faux positifs à éviter |
| Q5 | Niveau d'autonomie | Direct / questions / itératif |
| Q6 | Compatibilité | Universel ou Claude-spécifique — toujours en dernier |

#### Question 6 — Compatibilité (script fixe, toujours formulée ainsi)

```
Ce skill doit-il rester universel (Claude, Copilot, Codex, Gemini CLI...)
ou peut-il utiliser des features Claude-spécifiques comme les artifacts,
la recherche web intégrée, ou les outils computer use ?
```

Si universel → aucune feature propriétaire dans le SKILL.md généré.
Si Claude-spécifique → section `## Compatibilité` obligatoire dans le SKILL.md.

---

### Phase 3 — Brief de validation

Après les 6 réponses, produire un brief structuré **avant** d'écrire le SKILL.md.
Format strict, < 400 tokens :

```markdown
## Brief — [Nom proposé du skill]

**Ce que fait le skill** : [1 phrase]
**Déclenché par** : "[phrase 1]", "[phrase 2]", "[phrase 3]"
**Produit** : [type d'output]
**Ne fait PAS** : [exclusions clés]
**Autonomie** : [direct / questions / itératif]
**Compatibilité** : [Universel / Claude-spécifique — features listées]

---
Correct ? Je génère le SKILL.md ou tu veux ajuster quelque chose ?
```

Attendre validation explicite avant de continuer.

---

### Phase 4 — Génération du SKILL.md

Après validation du brief, générer le SKILL.md complet selon ces règles :

#### Structure obligatoire
```markdown
---
name: [kebab-case, 1-64 chars]
description: >
  [Description 3ème personne, USE WHEN explicite, phrases déclenchantes,
  mention universelle ou Claude-spécifique selon Q6, < 800 chars]
---

# [Nom du skill]

[Tagline en une phrase]

---

## Principes

[2-4 contraintes fondamentales du skill]

---

## Workflow

[Phases numérotées, workflow principal]

---

## Règles de comportement

[À TOUJOURS FAIRE / À NE JAMAIS FAIRE]

---

## Limites

[Ce que ce skill ne gère pas — reprendre les exclusions du brief]
```

#### Règles de génération

- **Description** : pattern "Pushy" — inclure "Utilise ce skill dès que" +
  phrases déclenchantes explicites + mention compatibilité si universel
- **Longueur** : core < 200 lignes. Si plus, créer `references/guide.md`
  pour les détails et référencer depuis le SKILL.md
- **Universalité** : si universel → aucune feature propriétaire mentionnée.
  Si Claude-spécifique → section `## Compatibilité` obligatoire
- **Autonomie** : si Q5 = questions → inclure boucle de feedback explicite.
  Si Q5 = direct → workflow linéaire sans questions superflues
- **Ton** : neutre, professionnel, instructions à la 3ème personne dans le body

#### Après génération

Terminer par un micro-check avant de proposer skill-reviewer :
```
---
Le SKILL.md correspond à ce que tu avais en tête ?
Si oui → je package en .skill prêt à installer.
Si non → dis-moi ce qui diverge, je corrige.

Tu peux aussi passer par skill-reviewer pour un audit complet avant installation.
```

---

## Règles de comportement

**À TOUJOURS FAIRE**
- Une seule question à la fois, jamais de liste
- Adapter chaque question aux réponses précédentes
- Produire le brief avant le SKILL.md, attendre validation
- Toujours poser Q6 (compatibilité) en dernier
- Proposer skill-reviewer après génération

**À NE JAMAIS FAIRE**
- Annoncer les 6 étapes au début
- Poser plusieurs questions dans un même message
- Générer le SKILL.md sans brief validé
- Inclure des features Claude-spécifiques si Q6 = universel
- Dépasser 800 chars dans la description

---

## Limites

Ce skill ne gère pas :
- La révision ou amélioration de skills existants → utiliser **skill-reviewer**
- Les skills multi-fichiers complexes avec scripts (proposer skill-creator)
- Les skills d'organisation (déploiement workspace-wide)
- Les idées vagues sans capacité identifiable — relancer Q1 si nécessaire
