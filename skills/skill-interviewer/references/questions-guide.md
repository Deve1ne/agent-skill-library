# Questions Guide — Exemples par question

Charger ce fichier uniquement si la question générée dynamiquement semble trop vague
ou si l'utilisateur ne répond pas de façon exploitable.

---

## Q1 — Capacité principale

Objectif : comprendre ce que le skill fait concrètement, en une phrase.

Formulations possibles :
- "Qu'est-ce que ce skill doit faire ? Décris en une phrase ce que tu veux obtenir."
- "Quelle tâche répétitive veux-tu automatiser avec ce skill ?"
- "Si ce skill existait, qu'est-ce que tu lui demanderais en premier ?"

Si la réponse est trop vague ("quelque chose avec les données") → relancer :
"Peux-tu me donner un exemple concret de ce que tu ferais avec ce skill ?"

---

## Q2 — Déclenchement

Objectif : identifier les phrases exactes que l'utilisateur écrirait pour activer le skill.

Formulations possibles :
- "Quelles phrases exactes tu écrirais pour déclencher ce skill ?"
- "Comment tu demanderais ça à Claude normalement, sans skill ?"
- "Y a-t-il des mots-clés spécifiques à ce domaine ?"
- "Si tu avais ce skill installé, comment tu l'appellerais ?"

---

## Q3 — Output attendu

Objectif : savoir ce que le skill produit (fichier, explication, transformation...).

Formulations possibles :
- "Qu'est-ce que le skill doit produire à la fin — un fichier, une analyse, un dialogue ?"
- "Est-ce que le résultat est réutilisable ailleurs ou juste pour cette conversation ?"
- "Tu veux quelque chose à télécharger, ou une réponse dans le chat ?"
- "Le résultat est pour toi seul ou pour le partager ?"

---

## Q4 — Limites et exclusions

Objectif : définir ce que le skill NE fait PAS pour éviter les faux positifs.

Formulations possibles :
- "Y a-t-il des cas proches où tu ne veux PAS que ce skill se déclenche ?"
- "Qu'est-ce qui ressemble à ce skill mais est différent ?"
- "Des sujets ou contextes à exclure explicitement ?"
- "Si quelqu'un d'autre installe ce skill, qu'est-ce qu'il ne devrait pas attendre de lui ?"

---

## Q5 — Niveau d'autonomie

Objectif : savoir si le skill doit poser des questions, itérer, ou exécuter directement.

Formulations possibles :
- "Le skill doit-il poser des questions avant d'agir, ou exécuter directement ?"
- "Est-ce qu'il doit proposer des variantes ou aller droit au but ?"
- "Une boucle de validation est-elle utile ici ?"
- "Tu préfères un skill qui demande confirmation à chaque étape, ou qui livre directement ?"

Niveaux possibles :
- **Direct** : exécute sans poser de questions
- **Questions** : demande des précisions avant d'agir
- **Itératif** : produit, demande feedback, raffine en boucle

---

## Q6 — Compatibilité (script fixe)

Ne pas adapter cette question — la formuler toujours ainsi :

```
Ce skill doit-il rester universel (Claude, Copilot, Codex, Gemini CLI...)
ou peut-il utiliser des features Claude-spécifiques comme les artifacts,
la recherche web intégrée, ou les outils computer use ?
```

### Si universel → exclure du SKILL.md généré :
- `artifacts` (Claude.ai uniquement)
- `web_search` intégré (Claude.ai uniquement)
- `computer_use` (Claude.ai uniquement)
- `context: fork` (Claude Code uniquement)
- `allowed-tools` (Claude Code uniquement)

### Si Claude-spécifique → ajouter dans le SKILL.md généré :
```markdown
## Compatibilité
Ce skill est optimisé pour Claude. Il utilise :
- [feature 1] — [pourquoi]
- [feature 2] — [pourquoi]
Non compatible avec Copilot, Codex CLI, Gemini CLI sans adaptation.
```
