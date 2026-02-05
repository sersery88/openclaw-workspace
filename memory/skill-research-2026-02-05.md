# Tech-Stack Skill & Docs Research — 2026-02-05

Systematische Recherche über ClawHub Skills und Context7 (Skills + Docs API).

---

### 1. OpenSearch

**ClawHub Skills:** Keine relevanten Ergebnisse  
**Context7 Skills:** Nicht verfügbar  
**Context7 Docs:**
| ID | Tokens | Benchmark | Trust | Verified | Hinweis |
|---|---|---|---|---|---|
| `/websites/opensearch` | 4.54M | 79.9 | 10 | ✅ | Allgemeine Docs (latest) |
| `/websites/opensearch_3_3` | 4.56M | 65.0 | 10 | ✅ | Version 3.3 spezifisch |
| `/opensearch-project/opensearch` | 339K | 71.7 | 9.4 | ✅ | GitHub Repo (v2.19.1), 10.9K ⭐ |

**Empfehlung:** ✅ Context7 Docs nutzen — `/websites/opensearch` (beste Benchmark 79.9, umfassendste Docs). Kein ClawHub-Skill nötig, Docs reichen.

---

### 2. React

**ClawHub Skills:**
| Skill | Version | Score |
|---|---|---|
| `react-expert` | v0.1.0 | 0.423 |
| `ai-sdk-ui` | v0.1.0 | 0.351 |
| `shadcn-ui` | v1.0.0 | 0.340 |

**Context7 Skills:** Nicht verfügbar  
**Context7 Docs:**
| ID | Tokens | Benchmark | Trust | VIP | Verified |
|---|---|---|---|---|---|
| `/websites/react_dev` | 571K | **91.7** | 10 | ✅ | ✅ |
| `/websites/18_react_dev` | 521K | 82.6 | 10 | ✅ | ✅ |
| `/websites/react_dev_reference` | 307K | 77.8 | 10 | ✅ | ✅ |

**Empfehlung:** ✅ `react-expert` (ClawHub) installieren + Context7 Docs `/websites/react_dev` nutzen (Benchmark 91.7 — hervorragend!). Für API-Reference zusätzlich `/websites/react_dev_reference`.

---

### 3. Tailwind CSS

**ClawHub Skills:**
| Skill | Version | Score |
|---|---|---|
| `ai-css-to-tailwind` | v1.0.1 | 0.391 |
| `css-to-tailwind` | v1.0.0 | 0.372 |
| `tailwind-config-gen` | v1.0.1 | 0.251 |

**Context7 Skills:** Nicht verfügbar  
**Context7 Docs:**
| ID | Tokens | Benchmark | Trust | VIP | Verified |
|---|---|---|---|---|---|
| `/websites/tailwindcss` | 329K | 65.6 | 9.9 | ✅ | ✅ |
| `/websites/v2_tailwindcss` | 145K | 77.9 | 9.9 | ❌ | ✅ |

**Empfehlung:** ✅ `tailwind-config-gen` (ClawHub) installieren für Config-Generierung. Context7 Docs: `/websites/tailwindcss` (v4, aktuellste) für Utility-Referenz. ACHTUNG: `v2_tailwindcss` hat zwar bessere Benchmark, ist aber v2 (veraltet)!

---

### 4. Rust

**ClawHub Skills:** Keine relevanten (nur `test-patterns` v1.0.0 mit 0.233 — generisch)  
**Context7 Skills:** Nicht verfügbar  
**Context7 Docs:**
| ID | Tokens | Benchmark | Trust | Verified |
|---|---|---|---|---|
| `/websites/doc_rust-lang_stable_book` | 130K | **85.2** | 10 | ✅ |
| `/websites/doc_rust-lang_book` | 129K | 73.6 | 10 | ✅ |
| `/websites/rust-analyzer_github_io_book` | 166K | 39.4 | 8.3 | ❌ |

**Empfehlung:** ✅ Context7 Docs `/websites/doc_rust-lang_stable_book` nutzen (Benchmark 85.2, offizielle Rust Book Stable). Kein passender ClawHub-Skill → ggf. eigenen Rust-Expert-Skill bauen.

---

### 5. Golang / Go

**ClawHub Skills:**
| Skill | Version | Score |
|---|---|---|
| `go-linter-configuration` | v1.0.0 | 0.394 |

**Context7 Skills:** Nicht verfügbar  
**Context7 Docs:**
| ID | Tokens | Benchmark | Trust | Verified |
|---|---|---|---|---|
| `/websites/go_dev_doc` | 418K | **69.8** | 10 | ✅ |
| `/websites/go_dev_ref_spec` | 64K | 56.8 | 10 | ✅ |
| `/websites/go_dev_ref` | 68K | 42.2 | 10 | ✅ |
| `/golang/go` | 939K | — | 8.3 | ✅ |

**Empfehlung:** ✅ `go-linter-configuration` (ClawHub) installieren. Context7 Docs: `/websites/go_dev_doc` (umfassendste Go-Docs, 418K Tokens). Für Spec: `/websites/go_dev_ref_spec`. Benchmark-Scores eher moderat — ggf. eigenen Go-Expert-Skill bauen.

---

### 6. Gin (Go Web Framework)

**ClawHub Skills:** Keine relevanten (Suche lieferte nur Prompt-Engineering-Artikel)  
**Context7 Skills:** Nicht verfügbar  
**Context7 Docs:**
| ID | Tokens | Benchmark | Trust | Verified |
|---|---|---|---|---|
| `/websites/gin-gonic_ja` | 36K | **90.4** | 9.4 | ✅ |
| `/websites/gin-gonic_zh-cn` | 23K | 88.9 | 9.4 | ❌ |
| `/gin-gonic/gin` | 29K | 85.0 | 7.2 | ❌ |

**Empfehlung:** ✅ Context7 Docs `/websites/gin-gonic_ja` nutzen (Benchmark 90.4 — exzellent! Trotz japanischer URL ist Content meist bilingual). Alternative: `/gin-gonic/gin` (GitHub, 82K ⭐). Kein ClawHub-Skill → eigenen Gin-Expert-Skill bauen (lohnt sich bei 90.4 Benchmark-Docs).

---

### 7. Nginx

**ClawHub Skills:**
| Skill | Version | Score |
|---|---|---|
| `nginx-gen` | v1.0.0 | 0.366 |
| `nginx-config-gen` | v1.0.1 | 0.350 |
| `npm-proxy` | v1.0.0 | 0.308 |

**Context7 Skills:** Nicht verfügbar  
**Context7 Docs:**
| ID | Tokens | Benchmark | Trust | VIP | Verified |
|---|---|---|---|---|---|
| `/websites/nginx_en` | 459K | **77.6** | 9.7 | ✅ | ✅ |
| `/0xjacky/nginx-ui` | 82K | 78.6 | 9.9 | ❌ | ✅ |

**Empfehlung:** ✅ `nginx-config-gen` (ClawHub) installieren für Config-Generierung. Context7 Docs: `/websites/nginx_en` (offizielle Nginx Docs, VIP, 459K Tokens).

---

### 8. Snyk

**ClawHub Skills:** Keine Ergebnisse  
**Context7 Skills:** Nicht verfügbar  
**Context7 Docs:**
| ID | Tokens | Benchmark | Trust | Verified |
|---|---|---|---|---|
| `/snyk/cli` | 761K | **85.7** | 9.6 | ✅ |
| `/snyk-labs/pysnyk` | 3K | 0 | 9.7 | ✅ |

**Empfehlung:** ✅ Context7 Docs `/snyk/cli` nutzen (Benchmark 85.7, 5.3K ⭐). Kein ClawHub-Skill → eigenen Snyk-Integration-Skill bauen (Security-Scanning in CI/CD).

---

### 9. Lynis (Security)

**ClawHub Skills:** Keine Ergebnisse (aber `security-sentinel` v1.1.2 und `pentest` v1.0.0 bei "security" Suche gefunden)  
**Context7 Skills:** Nicht verfügbar  
**Context7 Docs:** Keine relevanten Ergebnisse (nur unrelated: Lenis, Silicon Labs IoT Security)

**Empfehlung:** ⚠️ Lücke! Weder ClawHub noch Context7 haben Lynis-spezifische Resources. → **Eigenen Lynis-Skill bauen** (Audit-Kommandos, Report-Parsing, Hardening-Empfehlungen). Als Workaround: `security-sentinel` v1.1.2 (ClawHub) für allgemeine Security-Audits.

---

### 10. Docker

**ClawHub Skills:**
| Skill | Version | Score |
|---|---|---|
| `container-debug` | v1.0.0 | 0.427 |
| `docker-essentials` | v1.0.0 | 0.423 |
| `docker-ctl` | v1.0.0 | 0.411 |
| `ai-dockerfile` | v1.0.1 | 0.409 |
| `docker-sandbox` | v1.0.0 | 0.406 |
| `docker-compose-gen` | v1.0.1 | 0.383 |

**Context7 Skills:** Nicht verfügbar (Seite zeigt generischen Text)  
**Context7 Docs:**
| ID | Tokens | Benchmark | Trust | VIP | Verified |
|---|---|---|---|---|---|
| `/docker/docs` | 1.34M | **84.3** | 9.9 | ✅ | ✅ |
| `/llmstxt/docker_llms_txt` | 2.79M | **85.0** | 10 | ❌ | ❌ |

**Empfehlung:** ✅ `docker-essentials` + `docker-compose-gen` + `ai-dockerfile` (ClawHub) installieren — beste Kombination. Context7 Docs: `/docker/docs` (offizielle Docker Docs, VIP, 1.3M Tokens, Benchmark 84.3).

---

### 11. Node.js

**ClawHub Skills:** Keine relevanten Ergebnisse  
**Context7 Skills:** Nicht verfügbar  
**Context7 Docs:**
| ID | Tokens | Benchmark | Trust | Verified |
|---|---|---|---|---|
| `/websites/nodejs_latest-v20_x` | 1.34M | **80.8** | 10 | ✅ |
| `/websites/nodejs_api` | 1.47M | — | 10 | ✅ |
| `/websites/nodejs_latest-v24_x_api` | 1.30M | 54.0 | 10 | ✅ |

**Empfehlung:** ✅ Context7 Docs `/websites/nodejs_latest-v20_x` nutzen (Benchmark 80.8, LTS v20). Für bleeding edge: `/websites/nodejs_latest-v24_x_api` (v24). Kein passender ClawHub-Skill → Core-Skills decken Node.js bereits gut ab.

---

### 12. Next.js

**ClawHub Skills:**
| Skill | Version | Score |
|---|---|---|
| `nextjs-expert` | v1.0.0 | **0.540** |
| `deps-checker` | v1.0.2 | 0.526 |
| `next-config-gen` | v1.0.1 | 0.497 |
| `tailwind-config-gen` | v1.0.1 | 0.496 |
| `frontend-design-ultimate` | v1.0.0 | 0.511 |

**Context7 Skills:** Nicht verfügbar  
**Context7 Docs:**
| ID | Tokens | Benchmark | Trust | VIP | Verified |
|---|---|---|---|---|---|
| `/vercel/next.js` | 575K | **87.0** | 10 | ✅ | ✅ |
| `/websites/nextjs` | 643K | 83.5 | 10 | ✅ | ✅ |
| `/llmstxt/nextjs_llms-full_txt` | 1.56M | 64.4 | 10 | ❌ | ❌ |

**Empfehlung:** ✅ `nextjs-expert` (ClawHub, höchster Score 0.540!) installieren + `next-config-gen`. Context7 Docs: `/vercel/next.js` (Benchmark 87.0, direkt von Vercel, Versions bis v16.1.5).

---

## 🎯 Gesamtempfehlung

### Sofort installieren (ClawHub Skills)

| Priorität | Skill | Technologie | Score |
|---|---|---|---|
| 🔴 Hoch | `nextjs-expert` | Next.js | 0.540 |
| 🔴 Hoch | `react-expert` | React | 0.423 |
| 🔴 Hoch | `docker-essentials` | Docker | 0.423 |
| 🟡 Mittel | `docker-compose-gen` | Docker | 0.383 |
| 🟡 Mittel | `ai-dockerfile` | Docker | 0.409 |
| 🟡 Mittel | `nginx-config-gen` | Nginx | 0.350 |
| 🟡 Mittel | `next-config-gen` | Next.js | 0.497 |
| 🟡 Mittel | `tailwind-config-gen` | Tailwind | 0.251 |
| 🟢 Optional | `go-linter-configuration` | Go | 0.394 |
| 🟢 Optional | `security-sentinel` | Security allg. | 0.359 |

### Context7 Docs — Top Picks für jeden Technologie

| Technologie | Context7 ID | Benchmark | Empfehlung |
|---|---|---|---|
| React | `/websites/react_dev` | **91.7** | 🏆 Beste Benchmark aller Docs! |
| Gin | `/websites/gin-gonic_ja` | **90.4** | Exzellent |
| Next.js | `/vercel/next.js` | **87.0** | Top, mit Versionierung |
| Rust | `/websites/doc_rust-lang_stable_book` | **85.2** | Offizielle Rust Book |
| Snyk | `/snyk/cli` | **85.7** | Sehr gut |
| Docker | `/docker/docs` | **84.3** | Offizielle Docs, VIP |
| Node.js | `/websites/nodejs_latest-v20_x` | **80.8** | LTS v20 |
| OpenSearch | `/websites/opensearch` | **79.9** | Umfassend |
| Nginx | `/websites/nginx_en` | **77.6** | Offiziell, VIP |
| Tailwind | `/websites/tailwindcss` | 65.6 | v4 (aktuell) |
| Go | `/websites/go_dev_doc` | 69.8 | Solide |

### Selbst bauen (keine guten vorhandenen Skills)

| Technologie | Warum selbst bauen? |
|---|---|
| **Lynis** | ⚠️ Komplette Lücke — weder ClawHub noch Context7 |
| **Snyk** | Gute Docs (85.7), aber kein ClawHub-Skill für Integration |
| **Rust** | Gute Docs, aber kein Expert-Skill |
| **Gin** | Hervorragende Docs (90.4), kein ClawHub-Skill |
| **OpenSearch** | Gute Docs, aber kein ClawHub-Skill für Query/Index-Management |

### Context7 Skills

**Ergebnis:** Context7 Skills sind für KEINE der gesuchten Technologien verfügbar. Context7 ist primär eine Docs/Documentation-Plattform, keine Skill-Distribution. → Fokus auf ClawHub für Skills, Context7 nur für Docs.

---

*Recherche durchgeführt am 2026-02-05 08:52 UTC*
