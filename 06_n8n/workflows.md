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
| `VBwhzSYheRZOnK6U` | WF3 - Validación Comprobantes WhatsApp | 34 | ✅ v9 2026-03-15 | ALV-48 ✅ |
| `es47dKkLiEyuzsLf` | WF4 - Notificaciones al Coach | 7 | ✅ Activo | — |
| `a0KGcLv4yAXBsZAW` | WF5 - Error Handler | 5 | ✅ Activo | ALV-143 ✅ |
| `GBiaBwkm5H4pYrL0` | WF6 - Recordatorio Manual por Cliente | 13 | ✅ Activo | — |
| `ORp0nd7ZNLYBalgb` | WF-WHATSAPP-MONITOR | 4 | ✅ Activo | — |
| `BFsLIVWRC0B3IP6K` | WF-INFRA-ALERTS | 3 | ✅ Activo | — |
| `sbTyvgzp7Q7MWB1T` | WF-KEEPALIVE - Supabase Ping | 3 | ✅ Activo | — |

---

## WF1 — Recordatorio de Cobro

- **Trigger:** Cron (mensual)
- **Propósito:** Enviar recordatorios de cobro por WhatsApp a clientes con pago pendiente
- **Tablas Supabase:** `clientes`, `historial_pagos`
- **Canales:** Evolution API (WhatsApp)
- **Nota:** Patch ALV-135 — recordatorios a `<= 3` días de vencimiento.

## WF2 — Webhook Payphone

- **Trigger:** Webhook (Payphone callback)
- **Propósito:** Recibir confirmación de pago de Payphone y registrar en Supabase
- **Tablas Supabase:** `clientes`, `historial_pagos`

## WF3 — Validación Comprobantes WhatsApp

- **Trigger:** Webhook (Evolution API → mensaje entrante)
- **Propósito:** Validar comprobantes de pago enviados por WhatsApp con IA (OpenRouter)
- **Nodos:** 34 (v9 — versión estable)
- **Motor IA:** Qwen Vision 72B vía OpenRouter (confirmado 2026-03-15)
- **Tablas Supabase:** `clientes`, `historial_pagos`, `conversaciones_whatsapp`
- **Canales:** Evolution API, OpenRouter
- **Nota crítica:** bug de teléfono corregido 2026-03-15 — `Parsear Mensaje WA` normaliza sin `+`
- **Backup:** `wf3_complete.json` en raíz del repo JauriaCrossfit

## WF4 — Notificaciones al Coach

- **Trigger:** Llamado por otros workflows (WF2, WF3)
- **Propósito:** Notificar al coach por Gmail sobre eventos importantes
- **Nota crítica:** Requiere `parameters.operation: \"send\"` explícito en nodo Gmail

## WF5 — Error Handler

- **Trigger:** Error en cualquier workflow
- **Propósito:** Capturar y notificar errores de ejecución
- **Nota:** 5 nodos — validación limpia + Telegram corregido (independiente de Gmail)

## WF6 — Recordatorio Manual por Cliente

- **Trigger:** Webhook (desde BFF NestJS)
- **Propósito:** Enviar recordatorio de pago a un cliente específico a demanda
- **Nota:** Implementado ALV-129 (tono configurable)

## WF-WHATSAPP-MONITOR

- **Trigger:** Cron (horario)
- **Propósito:** Monitorear estado de la instancia `jauriaCrossfit` en Evolution API
- **Acción:** Notificar vía Telegram si la instancia está desconectada

## WF-INFRA-ALERTS

- **Trigger:** Webhook (desde Coolify/Glitchtip)
- **Propósito:** Recibir alertas de infraestructura y reenviar a Telegram

## WF-KEEPALIVE - Supabase Ping

- **Trigger:** Cron (cada 5 días)
- **Propósito:** Evitar auto-pausa de Supabase en el free tier mediante pings activos

---

## Notas operativas

- **WhatsApp (Evolution):** instancia `jauriaCrossfit` — verificar estado antes de pruebas WF3
- Para activar/desactivar workflows desde el admin panel: `PATCH /api/v1/n8n/workflows/:id/activate`
- Documentación detallada de WF3: ver `06_n8n/wf3-arquitectura.md` (pendiente crear)

---

*Owner: CODO (ejecución) · CLAU (deploy / credenciales)*
*Última actualización: 2026-03-19*
