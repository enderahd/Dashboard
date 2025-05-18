# Feuerwehr Dashboard – Raspberry Pi Setup

Mit diesem Projekt kann ein Raspberry Pi automatisiert als Dashboard mit Webserver (nginx), Firewall (ufw), Tailscale-VPN und Python-Backend eingerichtet werden.  
Das Setup erfolgt vollständig per **Ansible Playbook** und übernimmt die komplette Systemvorbereitung, das Klonen des eigentlichen Dashboard-Codes sowie das Konfigurieren aller notwendigen Systemdienste und Firewallregeln.

---

## 🚀 Features

- **Vollautomatisiertes Setup via Ansible:**
  - System- und Paketupdate
  - Installation von `nginx`, `git`, `ufw`, `curl`, `python3`, `python3-venv`, `python3-pip`
  - Optionale Installation und Anmeldung bei **Tailscale (VPN)**

- **Firewall-Konfiguration:**
  - SSH und Backend-Port nur via Tailscale-IP (wenn vorhanden) freigegeben
  - Backend-Port zusätzlich über lokale IP erreichbar
  - HTTP/HTTPS nur aus dem lokalen Subnetz erreichbar

- **Klonen & Einrichten des Repos [`Feuerwehr_Dashboard`](https://github.com/enderahd/Feuerwehr_Dashboard):**
  - Alte Versionen löschen
  - Dateirechte setzen
  - Python Virtual Environment einrichten
  - Python-Abhängigkeiten aus `requirements.txt` installieren

- **Systemd-Service für das Backend**
  - Automatischer Start des Python-Backends als Service

- **Automatisches Deployment der Webseite nach `/var/www/html`:**
  - Kopieren von HTML, CSS, JS, Bildern und Ausgabedateien
  - Setzen der passenden Rechte für `nginx`

---

## 🛠 Voraussetzungen

- Raspberry Pi mit aktuellem Raspberry Pi OS/Debian  
  *(getestet auf Debian-basierten Systemen)*
- Ansible installiert:

  ```bash
  sudo apt install ansible
  ```

- Lokaler Zugriff oder SSH-Zugriff auf das Zielsystem
- Optional: **Tailscale Auth-Key**, falls VPN-Zugang gewünscht

---

## ⚙️ Setup-Anleitung

### 1. Repository klonen

```bash
git clone https://github.com/enderahd/Dashboard.git
cd Dashboard
```

### 2. Ansible Playbook ausführen

#### Ohne Tailscale (lokal):

```bash
ansible-playbook setup-server.yml -e "ansible_user_id=$(whoami)"
```

#### Mit Tailscale-Key (empfohlen):

```bash
ansible-playbook setup-server.yml -e "ansible_user_id=$(whoami)" -e "tailscale_auth_key=tskey-..."
```

📌 *Hinweis:*  
Das Zielverzeichnis für das geklonte Dashboard ist standardmäßig:

```text
/home/<benutzer>/Feuerwehr_Dashboard
```

---

## 🌐 Zugriff nach der Installation

- **Backend:** erreichbar über Port `5000`  
  → via Tailscale oder im lokalen Netzwerk

- **Webseite:**  
  [http://<raspberrypi-ip>/](http://<raspberrypi-ip>/)

---

## 📜 Übersicht: setup-server.yml

1. System- und Paketupdate
2. Installation aller benötigten Pakete
3. (Optional) Tailscale installieren & konfigurieren
4. Lokale und Tailscale-IP/Subnetz ermitteln
5. Firewall-Regeln setzen:
   - SSH & Backend-Port: nur über Tailscale
   - Backend-Port auch lokal
   - HTTP/HTTPS nur lokal
6. Altes Projekt löschen und neu klonen
7. Dateirechte setzen
8. Python Virtualenv erstellen und Abhängigkeiten installieren
9. Systemd-Service für Backend-Skript einrichten
10. Webseite nach `/var/www/html` kopieren & Rechte setzen
11. nginx neu starten

---

## 🧰 Variablen im Playbook

| Variable           | Beschreibung                                                  | Standardwert                                              |
|--------------------|---------------------------------------------------------------|-----------------------------------------------------------|
| `github_repo`      | GitHub-Repo des Dashboards                                    | `https://github.com/enderahd/Feuerwehr_Dashboard.git`     |
| `target_dir`       | Zielverzeichnis für das geklonte Projekt                      | `/home/<benutzer>/Feuerwehr_Dashboard`                   |
| `backend_entry`    | Name des Python-Backendskripts                                 | `API_backend.py`                                          |
| `backend_port`     | Port für das Backend                                           | `5000`                                                    |
| `tailscale_auth_key` | Tailscale Auth-Key (optional)                                | *leer*                                                    |
| `html_target_dir`  | Zielverzeichnis für die Webseite                              | `/var/www/html`                                           |

---

## ❗ Troubleshooting & FAQ

### 🔸 Das Backend ist nicht erreichbar?

**Service-Status prüfen:**

```bash
systemctl status backend.service
```

**Firewall prüfen:**

```bash
sudo ufw status
```

👉 Falls **kein Tailscale-Key** angegeben wurde, ist Port 5000 nur lokal erreichbar.

---

### 🔸 Webseite lädt nicht / zeigt nur die nginx-Standardseite?

- Sicherstellen, dass die Dateien korrekt nach `/var/www/html` kopiert wurden
- nginx-Status prüfen:

```bash
sudo systemctl status nginx
```

---

### 🔸 Probleme mit Abhängigkeiten / Virtualenv?

- Existiert das Verzeichnis `venv`?

```bash
ls <target_dir>/venv
```

- Virtual Environment aktivieren und Abhängigkeiten manuell installieren:

```bash
source <target_dir>/venv/bin/activate
pip install -r <target_dir>/requirements.txt
```

---

### 🔸 Änderungen am Frontend werden nicht angezeigt?

- Dateien erneut nach `/var/www/html` kopieren
- Rechte prüfen
- nginx neu starten:

```bash
sudo systemctl restart nginx
```

---

## 📄 Lizenz

MIT License

---
