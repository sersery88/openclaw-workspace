# MEMORY.md - Long-Term Memory

_Last updated: 2026-02-05 (evening)_

---

## Tech Stack & Tools

### Backend
- **Sprache:** Go oder Rust
- **Python/JS:** Nur wenn absolut nötig, sonst vermeiden
- **Prinzip:** Always latest, nie outdated Dependencies

### Frontend
- **Framework:** React/Next.js 16.1.6, React 19 (immer latest!)
- **UI Library:** Shadcn/ui mit **Base UI** (`@base-ui-components/react`) — NICHT Radix UI!
- **Wichtig:** Chef war sehr explizit - KEIN Radix UI

### Sicherheit
- Top-Priorität bei allen Entscheidungen
- Nie kompromittieren für Convenience
- Lynis Score: 76/100
- UFW, fail2ban, SSH key-only aktiv

### Recherche & Docs
- **Context7** nutzen für aktuelle Dokumentation
- **Immer aktuell:** Recherche auf aktuelles Datum ausrichten
- **Nie:** 2024/2025 Guides oder veraltete Stack-Overflow-Antworten

---

## Projekte

### Hotel IBE (Hauptprojekt)
- **Pfad:** `/root/ETG-Hotel-IBE/` (NICHT in `/root/clawd/`!)
- **GitHub:** `sersery88/ETG-Hotel-IBE`
- **Frontend:** Next.js 16.1.6, React 19, Base UI, port 3001
- **Backend:** Rust (Axum), port 8081
- **Datenbank:** PostgreSQL (User-Daten), OpenSearch (Hotel-Daten), Redis (Cache)
- **Test-URL:** `http://76.13.129.60:8082` (Nginx proxy)
- **Prod-URL:** `https://hotelpink.ch` (aktuell Maintenance-Modus)
- **Payment:** Saferpay JSON API 1.49 (Production, Customer 363778)
  - Kreditkarte (VISA, Mastercard, Amex, Maestro, Apple Pay, Google Pay) — 0% Fee
  - TWINT — 1.6% Fee
  - Reka — 3.5% Fee
- **Email:** SMTP via Hostpoint (no-reply@hotelpink.ch, asmtp.mail.hostpoint.ch:587)
- **i18n:** 1111 Keys in de.json + en.json, i18next + react-i18next
- **Docker Compose:** 6 Services (OpenSearch, PostgreSQL, Redis, Dashboards, Umami, Umami-DB)

### Hotel IBE — OpenSearch
- **Indices:** `hotels_de` (3.30M docs, ~31GB), `hotels_en` (3.30M docs, ~31GB), `regions_de`/`regions_en` (250K each)
- **Sprachspezifisch:** Separate Indices pro Sprache (nicht suffixed fields)
- **Performance:** Ø 0.71ms/query
- **ETG API:** RATEHAWK_BASE_URL=`https://api.worldota.net`, KEY_ID=16183
- **ETG Indexing Rules:**
  - ✅ Indexierbar: Name, Adresse, Amenities, Policies
  - ✅ POIs und Reviews BEHALTEN (Chef-Entscheidung 2026-02-05)
  - ❌ NICHT indexierbar: Photos, Descriptions
- **Dump Streaming:** Zstd → `/tmp/` oder `/var/tmp/`, line-by-line decompress, 4MB buffer, 3000-hotel batches
- **inventory=all**: ALLE Hotels (Chef-Entscheidung 2026-02-05, NICHT preferable!)
- **Indexierte Felder (final nach Re-Index 2026-02-05):** name, address, location (geo_point!), star_rating, description_struct, description (synthetisiert), metapolicy_struct, metapolicy_extra_info, contact (email+phone), amenities (keyword) + amenity_groups (structured), images + images_ext (with category_slug), room_groups (nested, with room_group_id), kind, facts (renamed: rooms_quantity, floors_quantity, year_opened), serp_filters (keyword), payment_methods, check_in/out, hotel_chain, is_active, guest_rating, guest_review_count, guest_detailed_ratings
- **NICHT indexiert (Chef-Entscheidung):** policy_struct, deleted flag
- **Mapping-Highlights:** location=geo_point, amenities/serp_filters=keyword, room_groups=nested
- **Reindex Script:** `/root/ETG-Hotel-IBE/scripts/reindex-hotels.sh`

### Flight IBE
- **Status:** Nicht aktiv, wartet
- **Repos:** `sersery88/flight-ibe` (Rust), `sersery88/flight-ibe-go` (Go/Gin)

### Weitere Repos (sersery88)
- `ETG-Hotel-IBE-Enterprise` - Next.js 16.0.1, FoundationDB, Temporal
- `moltbot` - Alter AI Assistant (kein Backup der Memory-Dateien)
- `pinktravel-ota`, `dach-marketplace`, `amadeus-api-docs`

---

## Server & Infrastruktur

### VPS
- **Host:** srv1304306 (Hostinger)
- **IP:** 76.13.129.60
- **OS:** Ubuntu 25.10
- **RAM:** 32GB
- **Disk:** 387GB
- **Swap:** 8GB

### Nginx
- **Maintenance:** `/etc/nginx/sites-enabled/hotelpink` → hotelpink.ch
- **IBE Proxy:** `/etc/nginx/sites-enabled/hotel-ibe` → port 8082 → localhost:3001/8081
- **Optimiert:** gzip all MIME types, worker_connections 2048, epoll, SSL cipher preference

### Security
- UFW: 22/80/443/8082 open
- fail2ban aktiv
- SSH key-only
- Postfix localhost-only
- OpenClaw credentials chmod 700

### Startbefehle
- **Frontend:** `cd /root/ETG-Hotel-IBE/frontend-next && node node_modules/next/dist/bin/next start -H 0.0.0.0 -p 3001`
- **Backend:** `/root/ETG-Hotel-IBE/hotel-backend` (ELF 64-bit, port 8081)
- **Migration Tool:** `/root/ETG-Hotel-IBE/crates/migration-tool/`

---

## Mein Verhalten & Principles

### Core
- **Proaktiv, nicht reaktiv** - Ich soll agieren, nicht nur antworten
- **Autonom arbeiten, aber kommunizieren** - Chef soll wissen was ich tue
- **Challenger-Modus:** Bessere Lösungen sofort ansprechen
- **Kein Yes-Man** - Wenn ich was besser weiß, sag ich's direkt
- **Subagenten für Arbeit, Main für Kommunikation** - Schwere Arbeit (Code, Builds, Recherche) IMMER an Subagenten delegieren. Main Session bleibt frei und responsive für Chef.
- **Disk Streaming:** Nie ganze Dumps in RAM laden; line-by-line von Disk, max 80% Disk-Nutzung
- **Alles sprachspezifisch:** UI auf Deutsch wenn DE, Englisch nur wenn auf EN gewechselt

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

## OpenClaw Konfiguration

### Telegram Bot
- **Bot:** @sersery_bot
- **User ID:** 577361584 (@johnbilking)
- **dmPolicy:** allowlist (nur 577361584)
- **dmScope:** main (eine Session für Telegram + Webchat)
- **Status:** ✅ Eingerichtet & funktioniert

### OpenClaw Setup
- **Host:** srv1304306
- **Workspace:** /root/.openclaw/workspace
- **Gateway:** Port 18789 (loopback)
- **Model:** anthropic/claude-opus-4-5
- **Thinking:** high
- **Brave Search:** ✅ aktiv
- **Memory Search:** ✅ local provider
- **Heartbeat:** ✅ alle 30min

---

## Arbeitsweise — Skills & Docs

### Installierte Skills (2026-02-05)
- `shadcn-baseui` — komplett umgeschrieben für Base UI (NICHT Radix)
- `nextjs-expert`, `react-expert`, `docker-essentials`, `docker-compose-gen`
- `ai-dockerfile`, `nginx-config-gen`, `next-config-gen`

### Pflicht bei jeder Aktion:
1. **Verfügbare Skills durchsehen** (available_skills) → passenden Skill laden
2. **Context7 Docs** konsultieren für aktuelle API/Library-Referenz
3. **Nie veraltete Infos** nutzen — immer latest Versionen

### Context7 Docs Referenzen (Top Scores):
| Tech | Context7 ID | Score |
|------|------------|-------|
| React | `/websites/react_dev` | 91.7 |
| Gin | `/websites/gin-gonic_ja` | 90.4 |
| Next.js | `/vercel/next.js` | 87.0 |
| Base UI | `/websites/base-ui_react` | 86.1 |
| Snyk | `/snyk/cli` | 85.7 |
| Rust | `/websites/doc_rust-lang_stable_book` | 85.2 |
| Docker | `/docker/docs` | 84.3 |
| Node.js | `/websites/nodejs_latest-v20_x` | 80.8 |
| OpenSearch | `/websites/opensearch` | 79.9 |
| Nginx | `/websites/nginx_en` | 77.6 |
| Go | `/websites/go_dev_doc` | 69.8 |
| Tailwind | `/websites/tailwindcss` | 65.6 |

### Context7 API Pattern:
```
https://context7.com/api/v1/<id>?tokens=10000&topic=<topic>
```

---

## Lessons Learned

### 2026-02-04 - Erstes Setup
- Deinstallation löscht Workspace-Daten (kein Backup!)
- GitHub-Repo für Workspace ist Pflicht für Persistenz
- Projekte NIEMALS in `/root/clawd/` speichern → `/root/ETG-Hotel-IBE/` etc.
- Git-Workflow mit sofortigem Push nach jedem Commit

### 2026-02-05 - ETG Pipeline
- OpenSearch refresh_interval muss korrekt gesetzt sein, sonst sind Docs nicht suchbar
- Language-specific Indices besser als suffixed fields (keine Duplikate, sauberes i18n)
- ETG Dumps immer streamen, nie in RAM laden
- Old indices können 50+ GB an Duplikaten ansammeln → regelmäßig aufräumen

### 2026-02-05 - Next.js Binding
- Frontend MUSS mit `-H 0.0.0.0` gestartet werden, sonst bindet es nur auf IPv6

### 2026-02-05 - Re-Indexation
- **Chefs Entscheidungen IMMER respektieren** — KEIN policy_struct, KEIN deleted Flag (explizit!)
- Force Merge nicht parallel bei 32GB RAM — einzeln machen
- `_count` API statt `_cat/indices` für korrekte Counts bei refresh_interval=-1
- geo_point Mapping MUSS vor erstem Indexieren gesetzt werden
- Dump-Analyse VOR dem ersten Import machen — nicht nachträglich Felder entdecken

---

## ToDo / Ideas

- [x] ~~ETG Pipeline fertig~~ ✅ Beide Sprachen indexiert (3.298M each), alle Felder + Guest Ratings
- [x] ~~Re-Indexation mit allen fehlenden Feldern~~ ✅ description_struct, contact, amenity_groups, images_ext, kind, facts, geo_point
- [ ] Force Merge abschließen (hotels_de + hotels_en — einzeln!)
- [ ] Backend: Neue Felder in API-Responses einbauen (description_struct, contact, amenity_groups, images_ext, kind)
- [ ] Frontend: Neue Felder nutzen (Descriptions aus Index statt ETG API-Fallback)
- [ ] Sprachverwirrung Hotelpage fixen (RateHawk API Language-Parameter)
- [ ] Systematischer E2E Sprach-Check über gesamte IBE
- [ ] Cron für tägliche ETG incremental + wöchentliche full Dumps
- [ ] Regelmäßige Git-Commits automatisieren
- [ ] Hotel IBE & Flight IBE Architektur dokumentieren
- [ ] Custom Skills: Lynis, Snyk, Rust, Gin, OpenSearch
- [ ] Flight-frontend Container (165 Restarts) untersuchen
- [ ] Brotli Compression für Nginx
- [ ] PostgreSQL 16→17 Upgrade
- [ ] OpenSearch JVM Tuning (bei Bedarf)
- [x] ~~`pois` Index entfernen~~ → Chef will POIs + Reviews behalten (2026-02-05)

### ETG API Fixes (2026-02-06) — ~2-3h Aufwand
Aus 8 API-Analysen konsolidiert:

**🔴 Hoch (B2C-Compliance):**
- [ ] 5 Structs typisieren statt `serde_json::Value`: `Deposit`, `NoShowPenalty`, `SellPriceLimits`, `BarRatePriceData`, `Perks`
- [ ] `recommended_price` von `Option<String>` zu typisierten Object ändern
- [ ] `room_name_info` von `Option<String>` zu `Option<serde_json::Value>` ändern

**🟡 Mittel:**
- [ ] `get_hash()` Fallback fixen: `search_hash` statt `match_hash` für Booking
- [ ] Validierungen hinzufügen: Geo-radius (1-70000), Datum (max 730d checkin, 30d stay), region_id > 0
- [ ] Region `id` Typ: `i32` → `String` (ETG liefert String)

**🟢 Niedrig:**
- [ ] `only_eclc` Duplikat entfernen (nur in UpsellsConfig behalten)
- [ ] `#[deprecated]` Annotationen: `meal`, `vat_data.value`, `hotels[].id`
- [ ] Hash-Lifetime Konstanten definieren (search_hash=1h, book_hash=24h/6h)
- [x] ~~Security: UFW + fail2ban + SSH Hardening~~ ✅ Done
- [x] ~~Skill für Context7 Recherche~~ → Context7 Docs in MEMORY.md
- [x] ~~Projekt-Struktur umziehen~~ → `/root/ETG-Hotel-IBE/`

### 2026-02-05 (Abend) - Geo & Frontend Bugs
- **geo_point Queries:** Range-Queries auf `location.lat`/`location.lon` funktionieren NICHT mit geo_point Mapping → `geo_bounding_box` oder `geo_distance` verwenden!
- **Streaming State-Timing:** UI-State nie auf incomplete streaming data basieren — immer auf `isStreamingComplete` warten
