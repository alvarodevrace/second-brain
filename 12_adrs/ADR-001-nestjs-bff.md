# ADR-001 — Adoptar NestJS como Backend BFF

> **Estado:** Aceptado — 2026-03-12

---

## Contexto

El sistema Jauría CrossFit nació como `Angular + Supabase + n8n directo`. El admin panel llamaba directamente a n8n, Evolution API y Supabase desde el browser. Esto generó:

1. **Secretos expuestos:** `N8N_API_KEY`, `EVOLUTION_API_KEY` visibles en el cliente
2. **Errores CORS:** n8n y Evolution no están configurados para recibir llamadas desde el browser en todos los casos
3. **Sin autorización centralizada:** la lógica de "¿puede este rol hacer esto?" estaba dispersa en el frontend
4. **Sin composición segura:** el dashboard requería múltiples llamadas paralelas a proveedores externos, sin timeout ni normalización de errores

La capa de aplicación necesitaba un backend propio que mediara estas integraciones.

---

## Opciones consideradas

### Opción A — Next.js full-stack

- **Pro:** mismo ecosistema React/TypeScript, server actions, buena integración con Vercel/edge
- **Contra:** el objetivo no es un full-stack React app sino un backend puro y mantenible; el frontend ya es Angular y no se migra

### Opción B — NestJS

- **Pro:** framework backend dedicado en TypeScript, modular, con guards/interceptors nativos para auth y roles, se despliega como servicio independiente en Coolify
- **Contra:** un servicio más en infraestructura; requiere configurar CORS y env vars propias

### Opción C — Supabase Edge Functions

- **Pro:** cero infraestructura adicional, integrado con Supabase Auth
- **Contra:** vendor lock-in, limitaciones de runtime Deno, difícil de probar localmente, no adecuado para integraciones complejas con n8n y Evolution

---

## Decisión

**Se elige: Opción B — NestJS**

NestJS provee la estructura modular necesaria para escalar las integraciones (n8n, Evolution, Payphone, Supabase) sin exponer secretos al browser. Es un backend puro que complementa Angular sin obligar a migrar el frontend. Se puede desplegar en Coolify como servicio separado con sus propias env vars.

El proyecto ya no debe pensarse como `Angular + Supabase + n8n directo`, sino como:

```
Angular (cliente)
  → NestJS BFF (backend de aplicación)
    → Supabase (datos/auth/storage)
    → n8n (automatización)
    → Evolution API (WhatsApp)
    → Payphone (pagos)
```

---

## Consecuencias

### Positivas

- Secretos solo server-side (`N8N_API_KEY`, `EVOLUTION_API_KEY`, etc.)
- CORS resuelto: el browser habla con el BFF, el BFF habla con los proveedores
- Autorización por rol centralizada en NestJS (guards + JWT de Supabase)
- Composición de datos multi-fuente sin exponer complejidad al frontend
- Auditoría y logging técnico centralizados

### Negativas / Trade-offs

- Un servicio más en Coolify (CPU, memoria, costo)
- La migración del frontend existente es gradual — hay un período de coexistencia
- Deploy del BFF diferido hasta terminar el núcleo del desarrollo y tener dominio final del cliente

### Neutras

- Supabase sigue siendo dueño de auth.users, tablas y storage
- n8n sigue siendo dueño de automatizaciones y cron jobs
- El BFF no reemplaza a Supabase ni a n8n; los complementa

---

## Fases de migración

Ver `docs/ai-ops/BACKEND-BFF-MIGRATION.md` para el plan completo de fases 1–7.

| Fase | Descripción | Estado |
|---|---|---|
| 1 | Bootstrap NestJS base | 🟡 En progreso (ALV-86) |
| 2 | Proxy seguro para n8n | Pendiente (ALV-88) |
| 3 | Proxy seguro para Evolution API | Pendiente |
| 4 | Operaciones de pagos | Pendiente |
| 5 | Landing page hacia backend | Pendiente |
| 6 | Capa de aplicación sobre Supabase | Pendiente |
| 7 | Deploy y operación | Diferido — requiere dominio final |

---

## Issues relacionados

- `ALV-86` — bootstrap backend NestJS
- `ALV-87` — arquitectura BFF
- `ALV-88` — integración n8n segura
- `ALV-89` — migración del admin al backend BFF
- `ALV-91` — deploy backend BFF en Coolify
- `ALV-92` — dominio y routing final
- `ALV-93` — operación local-first del BFF

---

*Fecha de decisión: 2026-03-12*
*Owner: CLAU*
*Fuente: `docs/ai-ops/BACKEND-BFF-MIGRATION.md`*
