# Templates de tutos — Ops

Structures pré-construites pour les tutoriels orientés opérations,
CI/CD, conteneurs et orchestration.

---

## Template : Conteneuriser une application

```
📎 OBJECTIF DU TUTO
───────────────────
Sujet       : Docker — conteneuriser [type d'app]
On va faire : Écrire un Dockerfile, builder et lancer un conteneur
Résultat    : App qui tourne dans un conteneur Docker accessible en local
Durée       : 20-30 min
Prérequis   : Docker installé (docker --version), app source disponible

ÉTAPES TYPE :
1. Vérifier que Docker tourne (docker info)
2. Préparer une app minimale si pas de source existante
3. Créer le Dockerfile — expliquer chaque instruction
4. Créer le .dockerignore
5. Builder l'image (docker build)
6. Vérifier l'image (docker images)
7. Lancer le conteneur (docker run)
8. Tester l'app dans le conteneur (curl, navigateur)
9. Inspecter les logs (docker logs)
10. Arrêter et nettoyer (docker stop, docker rm, docker rmi)
```

---

## Template : Docker Compose multi-services

```
📎 OBJECTIF DU TUTO
───────────────────
Sujet       : Docker Compose — stack [app + BDD + cache/autre]
On va faire : Orchestrer 2-3 conteneurs avec docker-compose
Résultat    : Stack complète qui tourne avec un seul docker compose up
Durée       : 30-45 min
Prérequis   : Docker et Docker Compose installés

ÉTAPES TYPE :
1. Présenter l'architecture de la stack (schéma simple)
2. Créer le docker-compose.yml avec le premier service
3. Ajouter le service de base de données
4. Configurer les volumes pour la persistance
5. Configurer le réseau entre services
6. Ajouter les variables d'environnement (.env)
7. Lancer la stack (docker compose up -d)
8. Vérifier que chaque service tourne (docker compose ps)
9. Tester la communication entre services
10. Voir les logs agrégés (docker compose logs -f)
11. Arrêter et nettoyer (docker compose down -v)
```

---

## Template : Pipeline CI/CD

```
📎 OBJECTIF DU TUTO
───────────────────
Sujet       : CI/CD avec [GitHub Actions / GitLab CI / Jenkins]
On va faire : Créer un pipeline qui build, teste et (optionnel) déploie
Résultat    : Pipeline qui se déclenche sur push et affiche vert
Durée       : 30-45 min
Prérequis   : Repo Git, compte sur la plateforme CI

ÉTAPES TYPE :
1. Créer le repo et le code minimal à builder/tester
2. Expliquer la structure d'un fichier pipeline
3. Écrire le job de build
4. Écrire le job de tests
5. Configurer le déclencheur (push, PR…)
6. Pusher et observer le pipeline tourner
7. Introduire un test qui échoue, voir le pipeline rouge
8. Corriger et voir le vert revenir
9. (Optionnel) Ajouter un job de déploiement / notification
10. Récap des bonnes pratiques pipeline
```

---

## Template : Monitoring basique

```
📎 OBJECTIF DU TUTO
───────────────────
Sujet       : Monitoring avec [Prometheus + Grafana / Netdata / Uptime Kuma]
On va faire : Mettre en place un dashboard de monitoring basique
Résultat    : Dashboard visible avec des métriques en temps réel
Durée       : 30-45 min
Prérequis   : Docker Compose (recommandé), ou accès serveur

ÉTAPES TYPE :
1. Présenter l'architecture du stack de monitoring
2. Déployer l'outil de collecte (Prometheus, telegraf…)
3. Configurer une source de données (métriques système, app…)
4. Déployer le dashboard (Grafana, interface web…)
5. Connecter la source de données au dashboard
6. Créer un premier panneau / graphique
7. Simuler une charge et observer les métriques bouger
8. Configurer une alerte simple (optionnel)
9. Exporter / sauvegarder la config
```

---

## Template : Infrastructure as Code

```
📎 OBJECTIF DU TUTO
───────────────────
Sujet       : IaC avec [Terraform / Ansible / Pulumi]
On va faire : Provisionner/configurer une ressource simple de manière déclarative
Résultat    : Ressource créée et reproductible avec une seule commande
Durée       : 30-60 min
Prérequis   : Outil IaC installé, credentials si cloud

ÉTAPES TYPE :
1. Installer et vérifier l'outil
2. Expliquer le concept déclaratif vs impératif
3. Créer le fichier de configuration minimal
4. Exécuter un plan / dry-run (terraform plan, ansible --check)
5. Appliquer la configuration
6. Vérifier que la ressource existe
7. Modifier la configuration (ajout, changement)
8. Ré-appliquer et observer le diff
9. Détruire / rollback proprement
10. Bonnes pratiques : state, modules, secrets
```

---

## Conventions pour les tutos Ops

### Toujours montrer le nettoyage
Chaque tuto qui lance des conteneurs, VMs ou ressources cloud doit
finir par une section « Nettoyage » avec les commandes de suppression.

### Versions Docker
Toujours préciser la version des images utilisées :
```yaml
# ✅ Bon
image: postgres:16-alpine

# ❌ Mauvais
image: postgres
image: postgres:latest
```

### Sécurité dans les exemples
- Ne jamais mettre de vrais secrets dans les fichiers commités
- Toujours utiliser des variables d'environnement ou .env (gitignored)
- Mentionner les bonnes pratiques même dans un tuto simple

### Schémas d'architecture
Pour les stacks multi-services, toujours commencer par un schéma :
```
[Client] → [Nginx:80] → [App:8000] → [PostgreSQL:5432]
                                    → [Redis:6379]
```
