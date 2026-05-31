# Template de cheatsheet

Format standard pour les fiches récapitulatives générées en fin de tuto.
Une cheatsheet est un condensé des commandes clés, sans explications détaillées.

---

## Structure type

```markdown
# Cheatsheet — [Sujet]

> Généré le [date] — Résumé du tuto "[titre du tuto]"

## Setup rapide

    commande d'installation
    commande de vérification

## Commandes essentielles

| Action | Commande |
|---|---|
| [action 1] | `commande 1` |
| [action 2] | `commande 2` |
| [action 3] | `commande 3` |

## Fichiers importants

| Fichier | Rôle |
|---|---|
| `/chemin/fichier1` | [description courte] |
| `/chemin/fichier2` | [description courte] |

## Config type

    bloc de config minimal fonctionnel
    (le minimum pour que ça marche)

## Dépannage express

| Symptôme | Fix |
|---|---|
| [erreur courante 1] | `commande fix` |
| [erreur courante 2] | `commande fix` |

## Liens utiles

- [Documentation officielle](url)
- [Awesome list](url)
```

---

## Règles de rédaction

- Maximum 1 page écran (50-80 lignes)
- Pas d'explications, juste les commandes
- Utiliser des tableaux pour les correspondances action → commande
- Inclure seulement les commandes vues dans le tuto
- Toujours inclure la section "Dépannage express"
- Les commandes doivent être copiables telles quelles

## Nommage du fichier

Format : `{sujet}_cheatsheet.md`

Exemples :
- `docker_cheatsheet.md`
- `nginx_reverse_proxy_cheatsheet.md`
- `git_rebase_cheatsheet.md`
- `fastapi_crud_cheatsheet.md`
