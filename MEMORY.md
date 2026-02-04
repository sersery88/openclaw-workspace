# MEMORY.md - Long-Term Memory

_Last updated: 2026-02-04_

---

## Tech Stack & Tools

### Backend
- **Sprache:** Go oder Rust
- **Python/JS:** Nur wenn absolut nötig, sonst vermeiden
- **Prinzip:** Always latest, nie outdated Dependencies

### Frontend
- **Framework:** React/Next.js (immer latest!)
- **UI Library:** Shadcn/ui mit **Base UI** (NICHT Radix UI!)
- **Wichtig:** Chef war sehr explizit - KEIN Radix UI

### Sicherheit
- Top-Priorität bei allen Entscheidungen
- Nie kompromittieren für Convenience

### Recherche & Docs
- **Context7** nutzen für aktuelle Dokumentation
- **Immer aktuell:** Recherche auf aktuelles Datum ausrichten
- **Nie:** 2024/2025 Guides oder veraltete Stack-Overflow-Antworten

---

## Projekte

### Aktuell (2026-02-04)
- **Hotel IBE** (Internet Booking Engine)
- **Flight IBE**
- Flexible Struktur - Chef wechselt zwischen Projekten

### Repos auf GitHub (sersery88)
- `flight-ibe` - Rust Backend + React Frontend
- `ETG-Hotel-IBE` - Rust Backend + React Frontend
- `flight-ibe-go` - Go/Gin implementation
- `ETG-Hotel-IBE-Enterprise` - Next.js 16.0.1, FoundationDB, Temporal, etc.
- `moltbot` - Alter AI Assistant (kein Backup der Memory-Dateien)
- Weitere: `pinktravel-ota`, `dach-marketplace`, `amadeus-api-docs`

---

## Mein Verhalten & Principles

### Core
- **Proaktiv, nicht reaktiv** - Ich soll agieren, nicht nur antworten
- **Autonom arbeiten, aber kommunizieren** - Chef soll wissen was ich tue
- **Challenger-Modus:** Bessere Lösungen sofort ansprechen
- **Kein Yes-Man** - Wenn ich was besser weiß, sag ich's direkt

### Kommunikation mit Chef
- Locker, Kumpel-Basis (duzen)
- Sachlich aber humorvoll
- Direkt und ehrlich

### Tech-Präferenzen (Chefs Wünsche)
- Go oder Rust fürs Backend (kein Python/JS)
- React/Next.js + Shadcn/ui mit Base UI
- Immer latest Dependencies
- Sicherheit geht vor

---

## System-Konfiguration

### Telegram Bot
- **Bot:** @sersery_bot
- **User ID:** 577361584 (@johnbilking)
- **Mode:** Pairing aktiviert
- **Status:** ✅ Eingerichtet & funktioniert

### OpenClaw Setup
- **Host:** srv1304256
- **Workspace:** /root/.openclaw/workspace
- **Gateway:** Port 18789 (loopback)
- **Model:** openrouter/moonshotai/kimi-k2.5

---

## Lessons Learned

### 2026-02-04 - Erstes Setup
- Deinstallation löscht Workspace-Daten (kein Backup!)
- GitHub-Repo für Workspace ist Pflicht für Persistenz
- Pairing-Mode in Telegram braucht korrekte Config + Restart

### ⚠️ 2026-02-04 - KRITISCHER OpenClaw Bug
**Problem:** OpenClaw Deinstallation löscht `/root/clawd/` komplett - inklusive ALLER Projekte!
**Auswirkung:** Hotel IBE, Flight IBE und andere Projekte wurden gelöscht
**Lösung:** 
- Projekte NIEMALS in `/root/clawd/` speichern!
- Stattdessen: `/root/projects/` oder `/var/www/` verwenden
- Oder: Git-Workflow mit sofortigem Push nach jedem Commit

**Status:** Aktuell wird `pinktravel-hotel-ibe` neu geklont und aufgesetzt

---

## ToDo / Ideas

- [ ] Regelmäßige Git-Commits automatisieren?
- [ ] Skill für Context7 Recherche nutzen
- [ ] Hotel IBE & Flight IBE Architektur dokumentieren
- [ ] Projekt-Struktur umziehen nach `/root/projects/` (NICHT in `/root/clawd/`!)
