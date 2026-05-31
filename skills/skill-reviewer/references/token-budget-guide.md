# Token Budget Guide

## Pourquoi le budget token compte

Dans Claude.ai, chaque conversation a une limite de contexte. Un skill mal conçu peut :
- Exploser le contexte en chargeant des fichiers inutiles
- Générer des réponses trop longues qui saturent la fenêtre
- Forcer l'utilisateur à relancer la conversation

---

## Budget recommandé par type d'output

| Type de sortie | Budget max recommandé |
|---|---|
| Diagnostic initial (Phase 1) | 400-600 tokens |
| Questions de clarification | 100-150 tokens |
| Rescan + mise à jour score | 200-350 tokens |
| Réécriture d'une section | 300-400 tokens |
| Réécriture complète description | 100-200 tokens |
| **Total par session** | **< 3000 tokens actifs** |

---

## Technique : Progressive Disclosure dans un skill

### Niveau 1 — Toujours chargé (description frontmatter)
~100 tokens. Ne mettre que le strict nécessaire pour le routing.

### Niveau 2 — Chargé à l'activation (corps du SKILL.md)
< 500 lignes = ~4000-5000 tokens max. Tout ce qui est nécessaire au workflow principal.

### Niveau 3 — Chargé sur demande (fichiers references/)
Aucune limite stricte, mais charger **un fichier à la fois** et **seulement si pertinent**.

---

## Signaux d'alerte dans un skill audité

| Signal | Problème | Recommandation |
|---|---|---|
| SKILL.md > 400 lignes | Surcharge mémoire | Déplacer 30-40% en references/ |
| Tableaux répétés > 3 fois | Redondance | Consolider en un seul tableau |
| Exemples de code > 50 lignes inline | Surcharge | Déplacer en scripts/ ou references/ |
| Instructions "always load references/" | Force le chargement | Conditionner le chargement |
| Pas de section "Out of Scope" | Risque de faux positifs | Ajouter limites explicites |

---

## Calcul approximatif tokens/lignes

- 1 ligne de texte ordinaire ≈ 10-15 tokens
- 1 ligne de code ≈ 15-25 tokens
- 1 tableau (ligne) ≈ 20-30 tokens
- Frontmatter YAML complet ≈ 50-100 tokens

**Exemple :** SKILL.md de 200 lignes ≈ 2500-3500 tokens au chargement.

---

## Stratégie "Lazy Loading" pour skills complexes

```markdown
## Références disponibles
Ce skill dispose de guides détaillés. Ne les charger que si nécessaire :
- `references/advanced.md` — Charger si l'utilisateur pose des questions avancées
- `references/examples.md` — Charger si des exemples supplémentaires sont demandés
- `scripts/helper.py` — Exécuter seulement pour [cas spécifique]
```

Cela permet au skill de rester léger par défaut tout en ayant des ressources disponibles.
