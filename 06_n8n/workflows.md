# Workflows n8n — Jauría CrossFit

> Estado de todos los workflows activos en producción.
> Fuente de verdad para IDs y configuración.

---

## Servidor

- **URL:** `https://n8n.alvarodevrace.tech`
- **API key:** → ver `04_infraestructura/servicios.md`

---

## Tabla de workflows activos

| ID n8n | Nombre | Nodos | Estado | Issue |
|---|---|---|---|---|
| `GzmYUYGMG1X8wnTm` | WF1 - Recordatorio de Cobro | 24 | ✅ Activo | — |
| `ocfhDTSpWQN99ieE` | WF2 - Webhook Payphone | 22 | ✅ Activo | — |
| `VBwhzSYheRZOnK6U` | WF3 - Validación Comprobantes WhatsApp | 31 | ✅ v8 2026-03-09 | ALV-48 ✅ |
| `es47dKkLiEyuzsLf` | WF4 - Notificaciones al Coach | 7 | ✅ Activo | — |
| `a0KGcLv4yAXBsZAW` | WF5 - Error Handler | 4 | ✅ Activo | — |

---

## WF1 — Recordatorio de Cobro

- **Trigger:** Cron (mensual)
- **Propósito:** Enviar recordatorios de cobro por WhatsApp a clientes con pago pendiente
- **Tablas Supabase:** `clientes`, `historial_pagos`
- **Canales:** Evolution API (WhatsApp)

## WF2 — Webhook Payphone

- **Trigger:** Webhook (Payphone callback)
- **Propósito:** Recibir confirmación de pago de Payphone y registrar en Supabase
- **Tablas Supabase:** `clientes`, `historial_pagos`

## WF3 — Validación Comprobantes WhatsApp

- **Trigger:** Webhook (Evolution API → mensaje entrante)
- **Propósito:** Validar comprobantes de pago enviados por WhatsApp con IA (OpenRouter)
- **Nodos:** 31 (v8 — versión estable)
- **Tablas Supabase:** `clientes`, `historial_pagos`, `conversaciones_whatsapp`
- **Canales:** Evolution API, OpenRouter
- **Backup:** `wf3_complete.json` en raíz del repo JauriaCrossfit

## WF4 — Notificaciones al Coach

- **Trigger:** Llamado por otros workflows (WF2, WF3)
- **Propósito:** Notificar al coach por Gmail sobre eventos importantes
- **Nota crítica:** Requiere `parameters.operation: "send"` explícito en nodo Gmail

## WF5 — Error Handler

- **Trigger:** Error en cualquier workflow
- **Propósito:** Capturar y notificar errores de ejecución
- **Nota:** 4 nodos — workflow de soporte global

---

## Notas operativas

- **WhatsApp (Evolution):** instancia `jauriaCrossfit` — verificar estado antes de pruebas WF3
- Para activar/desactivar workflows desde el admin panel: `PATCH /api/v1/n8n/workflows/:id/activate`
- Documentación detallada de WF3: ver `06_n8n/wf3-arquitectura.md` (pendiente crear)

---

*Owner: CODO (ejecución) · CLAU (deploy / credenciales)*
*Última actualización: 2026-03-13*
