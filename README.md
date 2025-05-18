Feuerwehr Dashboard – Raspberry Pi Setup
Dieses Projekt richtet einen Raspberry Pi so ein, dass er als Dashboard mit Webserver, Firewall, Tailscale-VPN und Python-Backend genutzt werden kann.

Features
Automatisiertes Setup via Ansible (Webserver, Firewall, Tailscale, Python-Backend)
Klonen und Einrichten des Feuerwehr_Dashboard Repos
Systemd-Service für das Backend
Automatisches Deployment der Website-Dateien nach /var/www/html
Firewall-Rules für lokale und Tailscale-Zugriffe
Voraussetzungen
Raspberry Pi mit aktuellem Raspberry Pi OS (getestet auf Debian-basierten Systemen)
Ansible installiert (sudo apt install ansible)
SSH-Zugriff auf das Zielsystem (bei Remote-Setup)
Optional: Tailscale Key, falls VPN-Zugang erwünscht ist
Setup-Anleitung
Repository klonen

bash
git clone https://github.com/enderahd/Dashboard.git
cd Dashboard
Ansible Playbook ausführen

Mit eigenem Benutzer (lokal):
bash
ansible-playbook setup-server.yml -e "ansible_user_id=$(whoami)"
Mit Tailscale-Key:
bash
ansible-playbook setup-server.yml -e "ansible_user_id=$(whoami)" -e "tailscale_auth_key=tskey-..."
Hinweis:
Standardmäßig wird das Zielverzeichnis /home/<benutzer>/Feuerwehr_Dashboard benutzt.

Zugreifen

Das Backend läuft unter Port 5000 (per Tailscale oder im lokalen Netz erreichbar)
Die Webseite ist im Browser via http://<raspberrypi-ip>/ erreichbar
Playbook Übersicht (setup-server.yml)
System- und Paket-Update
Installation von:
nginx
git
ufw (Firewall)
curl
python3, python3-venv, python3-pip
Optionale Installation und Anmeldung bei Tailscale
Firewall-Regeln für SSH, Backend und Webserver
Klonen und Rechte-Setzen des Feuerwehr Dashboard-Repos
Einrichtung einer Python Virtual Environment & Installation der Abhängigkeiten
Systemd-Service für das Python-Backend
Kopieren der Web-Dateien nach /var/www/html
Neustart von nginx
Variablen im Playbook
github_repo: GitHub-Repo des Dashboards
target_dir: Zielverzeichnis für das geklonte Projekt
backend_entry: Name des Python-Backend-Skripts
backend_port: Port für das Backend (Standard: 5000)
tailscale_auth_key: Tailscale Auth-Key (optional)
html_target_dir: Verzeichnis für die Webseite (Standard: /var/www/html)
Lizenz
MIT License
