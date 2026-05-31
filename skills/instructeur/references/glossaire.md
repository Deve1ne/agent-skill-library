# Glossaire IT — Français / Anglais

Référence pour les utilisateurs débutants. Charger quand le niveau
est "débutant" pour utiliser les termes français avec la correspondance
anglaise entre parenthèses.

---

## Termes généraux

| Français | Anglais | Définition simple |
|---|---|---|
| Dépôt / Répertoire de code | Repository (repo) | Dossier contenant le code et son historique |
| Branche | Branch | Version parallèle du code pour travailler sans casser le principal |
| Fusion | Merge | Combiner deux branches ensemble |
| Livraison / Commit | Commit | Snapshot du code à un instant T |
| Tirer / Récupérer | Pull | Télécharger les dernières modifications depuis le serveur |
| Pousser | Push | Envoyer ses modifications vers le serveur |
| Conflit | Conflict | Quand deux personnes modifient la même ligne |

## Dev

| Français | Anglais | Définition simple |
|---|---|---|
| Dépendance | Dependency | Bibliothèque externe dont le projet a besoin |
| Environnement virtuel | Virtual environment (venv) | Espace isolé pour les dépendances d'un projet |
| Point d'accès | Endpoint | URL à laquelle une API répond |
| Requête | Request | Message envoyé à un serveur pour demander quelque chose |
| Réponse | Response | Message renvoyé par le serveur |
| Test unitaire | Unit test | Test d'une seule petite partie du code |
| Couverture de code | Code coverage | Pourcentage du code vérifié par les tests |
| Débogage | Debugging | Trouver et corriger les erreurs dans le code |
| Refactorisation | Refactoring | Réorganiser le code sans changer son comportement |
| Linter | Linter | Outil qui vérifie le style et les erreurs du code |
| Framework | Framework | Ensemble d'outils et de conventions pour construire une app |
| Bibliothèque | Library | Code réutilisable qu'on importe dans son projet |
| Sérialisation | Serialization | Convertir un objet en texte (JSON, XML…) pour le transmettre |

## Ops

| Français | Anglais | Définition simple |
|---|---|---|
| Conteneur | Container | Boîte isolée qui contient une app et tout ce qu'il lui faut |
| Image | Image | Modèle à partir duquel on crée un conteneur |
| Orchestration | Orchestration | Gérer automatiquement plusieurs conteneurs |
| Pipeline | Pipeline | Suite d'étapes automatisées (build, test, deploy) |
| Intégration continue | Continuous Integration (CI) | Tester automatiquement le code à chaque modification |
| Déploiement continu | Continuous Deployment (CD) | Mettre en production automatiquement après les tests |
| Registre | Registry | Magasin d'images de conteneurs (DockerHub, GHCR…) |
| Volume | Volume | Espace de stockage persistant pour un conteneur |
| Variable d'environnement | Environment variable (env var) | Configuration stockée en dehors du code |
| Artéfact | Artifact | Fichier produit par le pipeline (binaire, image, rapport) |
| Infrastructure as Code | IaC | Décrire l'infra dans des fichiers plutôt que cliquer |

## Infra

| Français | Anglais | Définition simple |
|---|---|---|
| Serveur | Server | Machine qui fournit des services à d'autres machines |
| Pare-feu | Firewall | Filtre qui contrôle le trafic réseau entrant/sortant |
| Proxy inverse | Reverse proxy | Intermédiaire qui reçoit les requêtes et les redirige vers le bon serveur |
| Résolution DNS | DNS resolution | Traduire un nom de domaine (google.com) en adresse IP |
| Certificat SSL/TLS | SSL/TLS certificate | Preuve d'identité d'un site, nécessaire pour HTTPS |
| Clé SSH | SSH key | Paire de clés (publique + privée) pour se connecter sans mot de passe |
| Port | Port | Numéro qui identifie un service sur une machine (80=HTTP, 443=HTTPS…) |
| Sous-réseau | Subnet | Division d'un réseau en parties plus petites |
| Passerelle | Gateway | Point de sortie d'un réseau vers un autre |
| Équilibrage de charge | Load balancing | Répartir le trafic entre plusieurs serveurs |
| Haute disponibilité | High availability (HA) | Architecture conçue pour ne jamais tomber en panne |
| Provisionnement | Provisioning | Créer et configurer automatiquement des ressources |
| Sauvegarde | Backup | Copie de sécurité des données |
| Journaux | Logs | Fichiers qui enregistrent ce qui se passe sur un système |
| Démon / Service | Daemon / Service | Programme qui tourne en arrière-plan |

---

## Utilisation dans les tutos

Quand l'utilisateur est débutant, utiliser la convention suivante
à la première occurrence d'un terme technique :

```
On va créer un **conteneur** (container) — une sorte de boîte isolée
qui contient l'application et tout ce dont elle a besoin pour tourner.
```

Après la première occurrence, utiliser le terme français seul.
