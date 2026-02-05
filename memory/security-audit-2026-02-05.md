# 🔒 Security Audit Report — srv1304306

**Date:** 2026-02-05  
**Server:** srv1304306 (76.13.129.60)  
**OS:** Ubuntu 25.10 (Questing Quokka)  
**Kernel:** 6.17.0-12-generic (x64)

---

## ✅ Zusammenfassung

| Bereich | Status | Details |
|---------|--------|---------|
| UFW Firewall | ✅ Aktiv | 5 Ports offen, deny incoming default |
| fail2ban | ✅ Aktiv | 5 Jails (SSH + 4x Nginx) |
| SSH Hardening | ✅ Gehärtet | prohibit-password, no PasswordAuth, MaxAuthTries 5 |
| Lynis Audit | ✅ Score 76/100 | 0 Warnings, 17 Suggestions |
| .env Sicherheit | ✅ Behoben | Permissions 644→600 korrigiert |

---

## 1. UFW Firewall

### Konfiguration
- **Default Incoming:** DENY
- **Default Outgoing:** ALLOW

### Offene Ports
| Port | Protokoll | Zugang | Verwendung |
|------|-----------|--------|------------|
| 22/tcp | TCP | Anywhere | SSH |
| 80/tcp | TCP | Anywhere | HTTP (Nginx) |
| 443/tcp | TCP | Anywhere | HTTPS (Nginx) |
| 8082/tcp | TCP | Anywhere | Hotel IBE Dev |
| 18789/tcp | TCP | 127.0.0.1 only | OpenClaw Gateway |

### ⚠️ Vorherige Regeln gefunden
Die folgenden Regeln existierten bereits **vor** dem Hardening:
- **Port 3000/tcp** — ALLOW von Anywhere (Node.js Prozess, PID 1052)
- **Port 8080/tcp** — ALLOW von Anywhere (kein aktiver Listener)

**Empfehlung:** 
- Port 3000: Prüfen ob nötig — falls nur lokal gebraucht, mit `ufw delete` entfernen
- Port 8080: Kein Dienst aktiv → kann entfernt werden: `ufw delete allow 8080/tcp`

---

## 2. fail2ban

### Aktive Jails (5)
| Jail | MaxRetry | Bantime | Status |
|------|----------|---------|--------|
| sshd | 5 | 3600s (1h) | ✅ Aktiv |
| nginx-http-auth | 5 | 3600s | ✅ Aktiv |
| nginx-botsearch | 10 | 3600s | ✅ Aktiv |
| nginx-bad-request | 10 | 3600s | ✅ Aktiv |
| nginx-forbidden | 5 | 3600s | ✅ Aktiv |

### SSH Jail Statistiken (vor Änderung)
- Total Failed: 14
- Total Banned: 4
- Currently Banned: 0

### Konfigurationsdatei
`/etc/fail2ban/jail.local` — neu erstellt/aktualisiert

---

## 3. SSH Hardening

### Aktuelle Konfiguration
| Setting | Wert | Quelle |
|---------|------|--------|
| PermitRootLogin | prohibit-password | /etc/ssh/sshd_config |
| PasswordAuthentication | no | sshd_config.d/50-cloud-init.conf + 60-cloudimg-settings.conf |
| KbdInteractiveAuthentication | no | /etc/ssh/sshd_config |
| MaxAuthTries | 5 | sshd_config.d/99-hardening.conf (NEU) |
| X11Forwarding | no | /etc/ssh/sshd_config |
| UsePAM | yes | /etc/ssh/sshd_config |

### Änderungen durchgeführt
- ✅ `/etc/ssh/sshd_config.d/99-hardening.conf` erstellt mit `MaxAuthTries 5`
- ✅ SSH Konfiguration validiert (`sshd -t` → OK)
- ✅ SSH Service reloaded

### ⚠️ WARNUNG: Keine SSH Keys!
```
/root/.ssh/authorized_keys → LEER (0 Bytes)
/home/ubuntu/.ssh/authorized_keys → LEER (0 Bytes)
```
**PasswordAuthentication ist "no" und keine Keys vorhanden!**  
SSH Login ist nur über den Provider-Console möglich.  

**Dringende Empfehlung:** SSH Public Key hinzufügen!
```bash
echo "ssh-ed25519 AAAA... user@host" >> /root/.ssh/authorized_keys
```

---

## 4. Lynis Security Audit

### Ergebnis
- **Hardening Index: 76/100** ⬛⬛⬛⬛⬛⬛⬛⬛⬛⬛⬛⬛⬛⬛⬛⬜⬜⬜⬜⬜
- **Tests durchgeführt:** 261
- **Warnings:** 0 ✅
- **Suggestions:** 17

### Top-Empfehlungen (priorisiert)

#### 🔴 Hoch
1. **[BOOT-5122]** GRUB Boot Loader Passwort setzen — verhindert unauthorized Boot-Änderungen
2. **[AUTH-9328]** Default umask von 022 auf 027 verschärfen
3. **[FINT-4350]** File Integrity Tool installieren (aide/tripwire)
4. **[HRDN-7222]** Compiler-Zugang einschränken (nur root)

#### 🟡 Mittel
5. **[HTTP-6710]** HTTPS/SSL Settings härten (Nginx)
6. **[AUTH-9229]** PAM Konfiguration — Password Hashing Rounds erhöhen
7. **[AUTH-9284]** Gesperrte Accounts prüfen und ggf. entfernen
8. **[LOGG-2154]** External Logging einrichten (syslog remote)
9. **[LOGG-2190]** Gelöschte Dateien in Benutzung prüfen
10. **[BOOT-5264]** Systemd Services härten

#### 🟢 Niedrig
11. **[DEB-0810]** apt-listbugs installieren
12. **[DEB-0811]** apt-listchanges installieren
13. **[BOOT-5180]** Runlevel und Startup-Services prüfen
14. **[FILE-6310]** /home und /var auf separate Partitionen (VPS-typisch nicht möglich)
15. **[TOOL-5002]** Automation Tools prüfen

### Lynis Logs
- Full report: `/var/log/lynis-report.dat`
- Debug log: `/var/log/lynis.log`

---

## 5. .env Sicherheit

### Gefundene .env Dateien
| Datei | Permissions (vorher) | Permissions (nachher) | .gitignore |
|-------|---------------------|----------------------|------------|
| `/root/.openclaw/workspace/skills/context7/.env` | 644 ⚠️ | 600 ✅ | ❌ nicht in .gitignore |
| `/root/ETG-Hotel-IBE/.env` | 644 ⚠️ | 600 ✅ | ✅ in .gitignore |
| `/root/.cargo/registry/src/.../sqlx-0.8.6/tests/.env` | — | — | (Cargo cache, unkritisch) |

### Änderungen
- ✅ Beide .env Dateien auf 600 gesetzt (nur root lesbar)

### Empfehlung
- `.env` in `/root/.openclaw/workspace/.gitignore` eintragen (falls git repo)

---

## 6. Zusätzliche Erkenntnisse

### Aktive Dienste auf offenen Ports
| Port | Dienst | PID | Zugang |
|------|--------|-----|--------|
| 22 | sshd | 1711 | Extern |
| 80 | nginx | 1730 | Extern |
| 443 | nginx | 1730 | Extern |
| 3000 | node | 1052 | Extern ⚠️ |
| 8082 | nginx | 1730 | Extern |
| 9600 | docker-proxy | 2780 | Localhost ✅ |
| 18789 | openclaw-gateway | 719930 | Localhost ✅ |

### Pending System Updates (17 Pakete)
- azure-cli, docker-ce (+ cli, rootless), libldap, systemd-Stack, udev
- **Empfehlung:** `apt upgrade -y` zeitnah durchführen

### Unattended Upgrades
- ✅ Installiert und aktiv
- Auto-Update-Package-Lists: 1 (täglich)
- Unattended-Upgrade: 1 (aktiv)

---

## 🎯 Nächste Schritte (Empfohlen)

1. **🔴 DRINGEND:** SSH Key hinterlegen (`/root/.ssh/authorized_keys`)
2. **🟡 UFW aufräumen:** Port 3000 und 8080 Regeln prüfen/entfernen
3. **🟡 System Update:** `apt upgrade -y` (17 pending Pakete)
4. **🟡 Nginx SSL Hardening:** TLS 1.3, HSTS, OCSP Stapling
5. **🟢 File Integrity:** `aide` oder `tripwire` installieren
6. **🟢 umask verschärfen:** 022 → 027 in `/etc/login.defs`

---

## Durchgeführte Änderungen (Changelog)

| Zeitpunkt | Aktion | Details |
|-----------|--------|---------|
| 09:10 | UFW aktiviert | Default deny incoming, 5 Ports geöffnet, 18789 nur localhost |
| 09:11 | fail2ban erweitert | jail.local mit 5 Jails (SSH + 4x Nginx) |
| 09:12 | SSH gehärtet | MaxAuthTries 5 via 99-hardening.conf, SSH reloaded |
| 09:15 | Lynis Audit | Score 76/100, 0 Warnings, 17 Suggestions |
| 09:16 | .env gesichert | 2 Dateien 644→600 |

---

*Report generiert am 2026-02-05 ~09:20 UTC*
