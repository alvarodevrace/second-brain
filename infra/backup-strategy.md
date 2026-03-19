# Estrategia de Backup — Jauría CrossFit

Arquitectura de respaldo y recuperación ante desastres para el ecosistema de Jauría CrossFit.

## Arquitectura de 3 capas

### Capa 1 — Supabase managed backups
*   **Servicio:** Supabase free tier.
*   **Frecuencia:** Backup automático gestionado por la plataforma cada 24h.
*   **Retención:** 7 días.
*   **Restauración:** Desde el Supabase Dashboard > Database > Backups.
*   **Responsable:** Supabase (SaaS) — sin intervención manual requerida para la creación.

### Capa 2 — WF-BACKUP (n8n → GitHub)
*   **Workflow:** `WF-BACKUP — Supabase + n8n → GitHub` (ID: `umFQAp8sZHBAvNCM`).
*   **Frecuencia:** Diario a las 3:00 AM (America/Guayaquil).
*   **Destino:** Repositorio privado `alvarodevrace/jauria-backups`.
*   **Tablas respaldadas:** `clientes`, `profiles`, `historial_pagos`, `conversaciones_whatsapp`, `leads`, `clases`, `inscripciones`, `retos`, `reto_participantes`, `auditoria_config`.
*   **Notificación:** Telegram vía `@jauriacrossfit_bot` con el resultado de la operación.
*   **Archivo fuente del workflow:** `infra/n8n/WF-BACKUP.json` en el monorepo.

### Capa 3 — Código e infraestructura
*   **Aplicaciones:** Repositorios GitHub para `jauria-admin`, `jauria-landingpage` y `jauria-admin-back`.
*   **Migraciones DB:** Almacenadas en `infra/migrations/` dentro del monorepo.
*   **Workflows n8n:** Exportaciones JSON en `infra/backups/n8n/` e `infra/n8n/`.
*   **CI/CD:** GitHub Actions configuradas en cada repositorio que despliegan a Coolify tras push exitoso a `main`.

---

## CI/CD Implementado
*   **Trigger:** Push a la rama `main` en los 3 repositorios core.
*   **Pipeline:** Checkout → Node 20 → npm ci → npm run build → Coolify API deploy.
*   **Secrets requeridos:** `COOLIFY_API_TOKEN`, `COOLIFY_BASE_URL`, y el UUID específico de cada aplicación en Coolify.
*   **Seguridad:** Branch protection activada (requiere PR + 1 review + build check exitoso).

---

## Recuperación ante Desastres (DRP)

| Escenario | Acción de Recuperación | Tiempo Estimado (RTO) |
| :--- | :--- | :--- |
| **Caída de servicio Supabase** | Esperar restauración por parte del proveedor (SLA oficial). | Horas |
| **Borrado accidental de datos** | Restaurar backup desde Supabase Dashboard > Backups. | 15 min |
| **Pérdida de instancia n8n** | Reimportar workflows desde `infra/n8n/`. | 30 min |
| **Pérdida de instancia Coolify** | Recrear servicios en nuevo servidor usando env vars de `second-brain`. | 2h |
| **Repositorio GitHub eliminado** | Restaurar desde backup local o clon/fork existente. | Inmediato |
