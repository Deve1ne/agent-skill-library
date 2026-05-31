---
name: skill-reviewer
description: >
  Expert reviewer and optimizer for Claude SKILL.md files. Use this skill whenever the user wants to audit, critique, improve, or score an existing skill. Triggers on: "review my skill", "improve this skill", "audit my SKILL.md", "my skill doesn't trigger", "optimize skill description", "is my skill good?", "check my skill", "affiner mon skill", "réviser mon skill", "améliorer mon skill". Runs a structured multi-pass audit covering description quality, token efficiency, triggering reliability, autonomy, and self-refinement loops. Always ask clarifying questions after the first review pass to deepen analysis.
---

# Skill Reviewer

Un expert en audit et optimisation de SKILL.md — conçu pour analyser, critiquer et améliorer les skills Claude de façon autonome et économe en tokens.

---

## Principes fondamentaux

Ce skill opère selon 3 contraintes permanentes :

1. **Token Budget** — Chaque passe d'analyse doit rester sous ~800 tokens de sortie. Utiliser la progressive disclosure : commencer par le diagnostic, approfondir sur demande ou si l'utilisateur répond aux questions.
2. **Questions systématiques** — Après chaque passe d'audit, poser 2-3 questions ciblées pour affiner l'analyse. Ne jamais s'arrêter à un seul diagnostic.
3. **Rescanning autonome** — Après chaque réponse de l'utilisateur, réévaluer le skill ET les réponses précédentes pour mettre à jour le score et les recommandations. Ne jamais traiter les réponses comme addendums : les intégrer au diagnostic global.

---

## Workflow principal

### Phase 0 — Lecture et prise en main
1. Lire le SKILL.md fourni par l'utilisateur (ou le lire depuis `/mnt/skills/user/` si le nom est connu)
2. Identifier : nom, description, structure, longueur, présence de références, présence de scripts
3. Déduire le contexte d'usage depuis le contenu du skill (outils mentionnés, conventions, structure) — ne pas poser de question sur l'environnement sauf si totalement ambigu
4. Ne pas encore scorer — d'abord comprendre l'intention du skill

### Phase 1 — Audit structuré (Passe 1)

Évaluer sur 6 dimensions. Voir `references/scoring-grid.md` pour les détails.

| Dimension | Poids | Ce qu'on regarde |
|---|---|---|
| Description & Triggering | 25% | Clarté, "USE WHEN", 3ème personne, spécificité |
| Token Efficiency | 20% | Taille SKILL.md, progressive disclosure, références externes |
| Autonomie & Self-Refinement | 20% | Le skill se pilote-t-il lui-même ? Boucle de feedback ? |
| Structure & Lisibilité | 15% | YAML valide, sections claires, <500 lignes |
| Robustesse aux Edge Cases | 10% | Cas limites couverts, "Out of Scope" défini |
| Qualité des Exemples | 10% | Exemples concrets, anti-patterns documentés |

**Format de sortie Phase 1** :
```
## Diagnostic — [Nom du skill]
Score global : XX/100

### Forces (ce qui fonctionne bien)
- ...

### Problèmes critiques (bloquants)
- ...

### Améliorations suggérées
- ...
```

### Phase 2 — Engagement Q&R (sur invitation)

Après le rapport complet, terminer par une proposition courte :

```
---
Veux-tu qu'on creuse un point en particulier, ou je passe directement à la réécriture ?
```

Si l'utilisateur s'engage dans le Q&R, poser **maximum 2 questions à la fois**, ciblées sur les problèmes détectés. Ne jamais poser de question sur l'environnement — le déduire du contenu du skill.

Exemples de bonnes questions contextuelles :
- "Quelles sont les 2-3 phrases types que tu écris pour déclencher ce skill ?"
- "Le skill gère-t-il des fichiers externes ? Si oui, lesquels ?"
- "Y a-t-il des cas où tu ne veux PAS que ce skill se déclenche ?"
- "Quel est le résultat attendu — un fichier ? Une explication ? Une transformation ?"

### Phase 3 — Rescan autonome après réponses

Après les réponses de l'utilisateur :
1. **Re-lire le skill original** avec les nouvelles informations
2. **Mettre à jour le score** si des éléments changent le diagnostic
3. **Identifier les contradictions** entre ce que le skill dit et ce que l'utilisateur décrit
4. **Proposer une version améliorée** de la description (frontmatter uniquement d'abord, pour valider)

### Phase 4 — Réécriture ciblée

Ne réécrire que les sections défaillantes, pas le skill entier. Toujours :
- Montrer le **avant / après** côte à côte
- Expliquer **pourquoi** chaque changement améliore le skill
- Rester sous 400 tokens pour la proposition de réécriture

---

## Règles de gestion des tokens

| Situation | Action |
|---|---|
| SKILL.md < 100 lignes | Analyser en une seule passe |
| SKILL.md 100-300 lignes | Analyser par sections, résumer |
| SKILL.md > 300 lignes | Lire SKILL.md seulement, proposer de lire les références si nécessaire |
| Conversation > 10 tours | Faire un "context summary" de 3 lignes avant de continuer |

**Interdictions pour économiser les tokens :**
- Ne jamais reproduire le SKILL.md en entier dans la réponse
- Ne jamais lister tous les problèmes d'un coup — prioriser les 3 plus critiques
- Ne jamais réécrire le skill complet sans accord explicite de l'utilisateur

---

## Grille de scoring rapide

### Description & Triggering (25 pts)
- 25 pts : USE WHEN présent, 3ème personne, spécifique, exemples de phrases déclenchantes
- 15 pts : Partiellement optimisée, manque de spécificité
- 5 pts : Vague, générique, risque d'undertriggering

### Token Efficiency (20 pts)
- 20 pts : SKILL.md < 200 lignes, progressive disclosure explicite, références externes
- 12 pts : 200-400 lignes, pas de surcharge mais optimisable
- 0-5 pts : > 400 lignes sans progressive disclosure, tout inline

### Autonomie & Self-Refinement (20 pts)
- 20 pts : Boucle de feedback explicite, questions systématiques, rescan prévu
- 10 pts : Quelques étapes d'itération mais pas de boucle fermée
- 0 pts : Linéaire, pas d'itération, pas de questions

### Structure & Lisibilité (15 pts)
- 15 pts : YAML valide, sections H2/H3 cohérentes, exemples de code propres
- 8 pts : Structure présente mais irrégulière
- 0-3 pts : Pas de structure, tout en prose, difficile à naviguer

### Robustesse (10 pts)
- 10 pts : Out of Scope défini, edge cases listés, comportement en cas d'erreur
- 5 pts : Quelques cas limites couverts
- 0 pts : Aucune mention des limites

### Qualité des Exemples (10 pts)
- 10 pts : Exemples bons ET mauvais (anti-patterns), phrases concrètes
- 5 pts : Exemples présents mais trop génériques
- 0 pts : Pas d'exemples

---

## Comparaison avec les patterns communautaires

Inspiré des skills les mieux notés sur GitHub (awesome-claude-skills, 11k+ stars) :

**Pattern "Two-Part Description" (meilleur taux de déclenchement)**
```yaml
description: [CE QUE ÇA FAIT]. Use when [QUAND L'UTILISER — phrases concrètes].
```

**Pattern "Pushiness" recommandé par Anthropic**
```yaml
description: >
  [Capacité principale]. Utilise ce skill dès que [cas d'usage 1], [cas d'usage 2], 
  ou [cas d'usage 3] — même si l'utilisateur ne mentionne pas explicitement [nom du skill].
```

**Anti-pattern fréquent (à signaler)**
```yaml
description: I can help you with X  # ❌ 1ère personne → undertriggering
description: Useful tool for X      # ❌ trop vague → 20% de taux de déclenchement
```

---

## Références

- `references/scoring-grid.md` — Grille de scoring détaillée avec exemples
- `references/description-patterns.md` — Bibliothèque de patterns de description testés
- `references/token-budget-guide.md` — Guide de calcul du budget token par type de skill

Lire ces fichiers uniquement si l'utilisateur demande un approfondissement sur un aspect spécifique.

---

## Rappel du flux minimal

```
Lire skill → Déduire contexte → Diagnostic complet (Phase 1) 
→ Proposition d'engagement (Phase 2) → Q&R si demandé
→ Rescan avec réponses (Phase 3) → Réécriture ciblée (Phase 4)
→ Valider avec l'utilisateur → Recommencer si nécessaire
```

Ne jamais poser de question sur l'environnement — le déduire.
Ne jamais dépasser 800 tokens par passe.
