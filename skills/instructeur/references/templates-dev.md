# Templates de tutos — Dev

Structures pré-construites pour les tutoriels orientés développement.
Adapter selon le sujet, le niveau et le temps disponible.

---

## Template : Créer une API REST

```
📎 OBJECTIF DU TUTO
───────────────────
Sujet       : API REST avec [framework]
On va faire : Créer une API avec 1-2 endpoints, validation, et tests
Résultat    : Serveur qui répond sur localhost, testable avec curl
Durée       : 30-45 min
Prérequis   : [langage] installé, terminal disponible

ÉTAPES TYPE :
1. Créer le dossier projet et initialiser
2. Installer le framework et les dépendances
3. Créer le fichier principal avec un endpoint GET /health
4. Lancer le serveur et vérifier avec curl
5. Ajouter un modèle de données simple (ex : une tâche)
6. Créer un endpoint POST pour ajouter une entrée
7. Créer un endpoint GET pour lister les entrées
8. Ajouter une validation basique sur les inputs
9. Écrire un test unitaire simple
10. Lancer les tests et vérifier que tout passe
```

---

## Template : Premier projet avec un framework frontend

```
📎 OBJECTIF DU TUTO
───────────────────
Sujet       : [React / Vue / Svelte / Angular]
On va faire : Créer une mini-app interactive (ex : todo list, compteur, formulaire)
Résultat    : App fonctionnelle dans le navigateur
Durée       : 30-45 min
Prérequis   : Node.js installé

ÉTAPES TYPE :
1. Créer le projet avec l'outil CLI officiel
2. Explorer la structure des fichiers générés
3. Lancer le serveur de dev et voir la page par défaut
4. Modifier le composant principal (comprendre le hot-reload)
5. Créer un composant simple avec du state
6. Ajouter une interaction utilisateur (bouton, formulaire)
7. Gérer une liste d'éléments (ajout, affichage)
8. Ajouter un peu de style (CSS modules, Tailwind ou inline)
9. Vérification finale dans le navigateur
```

---

## Template : Écrire des tests

```
📎 OBJECTIF DU TUTO
───────────────────
Sujet       : Tests avec [pytest / jest / go test / cargo test]
On va faire : Écrire des tests unitaires pour un module simple
Résultat    : Suite de tests qui passe au vert
Durée       : 20-30 min
Prérequis   : Projet existant ou code minimal à tester

ÉTAPES TYPE :
1. Installer le framework de test
2. Créer un module simple à tester (ex : fonctions utilitaires)
3. Écrire un premier test trivial (assert True)
4. Lancer les tests, vérifier le vert
5. Écrire un test pour un cas nominal
6. Écrire un test pour un cas d'erreur / edge case
7. Introduire un mock si pertinent
8. Voir la couverture de code (optionnel)
9. Lancer la suite complète
```

---

## Template : Utiliser Git efficacement

```
📎 OBJECTIF DU TUTO
───────────────────
Sujet       : Git [concept spécifique : branches, rebase, hooks, bisect…]
On va faire : Pratiquer le concept sur un repo de test
Résultat    : Historique Git propre démontrant le concept
Durée       : 15-30 min
Prérequis   : Git installé et configuré (user.name, user.email)

ÉTAPES TYPE :
1. Créer un repo de test avec quelques commits
2. Expliquer le concept visuellement (schéma ASCII si utile)
3. Exécuter la commande Git principale
4. Observer le résultat avec git log --oneline --graph
5. Pratiquer un cas d'usage réaliste
6. Montrer comment annuler / corriger si ça tourne mal
7. Résumé des commandes clés
```

---

## Template : Connecter une base de données

```
📎 OBJECTIF DU TUTO
───────────────────
Sujet       : [PostgreSQL / MySQL / MongoDB / SQLite / Redis] avec [langage]
On va faire : Connecter une app à une BDD, CRUD basique
Résultat    : Données persistées, requêtes fonctionnelles
Durée       : 30-45 min
Prérequis   : BDD installée ou Docker disponible

ÉTAPES TYPE :
1. Installer / lancer la BDD (Docker recommandé si possible)
2. Se connecter manuellement (CLI ou GUI) et créer une BDD de test
3. Installer le driver / ORM dans le projet
4. Configurer la connexion (string de connexion, env vars)
5. Créer un schéma / modèle simple
6. Écrire une insertion (Create)
7. Écrire une lecture (Read)
8. Écrire une mise à jour (Update) et une suppression (Delete)
9. Vérifier les données dans la BDD directement
10. Nettoyage (arrêter le conteneur si Docker)
```

---

## Conventions pour les tutos Dev

### Nommage des projets d'exemple
- Utiliser des noms descriptifs : `mon-api-taches`, `todo-app-react`, `test-demo-pytest`
- Éviter les noms génériques : `test`, `app`, `project`

### Structure de fichiers à montrer
Toujours afficher l'arborescence après le setup initial :
```
mon-projet/
├── src/
│   └── main.py
├── tests/
│   └── test_main.py
├── requirements.txt
└── README.md
```

### Gestion des dépendances
- Toujours créer un fichier de dépendances (requirements.txt, package.json…)
- Mentionner l'option d'un environnement virtuel (venv, nvm…)
- Indiquer les versions exactes dans les exemples
