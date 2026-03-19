# Backend BFF Migration Plan

Last updated: 2026-03-12
Owner: CLAU
Estado: En ejecución (Fase 1-3 completadas)

## Contexto operativo

Migración de Jauría CrossFit a una arquitectura con backend propio en NestJS para admin, landing e integraciones externas, eliminando llamadas directas desde el navegador a servicios sensibles.

## Mapa de arquitectura

```text
landing/front/jauria-web  --->  admin/back/jauria-api  --->  Supabase
                               |                       --->  n8n
admin/front/jauria-admin  --->  |                       --->  Evolution API
                               |
                               --->  futuros servicios sensibles (Payphone, etc.)
```

## Responsabilidades por capa

### Frontend admin / landing
- renderizado, formularios y UX
- envío de bearer token al backend
- cero secretos de terceros en cliente

### Backend NestJS
- validación de JWT de Supabase + autorización por rol
- proxy seguro a n8n, Evolution, Payphone
- lógica de negocio no trivial y auditoría

### Supabase
- tablas de negocio, auth.users + profiles, RLS y storage

### n8n
- cron jobs, webhooks, automatizaciones y notificaciones

## Fases de migración

1. **Fase 1. Bootstrap:** NestJS base + Auth Guard (Completado ✅)
2. **Fase 2. Proxy n8n:** Mover llamadas a n8n fuera del navegador (Completado ✅)
3. **Fase 3. Proxy Evolution:** Estado de WhatsApp y reconexión vía BFF (Completado ✅)
4. **Fase 4. Pagos:** Integración segura con Payphone (Pendiente ⏳)
5. **Fase 5. Landing:** Endurecer captación de leads (Pendiente ⏳)
6. **Fase 6. Deploy:** Desplegar en Coolify con dominio real (Pendiente ⏳ - ALV-91)
