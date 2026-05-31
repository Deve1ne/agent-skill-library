# Templates de tutos — Infra

Structures pré-construites pour les tutoriels orientés infrastructure,
réseaux, serveurs et cloud.

---

## Template : Configurer un serveur web

```
📎 OBJECTIF DU TUTO
───────────────────
Sujet       : [Nginx / Apache / Caddy] — serveur web basique
On va faire : Installer et configurer un serveur web servant une page statique
Résultat    : Page accessible dans le navigateur via localhost ou IP
Durée       : 20-30 min
Prérequis   : Serveur Linux (local, VM ou VPS), accès sudo

ÉTAPES TYPE :
1. Installer le serveur web
2. Vérifier qu'il tourne (systemctl status, curl localhost)
3. Explorer les fichiers de config par défaut
4. Créer une page HTML simple dans le bon répertoire
5. Comprendre la config du virtual host / server block
6. Modifier la config pour servir notre page
7. Tester la config (nginx -t ou équivalent)
8. Recharger le service
9. Vérifier dans le navigateur
10. (Optionnel) Ajouter HTTPS avec Let's Encrypt / Caddy auto-TLS
```

---

## Template : Configurer un reverse proxy

```
📎 OBJECTIF DU TUTO
───────────────────
Sujet       : Reverse proxy avec [Nginx / Caddy / Traefik]
On va faire : Router le trafic d'un domaine/port vers une app backend
Résultat    : Requête sur port 80/443 → app sur port interne
Durée       : 20-30 min
Prérequis   : Serveur web installé, app backend qui tourne

ÉTAPES TYPE :
1. Lancer une app backend simple (ou un conteneur)
2. Vérifier qu'elle répond sur son port interne
3. Créer la config de reverse proxy
4. Expliquer chaque directive (proxy_pass, headers, timeouts)
5. Tester la config
6. Recharger et vérifier via le navigateur / curl
7. Ajouter les headers de sécurité basiques
8. (Optionnel) Configurer le HTTPS
9. (Optionnel) Load balancing entre 2 instances
```

---

## Template : Administration Linux de base

```
📎 OBJECTIF DU TUTO
───────────────────
Sujet       : [Gestion utilisateurs / Permissions / Processus / Logs / Cron]
On va faire : Maîtriser un aspect fondamental de l'admin Linux
Résultat    : Commandes maîtrisées, cas pratique réalisé
Durée       : 20-30 min
Prérequis   : Accès à un terminal Linux (natif, VM ou WSL)

ÉTAPES TYPE (exemple : gestion utilisateurs) :
1. Comprendre /etc/passwd et /etc/group (structure)
2. Créer un utilisateur (useradd / adduser)
3. Définir un mot de passe (passwd)
4. Ajouter à un groupe (usermod -aG)
5. Vérifier les appartenances (id, groups)
6. Tester les permissions avec su / sudo
7. Verrouiller / supprimer un utilisateur
8. Bonnes pratiques sécurité
```

---

## Template : SSH et accès distant

```
📎 OBJECTIF DU TUTO
───────────────────
Sujet       : SSH — configuration sécurisée
On va faire : Configurer SSH avec clés, durcir la config
Résultat    : Connexion SSH par clé sans mot de passe, config durcie
Durée       : 20-30 min
Prérequis   : 2 machines (locale + distante, ou VM)

ÉTAPES TYPE :
1. Vérifier que le serveur SSH tourne
2. Générer une paire de clés (ssh-keygen)
3. Copier la clé publique (ssh-copy-id)
4. Tester la connexion par clé
5. Configurer ~/.ssh/config pour simplifier les connexions
6. Durcir sshd_config (désactiver root login, désactiver password auth)
7. Recharger sshd et vérifier
8. (Optionnel) Configurer fail2ban
9. (Optionnel) Port knocking ou changement de port
```

---

## Template : DNS et noms de domaine

```
📎 OBJECTIF DU TUTO
───────────────────
Sujet       : DNS — comprendre et configurer
On va faire : Configurer des enregistrements DNS pour un domaine
Résultat    : Domaine qui pointe vers le bon serveur, vérifiable avec dig/nslookup
Durée       : 20-30 min
Prérequis   : Nom de domaine (ou utiliser /etc/hosts en local)

ÉTAPES TYPE :
1. Comprendre le fonctionnement du DNS (schéma résolution)
2. Les types d'enregistrements (A, AAAA, CNAME, MX, TXT, NS)
3. Utiliser dig / nslookup pour interroger le DNS
4. Configurer un enregistrement A (via registrar ou /etc/hosts)
5. Vérifier la propagation
6. Ajouter un CNAME pour un sous-domaine
7. (Optionnel) Configurer un enregistrement MX
8. (Optionnel) Ajouter un enregistrement TXT (SPF, DKIM)
9. Outils de diagnostic DNS (dig, drill, dog, nslookup)
```

---

## Template : Firewall et sécurité réseau

```
📎 OBJECTIF DU TUTO
───────────────────
Sujet       : Firewall avec [ufw / iptables / firewalld / nftables]
On va faire : Configurer des règles de filtrage réseau
Résultat    : Seuls les ports nécessaires sont ouverts
Durée       : 20-30 min
Prérequis   : Accès sudo sur un serveur Linux

ÉTAPES TYPE :
1. Vérifier l'état actuel du firewall
2. Comprendre la logique de filtrage (allow/deny, chaînes)
3. Politique par défaut : tout bloquer sauf SSH
4. Ouvrir les ports nécessaires (HTTP, HTTPS, app custom)
5. Vérifier les règles actives
6. Tester depuis l'extérieur (ou avec nmap en local)
7. Ajouter une règle de rate limiting (optionnel)
8. Sauvegarder les règles pour qu'elles survivent au reboot
9. Bonnes pratiques sécurité réseau
```

---

## Template : VPN

```
📎 OBJECTIF DU TUTO
───────────────────
Sujet       : VPN avec [WireGuard / OpenVPN]
On va faire : Monter un tunnel VPN entre 2 machines
Résultat    : Machines qui communiquent via le tunnel
Durée       : 30-45 min
Prérequis   : 2 machines (serveur + client), accès sudo

ÉTAPES TYPE :
1. Installer WireGuard / OpenVPN sur les deux machines
2. Comprendre le principe du tunnel (schéma)
3. Générer les clés (serveur + client)
4. Configurer le serveur
5. Configurer le client
6. Activer les interfaces
7. Tester le tunnel (ping IP privée)
8. Configurer le routage si nécessaire
9. Rendre la config persistante (systemd)
10. Vérifier la sécurité (pas de fuite DNS)
```

---

## Conventions pour les tutos Infra

### Sécurité avant tout
Tout tuto Infra doit mentionner les implications sécurité :
- Ne jamais exposer un service sans authentification
- Toujours configurer un firewall minimal
- Mentionner les logs à surveiller
- Rappeler les mises à jour de sécurité

### Diagrammes réseau
Pour les tutos impliquant du réseau, toujours inclure un schéma ASCII :
```
[Internet] → [Firewall:22,80,443] → [Reverse Proxy:80,443]
                                          ├→ [App1:8001]
                                          └→ [App2:8002]
                                               └→ [DB:5432] (interne uniquement)
```

### Commandes de diagnostic
Toujours inclure des commandes de diagnostic pour chaque service :
- `systemctl status [service]` — état du service
- `journalctl -u [service] -f` — logs en temps réel
- `ss -tlnp` ou `netstat -tlnp` — ports en écoute
- `curl -v localhost:[port]` — test de connectivité

### Rollback
Chaque modification de config serveur doit mentionner comment revenir
en arrière si ça casse. Toujours sauvegarder les fichiers avant modif :
```bash
sudo cp /etc/nginx/nginx.conf /etc/nginx/nginx.conf.bak
```
