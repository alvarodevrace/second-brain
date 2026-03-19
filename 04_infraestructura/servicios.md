# Servicios de Infraestructura — Jauría CrossFit

> Referencia de todos los servicios activos. No almacenar secretos reales aquí — solo referencias a dónde encontrarlos.

---

## Plataforma de hosting

- **Coolify:** `https://coolify.alvarodevrace.tech`
- **VPS IP:** `72.60.26.201`
- **Dominio producción:** `jauriacrossfitness.com` (Cloudflare DNS pendiente)

---

## Servicios activos

### n8n (automatización)

| Campo | Valor |
|---|---|
| URL | `https://n8n.alvarodevrace.tech` |
| API key | → `MEMORY.md` del proyecto / Coolify env vars |
| Workflows activos | 9 (ver `06_n8n/workflows.md`) |

### Evolution API (WhatsApp)

| Campo | Valor |
|---|---|
| URL | `https://evolution.alvarodevrace.tech` |
| Instancia principal | `jauriaCrossfit` |
| API key | → `MEMORY.md` del proyecto / Coolify env vars |
| Reconexión WhatsApp | `GET /instance/connect/jauriaCrossfit` → escanear QR |

### Supabase (base de datos + auth)

| Campo | Valor |
|---|---|
| Project ID | `bxatcmcommoqnxnyqchu` |
| URL | `https://bxatcmcommoqnxnyqchu.supabase.co` |
| Anon key | → `MEMORY.md` del proyecto |
| Service role key | → Coolify env vars (NUNCA en frontend) |
| Admin nuevo | `alvarodevrace@outlook.com` |

### NestJS BFF (`admin/back/jauria-api`)

- Puerto: 4400 (local) | deploy: pendiente Coolify
- Env relevante: `N8N_MANUAL_REMINDER_WEBHOOK_PATH=recordatorio-manual`
- Para disparar workflows n8n: usar `N8nService.triggerWebhook(path, payload)` — NO `runWorkflow`
- Endpoint activo: `POST /api/v1/clients/:id/send-reminder` → llama webhook WF6

### GlitchTip (error monitoring)

| Campo | Valor |
|---|---|
| URL | `https://glitchtip.alvarodevrace.tech` |
| Org | `jauria-crossfit` |
| Proyecto | `jauria-admin` (ID 1) |
| Admin | `alvarodevrace@outlook.com` |
| DSN | → `MEMORY.md` del proyecto |
| Compatibilidad | SDK `@sentry/angular` compatible |

### OpenRouter (IA para WF3)

| Campo | Valor |
|---|---|
| Propósito | Validación de comprobantes en WF3 |
| API key | → `MEMORY.md` del proyecto / n8n credenciales |

---

## Git Repos

| Proyecto | Repo | Ramas | Rama activa |
|---|---|---|---|
| Landing Page | `alvarodevrace/jauria-landingpage` | main · develop · qa | develop |
| Admin Panel | `alvarodevrace/jauria-admin` | main · develop · qa | develop |
| Second Brain | `alvarodevrace/second-brain` | main | main |

Remote SSH: `git@github-alvarodevrace:alvarodevrace/<repo>.git`
Flujo: `develop → PR → qa → PR → main` (nunca commitear directo a main)

---

## Servicios en Producción (jauriacrossfitness.com)

| Servicio | URL | Estado |
|---|---|---|
| Landing | https://jauriacrossfitness.com | ⏳ DNS pending |
| Admin panel | https://admin.jauriacrossfitness.com | ⏳ DNS pending |
| BFF API | https://api.jauriacrossfitness.com | ⏳ DNS pending |

---

## Procedimientos de referencia

- Actualizar env vars en Coolify: `PATCH /api/v1/services/{uuid}/envs/bulk`
- Coolify API token: → `MEMORY.md` del proyecto
- Backup Supabase: `scripts/backup-supabase.sh` (diario 3:30 AM)
- Backup n8n: `scripts/backup-n8n.sh` (diario 3:00 AM)

---

*Owner: CLAU*
*Última actualización: 2026-03-19*
