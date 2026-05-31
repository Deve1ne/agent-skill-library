# Checklist de configuration utilisateur

Guide détaillé pour la Phase 1 — Collecte du contexte.

---

## Questions universelles (tout sujet)

### OS et environnement

| Question | Réponses possibles | Impact |
|---|---|---|
| Distribution Linux ? | Ubuntu, Debian, Fedora, Arch, Alpine… | Gestionnaire de paquets, chemins |
| Version macOS ? | Ventura, Sonoma, Sequoia… | Compatibilité Homebrew, outils système |
| Windows : WSL installé ? | WSL1, WSL2, pas de WSL | Détermine si on peut utiliser un workflow Linux |
| Architecture CPU ? | x86_64, ARM64 (Apple Silicon) | Images Docker, binaires à télécharger |

### IDE et éditeur

| Question | Réponses possibles | Impact |
|---|---|---|
| Éditeur principal ? | VS Code, Vim, Neovim, IntelliJ, Sublime, Emacs… | Extensions, raccourcis, config |
| Extensions déjà installées ? | Lister les principales | Éviter de recommander ce qui existe déjà |
| Terminal intégré ou externe ? | Terminal VS Code, iTerm2, Windows Terminal, Alacritty… | Commandes de navigation |

### Réseau et accès

| Question | Quand la poser | Impact |
|---|---|---|
| Derrière un proxy d'entreprise ? | Si contexte pro | Config git, npm, pip, docker |
| Accès sudo / admin ? | Si install système nécessaire | Choix d'install user-level vs system |
| VPN actif ? | Si accès réseau requis | Ports, résolution DNS |

---

## Questions spécifiques par domaine

### Dev — Questions supplémentaires

| Question | Pourquoi |
|---|---|
| Version du langage installée ? | Compatibilité syntaxe, dépendances |
| Gestionnaire de paquets préféré ? (npm/yarn/pnpm, pip/poetry/uv…) | Adapter les commandes d'install |
| Utilises-tu un linter / formatter ? | Ne pas casser la config existante |
| Base de données disponible ? (PostgreSQL, MySQL, SQLite…) | Si le tuto nécessite du stockage |
| Git configuré ? (user.name, user.email) | Prérequis pour beaucoup de tutos |

### Ops — Questions supplémentaires

| Question | Pourquoi |
|---|---|
| Docker installé ? Quelle version ? | Fonctionnalités disponibles (buildx, compose v2…) |
| Accès à un registry ? (DockerHub, GHCR, ECR…) | Pour push d'images |
| CI/CD existant ? (GitHub Actions, GitLab CI, Jenkins…) | Adapter les exemples de pipeline |
| Cluster K8s disponible ? (minikube, kind, cloud…) | Choix de l'outil de dev local |
| Accès cloud ? (AWS, GCP, Azure, aucun) | Adapter le scope du tuto |

### Infra — Questions supplémentaires

| Question | Pourquoi |
|---|---|
| Accès SSH à un serveur distant ? | Si le tuto implique de la config serveur |
| Nom de domaine disponible ? | Pour les tutos DNS, certificats, reverse proxy |
| Firewall actif ? (ufw, iptables, firewalld) | Éviter les blocages réseau |
| Hyperviseur disponible ? (VirtualBox, VMware, Proxmox) | Si le tuto utilise des VMs |
| Terraform / Ansible déjà installé ? | Pour les tutos IaC |

---

## Matrice des valeurs par défaut

Si l'utilisateur ne répond pas à une question, utiliser ces valeurs par défaut
et l'annoncer clairement.

| Paramètre | Valeur par défaut | Justification |
|---|---|---|
| OS | Ubuntu LTS dernière version | Le plus courant en apprentissage IT |
| IDE | VS Code | Le plus répandu, extensions riches |
| Terminal | Terminal intégré VS Code | Simplifie le workflow |
| Niveau | Débutant | Mieux vaut trop expliquer que pas assez |
| Temps | 30 minutes | Bon équilibre profondeur/faisabilité |
| Contexte | Personnel | Exemples plus simples |
| Gestionnaire paquets Python | pip | Le plus universel |
| Gestionnaire paquets JS | npm | Installé avec Node.js |
