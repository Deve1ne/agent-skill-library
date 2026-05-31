# Guide — Génération de description optimale

Charger ce fichier uniquement si la description générée en Phase 4 semble faible
ou si l'utilisateur demande à optimiser le triggering.

---

## Pattern recommandé (taux ~80%)

```yaml
description: >
  [Capacité principale en 1 phrase, 3ème personne].
  Utilise ce skill dès que [cas 1], [cas 2], ou [cas 3].
  Déclenche sur : "[phrase exacte 1]", "[phrase exacte 2]", "[phrase exacte 3]".
  [Si universel] Compatible Claude, Copilot, Codex CLI, Gemini CLI.
  [Si Claude-spécifique] Optimisé pour Claude.ai — utilise [feature X].
  Ne pas déclencher si [exclusion principale].
```

## Checklist avant de valider la description

- [ ] 3ème personne (pas "I" ni "you")
- [ ] "Utilise ce skill dès que" présent
- [ ] Minimum 3 phrases déclenchantes entre guillemets
- [ ] Mention universalité OU Claude-spécifique
- [ ] Au moins une exclusion ("Ne pas déclencher si...")
- [ ] < 800 caractères
- [ ] Pas de XML tags

## Calcul rapide de longueur

```
len(description) → doit être < 800
Si > 800 : supprimer les synonymes redondants en priorité
Si > 1000 : risque de troncature selon l'environnement
```

## Compatibilité universelle — features à éviter

Si Q6 = universel, ne pas mentionner ni utiliser dans le SKILL.md :
- `artifacts` (Claude.ai uniquement)
- `web_search` intégré (Claude.ai uniquement)
- `computer_use` (Claude.ai uniquement)
- `Claude Code subagents` (Claude Code uniquement)
- `context: fork` (Claude Code uniquement)
- `allowed-tools` (Claude Code uniquement)

Features universelles (OK dans tous les cas) :
- Instructions markdown standard
- Références vers des fichiers (`references/`)
- Workflows en phases numérotées
- Boucles de feedback textuelles
- Tableaux et listes
