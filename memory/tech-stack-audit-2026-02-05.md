# 🏨 Hotel IBE — Tech Stack Audit Report

**Datum:** 2026-02-05
**Projekt:** `/root/ETG-Hotel-IBE/`
**Server:** srv1304306 — 8 CPU, 32GB RAM, 387GB Disk (22% used)

---

## Zusammenfassung

| Bereich | Status | Kritisch | Optimierbar | OK |
|---------|--------|----------|-------------|-----|
| **OpenSearch** | ⚠️ | 2 | 3 | 5 |
| **Next.js Frontend** | ✅ | 0 | 2 | 8 |
| **Rust Backend** | ✅ | 0 | 0 | 10 |
| **Docker Setup** | ⚠️ | 1 | 3 | 3 |
| **Nginx** | ⚠️ | 1 | 3 | 5 |
| **Infrastruktur** | ⚠️ | 1 | 2 | 2 |

---

## 1. 🔍 OpenSearch (PRIORITÄT)

### Aktuelle Konfiguration
- **Version:** 3.4.0 (Docker Image `opensearchproject/opensearch:3.4.0`)
- **Cluster:** Single-Node (`discovery.type=single-node`)
- **JVM Heap:** 2GB (`-Xms2g -Xmx2g`)
- **Lucene:** 10.3.2
- **Security Plugin:** Deaktiviert (nur localhost binding)
- **Binding:** `127.0.0.1:9200` ✅

### Indices

| Index | Docs | Size | Shards | Replicas | Refresh |
|-------|------|------|--------|----------|---------|
| `hotels_de` | 8.511.045 | 900 MB | 2 | 0 | **-1 (DISABLED!)** |
| `hotels_en` | 0 | 416 B | 2 | 0 | — |
| `regions` | 249.774 | 104 MB | 1 | 1 | 60s |
| `analytics_events` | 7.098 | 1.7 MB | 1 | 0 | default |
| `flight-offers` | 238 | 149 KB | 1 | 0 | default |

### 🔴 KRITISCH

#### 1. `hotels_de` refresh_interval = -1 (DEAKTIVIERT!)
Der Hauptindex hat `refresh_interval: -1` — das bedeutet neue/geänderte Dokumente werden **NIE automatisch suchbar**. Dies ist nur akzeptabel während Bulk-Indexing. Falls der Index nach dem letzten Bulk-Import nicht zurückgesetzt wurde, ist das ein Problem.

**Fix — wenn Bulk-Import abgeschlossen:**
```bash
curl -X PUT "localhost:9200/hotels_de/_settings" -H 'Content-Type: application/json' -d '{
  "index": {
    "refresh_interval": "30s"
  }
}'
# Sofortiger Refresh:
curl -X POST "localhost:9200/hotels_de/_refresh"
```

> **Empfehlung:** `30s` für den Hotels-Index. Er wird nicht ständig geschrieben, also kein Performance-Problem, aber neue Daten werden zeitnah suchbar.

#### 2. Keine Docker Resource Limits
OpenSearch hat **keine Memory-/CPU-Limits** im Container. Bei einem Memory-Leak oder Spike kann es den gesamten Server lahmlegen.

**Fix:**
```bash
# Container mit Limits neu starten:
docker stop opensearch
docker rm opensearch
docker run -d \
  --name opensearch \
  --restart unless-stopped \
  -p 127.0.0.1:9200:9200 \
  -p 127.0.0.1:9600:9600 \
  -v /var/lib/opensearch-data:/usr/share/opensearch/data \
  --ulimit memlock=-1:-1 \
  --ulimit nofile=65536:65536 \
  --memory=4g \
  --memory-swap=4g \
  --cpus=4 \
  -e "discovery.type=single-node" \
  -e "bootstrap.memory_lock=true" \
  -e "OPENSEARCH_JAVA_OPTS=-Xms2g -Xmx2g" \
  -e "DISABLE_SECURITY_PLUGIN=true" \
  opensearchproject/opensearch:3.4.0
```

### ⚠️ OPTIMIERBAR

#### 3. Cluster Health: Yellow (unassigned replica shard)
`regions` hat `number_of_replicas: 1`, aber auf einem Single-Node-Cluster kann keine Replica zugewiesen werden.

**Fix:**
```bash
curl -X PUT "localhost:9200/regions/_settings" -H 'Content-Type: application/json' -d '{
  "index": { "number_of_replicas": 0 }
}'
```

#### 4. `hotels_de` — 2 Shards für ~900MB
2 Shards für 900MB sind OK, aber für einen Single-Node eher unnötig. Pro Shard empfiehlt OpenSearch 10-50GB. Bei unter 1GB reicht 1 Shard.

**Empfehlung bei nächstem Re-Index:** 1 Shard verwenden.

#### 5. JVM Heap bei 32GB RAM
2GB Heap bei 32GB System-RAM ist konservativ. Bei 8.5M Hotels und wachsendem Datenvolumen:

**Empfehlung:** 4GB Heap (`-Xms4g -Xmx4g`), maximal 50% des verfügbaren RAMs.

### ✅ WAS GUT IST

- **Version 3.4.0** — aktuell! (3.5.0 kommt ~10. Feb 2026, Upgrade optional)
- **Bulk-Indexing optimiert** — `refresh_interval: -1` zeigt, dass Bulk-Import korrekt konfiguriert wurde
- **Custom Analyzer** — `autocomplete_analyzer` (edge_ngram 2-15) und `german_analyzer` (stemming + normalization) sind top
- **Geo-Point Mapping** — vorhanden für location-basierte Suche
- **Completion Suggester** — Mapping vorhanden
- **Heap Usage** — 27% (gesund, kein Memory-Druck)
- **GC Performance** — 257 Young GC collections in 2.4s total, extrem schnell
- **Query Performance** — 9138 Queries, Ø 0.71ms pro Query — ausgezeichnet
- **memlock unlimited** und `nofile 65536` — korrekt konfiguriert

### 📊 Performance-Metriken
```
Heap Usage:          27% (593 MB / 2048 MB)
Old Gen:             21% (458 MB / 2048 MB)  
Young GC:            257 collections, 2.391s total
Query Total:         9.138 queries
Avg Query Time:      ~0.71ms
Fetch Total:         9.122
Avg Fetch Time:      ~0.32ms
Open File Descriptors: 928 / 65.536
```

---

## 2. ⚛️ Next.js Frontend

### Aktuelle Konfiguration
- **Next.js:** 16.1.6 ✅ (latest stable)
- **React:** 19.2.4 ✅ (latest)
- **TypeScript:** ^5
- **Tailwind CSS:** v4
- **Build Tool:** Turbopack (default in Next.js 16)

### Wichtige Dependencies

| Package | Version | Status |
|---------|---------|--------|
| Next.js | 16.1.6 | ✅ Latest |
| React | 19.2.4 | ✅ Latest |
| @base-ui/react | ^1.1.0 | ✅ Korrekt (kein Radix!) |
| @tanstack/react-query | ^5.90.20 | ✅ Latest |
| @tanstack/react-virtual | ^3.13.18 | ✅ Virtualisierung |
| zustand | ^5.0.10 | ✅ Latest |
| react-hook-form | ^7.71.1 | ✅ Latest |
| i18next | ^25.8.0 | ✅ Latest |
| mapbox-gl | ^3.18.1 | ✅ |
| lucide-react | ^0.563.0 | ✅ |
| date-fns | ^4.1.0 | ✅ |
| axios | ^1.13.4 | ✅ |
| babel-plugin-react-compiler | ^1.0.0 | ✅ |
| web-vitals | ^5.1.0 | ✅ |

### `next.config.ts` Analyse

### ✅ WAS GUT IST

- **React Compiler aktiviert** (`reactCompiler: true`) — automatische Memoization
- **Cache Components** (`cacheComponents: true`) — `'use cache'` Directive support
- **Turbopack FS Cache** — für Dev und Build (`turbopackFileSystemCacheForDev/Build`)
- **Security Headers** — umfassende CSP, X-Frame-Options, X-XSS-Protection, etc.
- **Image Optimization** — Remote Patterns konfiguriert, responsive Sizes definiert
- **API Rewrites** — sauberer Proxy zum Rust Backend
- **Base UI** statt Radix UI ✅
- **Zustand + React Query** — modernes State Management
- **Virtualisierung** — `@tanstack/react-virtual` für große Listen

### ⚠️ OPTIMIERBAR

#### 1. `typescript.ignoreBuildErrors: true`
TypeScript-Fehler werden beim Build ignoriert. Das ist gefährlich in Production.

**Fix:**
```ts
// next.config.ts
typescript: {
  ignoreBuildErrors: false, // oder Zeile entfernen
},
```

#### 2. CSP `'unsafe-eval'` in script-src
`unsafe-eval` schwächt die Content Security Policy erheblich ab. Prüfen ob wirklich nötig (mapbox-gl benötigt es oft).

**Empfehlung:** Falls nur Mapbox `unsafe-eval` braucht, einen CSP Nonce/Hash verwenden oder prüfen ob Mapbox neuere Versionen ohne eval auskommen.

---

## 3. 🦀 Rust Backend

### Aktuelle Konfiguration
- **Rust Edition:** 2024 (bleeding edge!) 🚀
- **Toolchain:** Nightly
- **Minimum Rust:** 1.90
- **Framework:** Axum 0.8

### Dependency-Audit

| Dependency | Version | Status |
|-----------|---------|--------|
| tokio | 1.49 | ✅ Latest |
| axum | 0.8 | ✅ Latest |
| axum-extra | 0.12 | ✅ Kompatibel mit axum 0.8 |
| reqwest | 0.13 | ✅ Latest (HTTP/2, native-tls) |
| sqlx | 0.8 | ✅ Latest |
| redis | 1.0 | ✅ Latest |
| serde | 1.0 | ✅ |
| tower-http | 0.6 | ✅ |
| jsonwebtoken | 10.3 | ✅ |
| simd-json | 0.17 | ✅ SIMD-beschleunigtes JSON Parsing |
| tikv-jemallocator | 0.6 | ✅ jemalloc Allocator |
| lettre | 0.11 | ✅ Email mit rustls |
| moka | 0.12 | ✅ In-Memory Cache |
| prometheus | 0.14 | ✅ Metrics |

### ✅ ALLES GUT — Hervorragend konfiguriert!

- **Rust 2024 Edition** mit async closures — State of the Art
- **jemalloc** — bessere Memory-Performance als System-Allocator
- **SIMD JSON** — 2-3x schneller als standard serde_json
- **Fat LTO + single codegen unit** — maximale Release-Optimierung
- **panic = "abort"** — 2-5% Performance-Gewinn
- **Compression** — zstd + gzip Support
- **System Monitoring** — sysinfo für Resource-Throttling
- **Saubere Workspace-Struktur** — `crates/api-server`, `crates/migration-tool`

---

## 4. 🐳 Docker Setup

### Laufende Container

| Container | Image | Version | Ports | Status |
|-----------|-------|---------|-------|--------|
| opensearch | opensearchproject/opensearch | 3.4.0 | 127.0.0.1:9200 | ✅ Up |
| opensearch-dashboards | opensearchproject/opensearch-dashboards | 3.4.0 | 127.0.0.1:5601 | ✅ Up |
| postgres | postgres:16-alpine | 16.11 | 127.0.0.1:5432 | ✅ Up |
| redis | redis:8-alpine | 8.4.0 | 127.0.0.1:6379 | ✅ Up |
| umami | ghcr.io/umami-software/umami:postgresql-latest | latest | 127.0.0.1:3002 | ✅ Up |
| umami-db | postgres:15-alpine | 15.x | 127.0.0.1:5433 | ✅ Healthy |

### 🔴 KRITISCH

#### 1. Keine Docker Compose Datei für Hotel IBE
Alle Container laufen als einzelne `docker run` Commands. Bei einem Server-Reboot oder Migration fehlt die Dokumentation, wie alles gestartet wird.

**Empfehlung:** Docker Compose Datei erstellen:

```yaml
# /root/ETG-Hotel-IBE/docker-compose.yml
services:
  opensearch:
    image: opensearchproject/opensearch:3.4.0
    container_name: opensearch
    restart: unless-stopped
    ports:
      - "127.0.0.1:9200:9200"
      - "127.0.0.1:9600:9600"
    environment:
      - discovery.type=single-node
      - bootstrap.memory_lock=true
      - "OPENSEARCH_JAVA_OPTS=-Xms2g -Xmx2g"
      - DISABLE_SECURITY_PLUGIN=true
    volumes:
      - /var/lib/opensearch-data:/usr/share/opensearch/data
    ulimits:
      memlock:
        soft: -1
        hard: -1
      nofile:
        soft: 65536
        hard: 65536
    deploy:
      resources:
        limits:
          memory: 4g
          cpus: '4'
    healthcheck:
      test: ["CMD-SHELL", "curl -sf http://localhost:9200/_cluster/health || exit 1"]
      interval: 30s
      timeout: 10s
      retries: 3

  opensearch-dashboards:
    image: opensearchproject/opensearch-dashboards:3.4.0
    container_name: opensearch-dashboards
    restart: unless-stopped
    ports:
      - "127.0.0.1:5601:5601"
    environment:
      - OPENSEARCH_HOSTS=["http://opensearch:9200"]
      - DISABLE_SECURITY_DASHBOARDS_PLUGIN=true
    depends_on:
      opensearch:
        condition: service_healthy

  postgres:
    image: postgres:17-alpine
    container_name: postgres
    restart: unless-stopped
    ports:
      - "127.0.0.1:5432:5432"
    environment:
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
      POSTGRES_DB: hotel_ibe
    volumes:
      - postgres-data:/var/lib/postgresql/data
    deploy:
      resources:
        limits:
          memory: 2g
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 10s
      timeout: 5s
      retries: 5

  redis:
    image: redis:8-alpine
    container_name: redis
    restart: unless-stopped
    ports:
      - "127.0.0.1:6379:6379"
    deploy:
      resources:
        limits:
          memory: 512m
    healthcheck:
      test: ["CMD", "redis-cli", "ping"]
      interval: 10s
      timeout: 5s
      retries: 3

volumes:
  postgres-data:
```

### ⚠️ OPTIMIERBAR

#### 2. PostgreSQL 16 statt 17
PostgreSQL 17 ist seit Ende 2024 verfügbar und bringt Performance-Verbesserungen für JSON, MERGE, und Query-Planung.

**Empfehlung:** Bei nächstem Maintenance-Window auf `postgres:17-alpine` upgraden.

#### 3. Keine Health Checks auf Containern
Nur `umami-db` hat einen Health Check. OpenSearch, Redis und Postgres (Hotel) haben keinen.

#### 4. PostgreSQL Default-Konfiguration
```
shared_buffers:       128MB  (sollte 25% RAM = ~512MB sein)
work_mem:             4MB    (OK)
effective_cache_size: 4GB    (OK)
max_connections:      100    (OK)
```

**Fix:**
```bash
docker exec postgres psql -U postgres -c "ALTER SYSTEM SET shared_buffers = '512MB';"
docker exec postgres psql -U postgres -c "ALTER SYSTEM SET random_page_cost = 1.1;"
docker restart postgres
```

### ✅ WAS GUT IST

- **Alle Container binden an 127.0.0.1** — nicht von außen erreichbar
- **RestartPolicy: unless-stopped** — Containers starten nach Reboot
- **Redis 8.4.0** — neueste Version
- **Alpine-basierte Images** — klein und sicher
- **Daten-Volumes gemountet** — Persistenz gewährleistet

---

## 5. 🌐 Nginx

### Aktuelle Konfiguration
- **Version:** Nginx (Paket-Version aus Ubuntu repo)
- **SSL:** Let's Encrypt mit auto-renewal
- **TLS:** 1.2 + 1.3
- **Rate Limiting:** Konfiguriert ✅

### Sites

| Site | Config | SSL | Status |
|------|--------|-----|--------|
| hotelpink.ch | ✅ | Let's Encrypt | ⚠️ Maintenance Mode |
| swissbooker.ch | ✅ | Let's Encrypt | Landing Page |
| hotel-ibe (8082) | ✅ | Kein SSL | Interner Proxy |
| pinktravel | ✅ | Self-signed | Flight IBE |
| dashboards.swissbooker.ch | ✅ | Let's Encrypt | OS Dashboards |
| analytics.swissbooker.ch | ✅ | Let's Encrypt | Umami |

### 🔴 KRITISCH

#### 1. Gzip nicht vollständig konfiguriert
`gzip on;` ist aktiv, aber **alle wichtigen Optionen sind auskommentiert**. Das bedeutet nur `text/html` wird komprimiert — kein JSON, CSS, JavaScript!

**Fix — in `/etc/nginx/nginx.conf`:**
```nginx
gzip on;
gzip_vary on;
gzip_proxied any;
gzip_comp_level 5;
gzip_buffers 16 8k;
gzip_http_version 1.1;
gzip_min_length 256;
gzip_types
  text/plain
  text/css
  text/xml
  text/javascript
  application/json
  application/javascript
  application/x-javascript
  application/xml
  application/xml+rss
  application/vnd.ms-fontobject
  application/x-font-ttf
  font/opentype
  image/svg+xml
  image/x-icon;
```

**Impact:** Ohne gzip für JSON/JS/CSS werden API-Responses und Frontend-Assets unkomprimiert geliefert. Bei Hotel-Search-Results mit vielen Hotels = unnötig viel Bandbreite.

### ⚠️ OPTIMIERBAR

#### 2. `worker_connections 768` — zu niedrig
Für einen Production-Server mit mehreren Sites:

```nginx
events {
    worker_connections 2048;
    multi_accept on;
    use epoll;
}
```

#### 3. Kein Proxy-Buffering/Caching konfiguriert
Für API-Responses und statische Assets fehlt Proxy-Caching:

```nginx
# In http{} Block:
proxy_buffer_size 128k;
proxy_buffers 4 256k;
proxy_busy_buffers_size 256k;

# Optional: Proxy Cache für statische API-Responses
proxy_cache_path /var/cache/nginx levels=1:2 keys_zone=api_cache:10m max_size=1g inactive=60m;
```

#### 4. Keine Brotli-Kompression
Brotli ist 15-20% besser als gzip und wird von allen modernen Browsern unterstützt.

```bash
# Installation:
apt install libnginx-mod-brotli
```

```nginx
# nginx.conf:
brotli on;
brotli_comp_level 6;
brotli_types text/plain text/css application/json application/javascript text/xml application/xml image/svg+xml;
```

### ✅ WAS GUT IST

- **SSL/TLS** — Let's Encrypt, TLSv1.2+1.3, moderne Ciphers
- **Rate Limiting** — 10r/s API, 5r/s Search, 20 concurrent connections
- **Security Headers** — HSTS, X-Frame-Options, X-Content-Type-Options, Referrer-Policy
- **server_tokens off** — Nginx Version nicht exponiert
- **HTTP → HTTPS Redirect** auf allen Sites
- **Separate Rate-Limit Zones** für API und Search

---

## 6. 🏗️ Infrastruktur & Prozessmanagement

### PM2

| Process | PID | Uptime | Restarts | Status |
|---------|-----|--------|----------|--------|
| hotel-backend | 956245 | 27m | 4 | ✅ Online |
| hotel-frontend | 957144 | 26m | 7 | ✅ Online |
| flight-backend | — | 24h | 10 | ⚠️ Online |
| flight-frontend | — | 24h | 165 | 🔴 165 Restarts! |

### 🔴 KRITISCH

#### 1. Secrets im Klartext in ecosystem.config.js
Die Datei enthält **alle Production Secrets** als Plaintext:
- Saferpay API Credentials
- SMTP Passwörter
- JWT Secret
- Database Passwörter
- Mapbox Token

**Empfehlung:** 
- Secrets in `.env` Datei auslagern (existiert bereits unter `/root/ETG-Hotel-IBE/.env`)
- `ecosystem.config.js` sollte nur `env_file: '.env'` referenzieren
- `.env` aus Git halten (ist bereits in `.gitignore` ✅)

### ⚠️ OPTIMIERBAR

#### 2. Kein Swap konfiguriert
Bei 32GB RAM und 70% Nutzung ohne Swap kann ein Memory-Spike zum OOM-Kill führen.

```bash
fallocate -l 4G /swapfile
chmod 600 /swapfile
mkswap /swapfile
swapon /swapfile
echo '/swapfile none swap sw 0 0' >> /etc/fstab
```

#### 3. `flight-frontend` hat 165 Restarts
Das deutet auf ein wiederkehrendes Problem hin (Memory-Leak, Config-Fehler, etc.). Sollte untersucht werden.

### ✅ WAS GUT IST

- **PM2** — Production-Grade Process Manager
- **Exponential Backoff** für Restart-Delays
- **Backend + Frontend** separat gemanagt

---

## 7. 📋 Priorisierte Maßnahmen

### Sofort (heute):

1. **🔴 OpenSearch `hotels_de` refresh_interval auf `30s` setzen** (falls Bulk-Import abgeschlossen)
   ```bash
   curl -X PUT "localhost:9200/hotels_de/_settings" -H 'Content-Type: application/json' -d '{"index":{"refresh_interval":"30s"}}'
   curl -X POST "localhost:9200/hotels_de/_refresh"
   ```

2. **🔴 Nginx gzip aktivieren** — alle gzip-Zeilen in nginx.conf uncommentieren
   ```bash
   # Dann: nginx -t && systemctl reload nginx
   ```

3. **🔴 `regions` replicas auf 0 setzen** (Single-Node → gelber Cluster)
   ```bash
   curl -X PUT "localhost:9200/regions/_settings" -H 'Content-Type: application/json' -d '{"index":{"number_of_replicas":0}}'
   ```

### Diese Woche:

4. **⚠️ Docker Compose Datei erstellen** — alle Container dokumentiert
5. **⚠️ Docker Resource Limits** — Memory-Limits auf allen Containern
6. **⚠️ PostgreSQL shared_buffers** erhöhen (128MB → 512MB)
7. **⚠️ Secrets aus ecosystem.config.js in .env verschieben**

### Nächste Iteration:

8. **⚠️ PostgreSQL 16 → 17 Upgrade**
9. **⚠️ OpenSearch auf 3.5.0** (kommt ~10. Feb 2026)
10. **⚠️ Brotli-Kompression** installieren
11. **⚠️ `typescript.ignoreBuildErrors: false`** — TypeScript-Fehler fixen
12. **⚠️ Nginx `worker_connections`** auf 2048 erhöhen
13. **⚠️ 4GB Swap** einrichten
14. **⚠️ flight-frontend 165 Restarts** untersuchen

---

## 8. 🌟 Gesamtbewertung

### Was herausragend gut ist:
- **Rust Backend** — State of the Art. Edition 2024, jemalloc, SIMD JSON, Fat LTO. Besser geht's kaum.
- **Next.js Frontend** — Alle Dependencies auf Latest, React Compiler, Turbopack, Cache Components. Perfekt.
- **OpenSearch Analyzer** — Autocomplete + German Stemming richtig konfiguriert.
- **Security** — Container nur auf localhost, Rate Limiting, Security Headers, SSL überall.
- **Redis 8.4.0** — Neueste Version, niedriger Memory-Footprint (72MB).

### Was Aufmerksamkeit braucht:
- **OpenSearch Ops** — refresh_interval, replicas, resource limits
- **Nginx Gzip** — einfacher Fix mit großem Impact
- **Docker Management** — Compose-Datei für Reproduzierbarkeit
- **Secrets Management** — aus Config-Dateien in .env

### Fazit:
Der **Application Code** (Rust + Next.js) ist erstklassig und auf dem neuesten Stand. Die **Infrastruktur-Konfiguration** (Docker, Nginx, OpenSearch Settings) hat typische Quick-Win Optimierungen, die mit wenig Aufwand großen Impact bringen. Keine der Kritischen Punkte ist ein Showstopper, aber sie sollten zeitnah adressiert werden.

---

*Report erstellt am 2026-02-05 10:53 UTC*
*Nächster Audit empfohlen: 2026-04-05*
