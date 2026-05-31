# Erreurs fréquentes et corrections

Guide de dépannage par domaine. Consulter quand l'utilisateur rencontre
une erreur pendant un tuto.

---

## Erreurs générales (tous domaines)

### Permissions

| Erreur | Cause probable | Correction |
|---|---|---|
| `Permission denied` | Pas les droits sur le fichier/dossier | `sudo` ou `chmod +x` / `chmod 755` |
| `EACCES: permission denied` (npm) | npm essaie d'écrire dans un dossier root | `npm config set prefix ~/.npm-global` + ajouter au PATH |
| `Operation not permitted` | Fichier immutable ou système protégé | Vérifier `lsattr`, désactiver SIP sur macOS si pertinent |

### Réseau

| Erreur | Cause probable | Correction |
|---|---|---|
| `Connection refused` | Le service ne tourne pas ou mauvais port | Vérifier `systemctl status` / `docker ps`, vérifier le port |
| `Address already in use` | Un autre process utilise le port | `lsof -i :PORT` puis `kill PID`, ou changer de port |
| `Name or service not known` | DNS ne résout pas | Vérifier la connexion, tester avec `dig`, vérifier `/etc/resolv.conf` |
| `Connection timed out` | Firewall, mauvaise IP, service down | Vérifier firewall (`ufw status`), ping, vérifier l'IP |
| `SSL certificate problem` | Certificat expiré/auto-signé | Mettre à jour les certificats, ou `-k` avec curl (dev only) |

### Environnement

| Erreur | Cause probable | Correction |
|---|---|---|
| `command not found` | Binaire pas dans le PATH | Vérifier l'install, ajouter au PATH dans `.bashrc`/`.zshrc` |
| `No such file or directory` | Mauvais chemin, fichier absent | Vérifier `ls`, vérifier le pwd avec `pwd` |
| `env: node: No such file` | Node pas installé ou pas dans PATH | Installer Node, vérifier `which node` |

---

## Erreurs Dev

### Python

| Erreur | Cause probable | Correction |
|---|---|---|
| `ModuleNotFoundError` | Module pas installé, mauvais venv | `pip install module`, vérifier `which python` |
| `SyntaxError: invalid syntax` | Mauvaise version Python (2 vs 3) | Vérifier `python --version`, utiliser `python3` |
| `IndentationError` | Mélange tabs/espaces | Configurer l'éditeur en espaces, reformater |
| `pip: command not found` | pip pas installé | `sudo apt install python3-pip` ou `python3 -m ensurepip` |
| `externally-managed-environment` | Debian/Ubuntu récent bloque pip système | Utiliser `--break-system-packages` ou créer un venv |

### JavaScript / Node.js

| Erreur | Cause probable | Correction |
|---|---|---|
| `Cannot find module` | Dépendance manquante | `npm install`, vérifier package.json |
| `SyntaxError: Unexpected token` | Mauvaise version Node ou syntaxe ES6 | Vérifier `node --version`, mettre à jour Node |
| `ENOSPC: System limit for number of file watchers` | Trop de watchers inotify | `echo fs.inotify.max_user_watches=524288 \| sudo tee -a /etc/sysctl.conf && sudo sysctl -p` |
| `ERR_MODULE_NOT_FOUND` | Import ESM vs CommonJS | Ajouter `"type": "module"` dans package.json ou utiliser `.mjs` |
| `node: --openssl-legacy-provider is not allowed` | Version Node trop récente pour l'outil | Mettre à jour l'outil ou utiliser nvm pour changer de version |

### Git

| Erreur | Cause probable | Correction |
|---|---|---|
| `fatal: not a git repository` | Pas dans un repo Git | `git init` ou se déplacer dans le bon dossier |
| `error: failed to push some refs` | Remote a des commits en avance | `git pull --rebase` puis `git push` |
| `merge conflict` | Modifications concurrentes | Ouvrir les fichiers marqués, résoudre, `git add` puis `git commit` |
| `detached HEAD` | Checkout sur un commit, pas une branche | `git checkout -b nouvelle-branche` ou `git checkout main` |

---

## Erreurs Ops

### Docker

| Erreur | Cause probable | Correction |
|---|---|---|
| `Cannot connect to the Docker daemon` | Docker pas démarré | `sudo systemctl start docker` |
| `permission denied while trying to connect to the Docker daemon socket` | User pas dans le groupe docker | `sudo usermod -aG docker $USER` puis re-login |
| `no matching manifest for linux/arm64` | Image pas dispo pour ARM (Apple Silicon) | Ajouter `--platform linux/amd64` ou chercher une image ARM |
| `port is already allocated` | Port déjà pris | `docker ps` pour voir qui utilise le port, arrêter ou changer |
| `OCI runtime create failed` | Image corrompue ou incompatible | `docker pull --no-cache` image, vérifier l'architecture |
| `COPY failed: file not found in build context` | Fichier hors du context Docker ou dans .dockerignore | Vérifier le chemin relatif, vérifier `.dockerignore` |
| `no space left on device` | Disque Docker plein | `docker system prune -a` (attention, supprime tout le cache) |

### Docker Compose

| Erreur | Cause probable | Correction |
|---|---|---|
| `service "x" depends on undefined service "y"` | Nom de service mal orthographié | Vérifier les noms dans docker-compose.yml |
| `Bind mount ... does not exist` | Dossier source n'existe pas | Créer le dossier avant `docker compose up` |
| `network xxx not found` | Réseau supprimé ou renommé | `docker compose down` puis `docker compose up` |

### Kubernetes

| Erreur | Cause probable | Correction |
|---|---|---|
| `ImagePullBackOff` | Image introuvable ou erreur d'auth au registry | Vérifier le nom de l'image, les credentials du registry |
| `CrashLoopBackOff` | Le conteneur crash en boucle | `kubectl logs pod-name` pour voir l'erreur |
| `Pending` (pod) | Pas assez de ressources ou pas de node compatible | `kubectl describe pod pod-name` pour voir les events |
| `connection refused` (kubectl) | Cluster pas démarré | Démarrer minikube/kind, vérifier le kubeconfig |

### CI/CD

| Erreur | Cause probable | Correction |
|---|---|---|
| Pipeline ne se déclenche pas | Mauvais trigger, fichier yml mal placé | Vérifier le chemin (`.github/workflows/`, `.gitlab-ci.yml`) |
| `Permission denied` dans le pipeline | Droits insuffisants sur le runner | Vérifier les permissions du token / service account |
| Cache invalide | Clé de cache changée | Nettoyer le cache, vérifier la clé |

---

## Erreurs Infra

### SSH

| Erreur | Cause probable | Correction |
|---|---|---|
| `Permission denied (publickey)` | Clé pas acceptée par le serveur | Vérifier `~/.ssh/authorized_keys` sur le serveur, permissions 600 |
| `Host key verification failed` | Fingerprint du serveur a changé | Supprimer l'ancienne entrée dans `~/.ssh/known_hosts` |
| `Connection refused` sur port 22 | sshd pas démarré ou port changé | Vérifier `systemctl status sshd`, vérifier le port dans `sshd_config` |
| `Too many authentication failures` | Trop de clés SSH essayées | Spécifier la clé : `ssh -i ~/.ssh/key_specifique user@host` |

### Nginx

| Erreur | Cause probable | Correction |
|---|---|---|
| `nginx: [emerg] bind() to 0.0.0.0:80 failed` | Port 80 déjà utilisé (Apache ?) | `sudo lsof -i :80`, arrêter le service concurrent |
| `502 Bad Gateway` | Backend ne répond pas | Vérifier que l'app tourne, vérifier le proxy_pass |
| `413 Request Entity Too Large` | Upload trop gros | Ajouter `client_max_body_size 10M;` dans la config |
| `nginx -t` échoue | Erreur de syntaxe dans la config | Lire le message d'erreur, vérifier les `;` et `{}` |

### DNS

| Erreur | Cause probable | Correction |
|---|---|---|
| `NXDOMAIN` | Le domaine n'existe pas dans le DNS | Vérifier l'enregistrement chez le registrar |
| `SERVFAIL` | Le serveur DNS a un problème | Essayer un autre résolveur (8.8.8.8, 1.1.1.1) |
| Propagation lente | TTL long sur l'ancien enregistrement | Attendre le TTL, ou tester avec `dig @8.8.8.8 domain` |

### Firewall

| Erreur | Cause probable | Correction |
|---|---|---|
| Service inaccessible de l'extérieur | Port pas ouvert dans le firewall | `sudo ufw allow PORT` ou équivalent |
| Bloqué après modif firewall | Règle SSH supprimée par erreur | Accéder via console physique/VPS console, `ufw allow 22` |

---

## Processus de diagnostic général

Quand une erreur n'est pas dans cette liste, suivre ce processus :

1. **Lire le message d'erreur en entier** — souvent la réponse est dedans
2. **Vérifier les logs** — `journalctl -u service -n 50`, `docker logs container`
3. **Vérifier les versions** — `tool --version`, comparer avec la doc
4. **Vérifier l'état du service** — `systemctl status`, `docker ps`
5. **Vérifier le réseau** — `curl -v`, `ping`, `ss -tlnp`
6. **Chercher l'erreur exacte** — web search avec le message entre guillemets
7. **Vérifier les issues GitHub** du projet concerné
