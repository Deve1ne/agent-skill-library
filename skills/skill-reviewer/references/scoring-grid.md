# Scoring Grid — Détail complet

## Dimension 1 : Description & Triggering (25 pts)

### Checklist
- [ ] Rédigée à la 3ème personne (pas "I" ni "you")
- [ ] Contient "Use when" ou "Utilise ce skill dès que"
- [ ] Mentionne des phrases concrètes que l'utilisateur pourrait écrire
- [ ] Spécifique au domaine (pas générique comme "helps with tasks")
- [ ] Pas de XML tags dans la description
- [ ] < 1024 caractères

### Exemples de scores

**25/25 ✅**
```yaml
description: >
  Analyse et optimise les SKILL.md Claude. Use when user says "review my skill", 
  "my skill doesn't trigger", "improve SKILL.md", "audit skill", or whenever 
  a SKILL.md file is shared for critique.
```

**12/25 ⚠️**
```yaml
description: Helps improve Claude skills and prompts.
```

**2/25 ❌**
```yaml
description: I can help you with your skills.
```

---

## Dimension 2 : Token Efficiency (20 pts)

### Principes
- Le SKILL.md de base doit être < 500 lignes (idéal < 200)
- Les détails longs → fichiers `references/`
- Les scripts → dossier `scripts/`
- Chaque section doit justifier sa présence

### Calcul approximatif
| Longueur SKILL.md | Score de base |
|---|---|
| < 100 lignes | 20 |
| 100-200 lignes | 18 |
| 200-350 lignes | 13 |
| 350-500 lignes | 7 |
| > 500 lignes | 2 |

Bonus (+3) si progressive disclosure explicitement documentée.
Malus (-5) si des tableaux ou listes répétitives pourraient être en référence.

---

## Dimension 3 : Autonomie & Self-Refinement (20 pts)

### Ce qu'on cherche
Un skill autonome doit :
1. **Poser des questions** après chaque sortie pour valider ou affiner
2. **Rescan** : relire ses propres sorties et les mettre à jour à la lumière de nouveaux inputs
3. **Avoir une boucle fermée** : pas de workflow linéaire sans retour

### Niveaux
| Niveau | Description | Score |
|---|---|---|
| Haute autonomie | Boucle explicite, questions systématiques, rescan documenté | 20 |
| Autonomie partielle | Itérations mentionnées mais pas structurées | 10-15 |
| Linéaire | Workflow "do once and done", pas de feedback | 0-5 |

### Exemple de boucle autonome bien écrite
```markdown
## Boucle d'itération
1. Produire le résultat initial
2. Poser 2-3 questions de validation
3. Sur réponse : relire le contexte complet + mettre à jour
4. Répéter jusqu'à validation explicite de l'utilisateur
```

---

## Dimension 4 : Structure & Lisibilité (15 pts)

### Checklist
- [ ] YAML frontmatter valide (name + description minimum)
- [ ] Sections H2 (##) pour les grandes parties
- [ ] Sections H3 (###) pour les sous-parties
- [ ] Blocs de code avec syntaxe spécifiée (```yaml, ```markdown...)
- [ ] Pas de murs de texte (paragraphes > 5 lignes)
- [ ] Tableaux pour les informations comparatives

---

## Dimension 5 : Robustesse (10 pts)

### Ce qu'on cherche
- Section "Out of Scope" ou "Ce skill ne fait PAS..."
- Mention des cas limites connus
- Comportement attendu en cas d'erreur ou d'ambiguïté

### Exemple bien fait
```markdown
## Limites de ce skill
Ce skill NE gère PAS :
- Les fichiers > 1000 lignes sans découpage explicite
- Les skills en langages autres que FR/EN
- La création de skills from scratch (utiliser skill-creator)
```

---

## Dimension 6 : Qualité des Exemples (10 pts)

### Bons exemples = exemples + anti-patterns

| Type | Description | Points |
|---|---|---|
| Exemples bons + mauvais | Avant/après, avec explication | 10 |
| Exemples bons seulement | Utiles mais incomplets | 6 |
| Exemples génériques | "Example: do X" sans contexte | 3 |
| Pas d'exemples | — | 0 |
