# Matrice de commandes par OS

Référence rapide pour adapter les commandes selon l'OS de l'utilisateur.

---

## Gestion de paquets

| Action | Debian/Ubuntu | Fedora/RHEL | Arch | macOS (Homebrew) | Windows (winget) |
|---|---|---|---|---|---|
| Mettre à jour la liste | `sudo apt update` | `sudo dnf check-update` | `sudo pacman -Sy` | `brew update` | `winget upgrade` |
| Installer un paquet | `sudo apt install pkg` | `sudo dnf install pkg` | `sudo pacman -S pkg` | `brew install pkg` | `winget install pkg` |
| Supprimer un paquet | `sudo apt remove pkg` | `sudo dnf remove pkg` | `sudo pacman -R pkg` | `brew uninstall pkg` | `winget uninstall pkg` |
| Chercher un paquet | `apt search pkg` | `dnf search pkg` | `pacman -Ss pkg` | `brew search pkg` | `winget search pkg` |
| Upgrade système | `sudo apt upgrade` | `sudo dnf upgrade` | `sudo pacman -Syu` | `brew upgrade` | `winget upgrade --all` |

---

## Chemins et répertoires

| Élément | Linux | macOS | Windows (WSL) | Windows natif |
|---|---|---|---|---|
| Home utilisateur | `/home/user` | `/Users/user` | `/home/user` (WSL) | `C:\Users\user` |
| Accès fichiers Windows depuis WSL | - | - | `/mnt/c/Users/user` | - |
| Fichiers temporaires | `/tmp` | `/tmp` | `/tmp` | `%TEMP%` |
| Config utilisateur | `~/.config/` | `~/.config/` ou `~/Library/` | `~/.config/` | `%APPDATA%` |
| Binaires locaux | `~/.local/bin` | `~/.local/bin` | `~/.local/bin` | `%LOCALAPPDATA%` |
| Variables d'env | `~/.bashrc` ou `~/.zshrc` | `~/.zshrc` | `~/.bashrc` | Panneau système |

---

## Gestion des services

| Action | systemd (Linux) | macOS | Windows |
|---|---|---|---|
| Démarrer | `sudo systemctl start svc` | `brew services start svc` | `net start svc` |
| Arrêter | `sudo systemctl stop svc` | `brew services stop svc` | `net stop svc` |
| Redémarrer | `sudo systemctl restart svc` | `brew services restart svc` | `net stop svc && net start svc` |
| Status | `sudo systemctl status svc` | `brew services list` | `sc query svc` |
| Activer au démarrage | `sudo systemctl enable svc` | `brew services start svc` (auto) | `sc config svc start=auto` |
| Logs | `journalctl -u svc -f` | `log show --predicate 'process == "svc"'` | Observateur d'événements |

---

## Réseau et diagnostic

| Action | Linux | macOS | Windows |
|---|---|---|---|
| Ports en écoute | `ss -tlnp` | `lsof -iTCP -sTCP:LISTEN` | `netstat -an \| findstr LISTEN` |
| Résolution DNS | `dig domain` ou `nslookup domain` | `dig domain` ou `nslookup domain` | `nslookup domain` |
| Ping | `ping -c 4 host` | `ping -c 4 host` | `ping host` |
| Traceroute | `traceroute host` | `traceroute host` | `tracert host` |
| IP locale | `ip addr show` ou `hostname -I` | `ifconfig \| grep inet` | `ipconfig` |
| Requête HTTP | `curl -v url` | `curl -v url` | `curl -v url` (si installé) |
| Test port ouvert | `nc -zv host port` | `nc -zv host port` | `Test-NetConnection host -Port port` (PowerShell) |

---

## Gestion des processus

| Action | Linux | macOS | Windows |
|---|---|---|---|
| Lister les processus | `ps aux` | `ps aux` | `tasklist` |
| Chercher un processus | `ps aux \| grep name` | `ps aux \| grep name` | `tasklist \| findstr name` |
| Tuer un processus | `kill PID` / `kill -9 PID` | `kill PID` / `kill -9 PID` | `taskkill /PID pid /F` |
| Process sur un port | `lsof -i :port` ou `fuser port/tcp` | `lsof -i :port` | `netstat -ano \| findstr :port` |
| Monitoring temps réel | `htop` ou `top` | `htop` ou `top` | `taskmgr` |

---

## Permissions et fichiers

| Action | Linux / macOS | Windows (PowerShell) |
|---|---|---|
| Changer permissions | `chmod 755 fichier` | `icacls fichier /grant user:F` |
| Changer propriétaire | `chown user:group fichier` | `icacls fichier /setowner user` |
| Créer un lien symbolique | `ln -s source dest` | `New-Item -ItemType SymbolicLink -Path dest -Target source` |
| Rendre exécutable | `chmod +x script.sh` | (pas nécessaire pour .ps1) |
| Voir les permissions | `ls -la` | `icacls fichier` |

---

## Ouvrir dans le navigateur

| OS | Commande |
|---|---|
| Linux (X11/Wayland) | `xdg-open http://localhost:8080` |
| macOS | `open http://localhost:8080` |
| WSL | `wslview http://localhost:8080` ou `explorer.exe http://localhost:8080` |
| Windows natif | `start http://localhost:8080` |

---

## Docker

| Action | Linux | macOS | Windows |
|---|---|---|---|
| Installation | `sudo apt install docker.io` | Docker Desktop (brew) | Docker Desktop (winget) |
| Lancer sans sudo | `sudo usermod -aG docker $USER` | (pas nécessaire) | (pas nécessaire) |
| Compose v2 | `docker compose` | `docker compose` | `docker compose` |
| Volumes natifs | chemin absolu Linux | chemin absolu macOS | `/mnt/c/...` dans WSL |

---

## Shells et profils

| OS | Shell par défaut | Fichier de profil | Recharger |
|---|---|---|---|
| Ubuntu/Debian | bash | `~/.bashrc` | `source ~/.bashrc` |
| Fedora/Arch | bash (souvent zsh) | `~/.bashrc` ou `~/.zshrc` | `source ~/.bashrc` |
| macOS | zsh | `~/.zshrc` | `source ~/.zshrc` |
| WSL | bash | `~/.bashrc` | `source ~/.bashrc` |

---

## Notes importantes

### WSL (Windows Subsystem for Linux)
- WSL2 est fortement recommandé (meilleure perf, support Docker natif)
- Les fichiers Windows sont accessibles via `/mnt/c/` mais les perfs I/O
  sont meilleures si on travaille dans le filesystem Linux (`~/`)
- Docker Desktop peut être intégré à WSL2
- Les ports exposés dans WSL sont accessibles depuis Windows sur localhost

### macOS (Apple Silicon vs Intel)
- Sur Apple Silicon (M1/M2/M3/M4), certaines images Docker nécessitent
  `--platform linux/amd64` ou une image ARM native
- Homebrew est installé dans `/opt/homebrew` (ARM) vs `/usr/local` (Intel)
- Rosetta 2 peut être nécessaire pour certains binaires x86
