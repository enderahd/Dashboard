
Feuerwehr Dashboard – Raspberry Pi Setup
Mit diesem Projekt kann ein Raspberry Pi automatisiert als Dashboard mit Webserver (nginx), Firewall (ufw), Tailscale-VPN und Python-Backend eingerichtet werden. Das Setup erfolgt vollständig per Ansible Playbook und übernimmt die komplette Systemvorbereitung, das Klonen des eigentlichen Dashboard-Codes sowie das Konfigurieren aller notwendigen Systemdienste und Firewallregeln.

Features
Vollautomatisiertes Setup via Ansible:
System- und Paketupdate
Installation von nginx, git, ufw (Firewall), curl, python3, python3-venv, python3-pip
Optionale Installation und Anmeldung bei Tailscale (VPN)
Firewall-Konfiguration:
SSH und Backend-Port nur via Tailscale-IP (wenn vorhanden) freigegeben
Backend-Port zusätzlich über lokale IP erreichbar
HTTP/HTTPS nur aus dem lokalen Subnetz erreichbar
Klonen & Einrichten des Feuerwehr_Dashboard Repos
Löschen evtl. vorhandener alter Versionen
Setzen der Dateirechte auf den aktuellen Benutzer
Anlegen und Einrichten einer Python Virtual Environment
Installieren aller Python-Abhängigkeiten aus requirements.txt
Systemd-Service für das Backend
Automatischer Start des Python-Backends als Service
Automatisches Deployment der Website-Dateien nach /var/www/html
Kopieren aller nötigen Dateien (HTML, CSS, JS, Bilder, Ausgabedateien)
Setzen der Dateirechte für nginx
Voraussetzungen
Raspberry Pi mit aktuellem Raspberry Pi OS/Debian (getestet auf Debian-basierten Systemen)
Ansible installiert (sudo apt install ansible)
Lokaler Zugriff oder SSH-Zugriff auf das Zielsystem
Optional: Tailscale Auth-Key, falls VPN-Zugang gewünscht
Setup-Anleitung
Repository klonen

bash
git clone https://github.com/enderahd/Dashboard.git
cd Dashboard
Ansible Playbook ausführen

Mit eigenem Benutzer (lokal):

bash
ansible-playbook setup-server.yml -e "ansible_user_id=$(whoami)"
Mit Tailscale-Key (empfohlen):

bash
ansible-playbook setup-server.yml -e "ansible_user_id=$(whoami)" -e "tailscale_auth_key=tskey-..."
Hinweis:
Das Zielverzeichnis für das geklonte Dashboard ist standardmäßig /home/<benutzer>/Feuerwehr_Dashboard.
Der Tailscale-Key ist optional; ohne ihn wird VPN nicht aktiviert.

Zugriff nach Installation
Das Backend läuft auf Port 5000 (erreichbar über Tailscale oder im lokalen Netz)
Die Webseite ist im Browser unter http://<raspberrypi-ip>/ erreichbar
Playbook Übersicht (setup-server.yml)
System- und Paket-Update
Installation aller benötigten Pakete
Installation & Konfiguration von Tailscale (optional)
Ermitteln und Setzen der lokalen sowie Tailscale-IP/Subnetz
Aktivieren & Konfigurieren der Firewall mit spezifischen Regeln:
SSH und Backend-Port nur via Tailscale-IP (wenn vorhanden) freigegeben
Backend-Port zusätzlich über lokale IP erreichbar
HTTP/HTTPS nur aus dem lokalen Subnetz erreichbar
Löschen alter Projektversionen, neu klonen des Dashboard-Repos
Setzen aller Dateirechte
Erstellen einer Python Virtual Environment & Installieren der Abhängigkeiten
Anlegen & Starten eines systemd-Service für das Backend-Skript
Kopieren aller Website-Dateien nach /var/www/html und setzen der Rechte
Neustart von nginx
Variablen im Playbook
github_repo: GitHub-Repo des Dashboards (Standard: https://github.com/enderahd/Feuerwehr_Dashboard.git)
target_dir: Zielverzeichnis für das geklonte Projekt (Standard: /home/<benutzer>/Feuerwehr_Dashboard)
backend_entry: Name des Python-Backend-Skripts (Standard: API_backend.py)
backend_port: Port für das Backend (Standard: 5000)
tailscale_auth_key: Tailscale Auth-Key (optional, Standard: leer)
html_target_dir: Verzeichnis für die Webseite (Standard: /var/www/html)
Troubleshooting & FAQ
Das Backend ist nicht erreichbar
Prüfe, ob der systemd-Service läuft:
bash
systemctl status backend.service
Prüfe, ob die Firewall ggf. Ports blockiert:
bash
sudo ufw status
Wurde der Tailscale-Key korrekt angegeben? Ohne VPN kann Backend-Port 5000 nur lokal oder im Subnetz erreichbar sein.
Die Webseite lädt nicht / zeigt nur eine nginx-Defaultseite
Stelle sicher, dass die Dateien aus dem geklonten Repository nach /var/www/html kopiert wurden.
Prüfe, ob nginx läuft:
bash
sudo systemctl status nginx
Probleme mit Abhängigkeiten/Virtualenv
Prüfe, ob das Verzeichnis <target_dir>/venv existiert.
Führe ggf. erneut das Playbook aus oder installiere die Abhängigkeiten manuell:
bash
source <target_dir>/venv/bin/activate
pip install -r <target_dir>/requirements.txt
Änderungen am Frontend werden nicht angezeigt
Stelle sicher, dass die Dateien nach /var/www/html kopiert und die Rechte korrekt gesetzt wurden.
Starte ggf. nginx neu:
bash
sudo systemctl restart nginx
Lizenz
MIT License

Möchtest du noch weitere Fragen oder typische Stolperfallen aufnehmen? Sag gerne Bescheid!

so das ich es in des README.md kopieren kann den ganzen text 
Natürlich! Hier ist der komplette Text, den du direkt in deine README.md kopieren kannst. Er enthält alle gewünschten Ergänzungen (inkl. Troubleshooting & FAQ):

Markdown
# Feuerwehr Dashboard – Raspberry Pi Setup

Mit diesem Projekt kann ein Raspberry Pi automatisiert als Dashboard mit Webserver (nginx), Firewall (ufw), Tailscale-VPN und Python-Backend eingerichtet werden. Das Setup erfolgt vollständig per Ansible Playbook und übernimmt die komplette Systemvorbereitung, das Klonen des eigentlichen Dashboard-Codes sowie das Konfigurieren aller notwendigen Systemdienste und Firewallregeln.

## Features

- **Vollautomatisiertes Setup via Ansible:**
  - System- und Paketupdate
  - Installation von nginx, git, ufw (Firewall), curl, python3, python3-venv, python3-pip
  - Optionale Installation und Anmeldung bei Tailscale (VPN)
- **Firewall-Konfiguration:**
  - SSH und Backend-Port nur via Tailscale-IP (wenn vorhanden) freigegeben
  - Backend-Port zusätzlich über lokale IP erreichbar
  - HTTP/HTTPS nur aus dem lokalen Subnetz erreichbar
- **Klonen & Einrichten des [Feuerwehr_Dashboard](https://github.com/enderahd/Feuerwehr_Dashboard) Repos**
  - Löschen evtl. vorhandener alter Versionen
  - Setzen der Dateirechte auf den aktuellen Benutzer
  - Anlegen und Einrichten einer Python Virtual Environment
  - Installieren aller Python-Abhängigkeiten aus requirements.txt
- **Systemd-Service für das Backend**
  - Automatischer Start des Python-Backends als Service
- **Automatisches Deployment der Website-Dateien nach /var/www/html**
  - Kopieren aller nötigen Dateien (HTML, CSS, JS, Bilder, Ausgabedateien)
  - Setzen der Dateirechte für nginx

## Voraussetzungen

- Raspberry Pi mit aktuellem Raspberry Pi OS/Debian (getestet auf Debian-basierten Systemen)
- Ansible installiert (`sudo apt install ansible`)
- Lokaler Zugriff oder SSH-Zugriff auf das Zielsystem
- Optional: Tailscale Auth-Key, falls VPN-Zugang gewünscht

## Setup-Anleitung

1. **Repository klonen**

   ```bash
   git clone https://github.com/enderahd/Dashboard.git
   cd Dashboard
Ansible Playbook ausführen

Mit eigenem Benutzer (lokal):

bash
ansible-playbook setup-server.yml -e "ansible_user_id=$(whoami)"
Mit Tailscale-Key (empfohlen):

bash
ansible-playbook setup-server.yml -e "ansible_user_id=$(whoami)" -e "tailscale_auth_key=tskey-..."
Hinweis:
Das Zielverzeichnis für das geklonte Dashboard ist standardmäßig /home/<benutzer>/Feuerwehr_Dashboard.
Der Tailscale-Key ist optional; ohne ihn wird VPN nicht aktiviert.

Zugriff nach Installation
Das Backend läuft auf Port 5000 (erreichbar über Tailscale oder im lokalen Netz)
Die Webseite ist im Browser unter http://<raspberrypi-ip>/ erreichbar
Playbook Übersicht (setup-server.yml)
System- und Paket-Update
Installation aller benötigten Pakete
Installation & Konfiguration von Tailscale (optional)
Ermitteln und Setzen der lokalen sowie Tailscale-IP/Subnetz
Aktivieren & Konfigurieren der Firewall mit spezifischen Regeln:
SSH und Backend-Port nur via Tailscale-IP (wenn vorhanden) freigegeben
Backend-Port zusätzlich über lokale IP erreichbar
HTTP/HTTPS nur aus dem lokalen Subnetz erreichbar
Löschen alter Projektversionen, neu klonen des Dashboard-Repos
Setzen aller Dateirechte
Erstellen einer Python Virtual Environment & Installieren der Abhängigkeiten
Anlegen & Starten eines systemd-Service für das Backend-Skript
Kopieren aller Website-Dateien nach /var/www/html und setzen der Rechte
Neustart von nginx
Variablen im Playbook
github_repo: GitHub-Repo des Dashboards (Standard: https://github.com/enderahd/Feuerwehr_Dashboard.git)
target_dir: Zielverzeichnis für das geklonte Projekt (Standard: /home/<benutzer>/Feuerwehr_Dashboard)
backend_entry: Name des Python-Backend-Skripts (Standard: API_backend.py)
backend_port: Port für das Backend (Standard: 5000)
tailscale_auth_key: Tailscale Auth-Key (optional, Standard: leer)
html_target_dir: Verzeichnis für die Webseite (Standard: /var/www/html)
Troubleshooting & FAQ
Das Backend ist nicht erreichbar
Prüfe, ob der systemd-Service läuft:
bash
systemctl status backend.service
Prüfe, ob die Firewall ggf. Ports blockiert:
bash
sudo ufw status
Wurde der Tailscale-Key korrekt angegeben? Ohne VPN kann Backend-Port 5000 nur lokal oder im Subnetz erreichbar sein.
Die Webseite lädt nicht / zeigt nur eine nginx-Defaultseite
Stelle sicher, dass die Dateien aus dem geklonten Repository nach /var/www/html kopiert wurden.
Prüfe, ob nginx läuft:
bash
sudo systemctl status nginx
Probleme mit Abhängigkeiten/Virtualenv
Prüfe, ob das Verzeichnis <target_dir>/venv existiert.
Führe ggf. erneut das Playbook aus oder installiere die Abhängigkeiten manuell:
bash
source <target_dir>/venv/bin/activate
pip install -r <target_dir>/requirements.txt
Änderungen am Frontend werden nicht angezeigt
Stelle sicher, dass die Dateien nach /var/www/html kopiert und die Rechte korrekt gesetzt wurden.
Starte ggf. nginx neu:
bash
sudo systemctl restart nginx
Lizenz
MIT License
