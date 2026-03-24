# VALHALLA — Sistema de Orquestación de Agentes IA

> **Estado:** Aprobado — Inicio implementación: 2026-03-27
> **Owner:** CLAU
> **Proyecto:** Independent (Mantenimiento Jauría)
> **Ref:** [JAU-173](https://linear.app/alvarodevrace/issue/JAU-173)

---

## Contexto

VALHALLA es el sistema operativo de agentes de Álvaro. Orquesta CLAU, CODI, CODO y GEMA de forma autónoma, con memoria sin desfaz (Verdad Absoluta), panel de control "Ojo de Dios", y ejecución en el Dell Inspiron local (8GB/500GB/Ubuntu Server) 8am–10pm vía Docker + systemd. Portabilidad a VPS sin cambiar código.

---

## Arquitectura

- **n8n [VAL] ORCHESTRATOR:** Recibe eventos de Linear y gestiona cron jobs (Start/End of Day).
- **Supabase VALHALLA:** Fuente de verdad absoluta (jobs, events, memory, sessions, decisions).
- **Dell Inspiron (Worker):** Poll cada 30s de la tabla `jobs`. Ejecuta comandos CLI de los agentes.
- **valhalla-dashboard (Angular 18):** Visualización en tiempo real del estado de los agentes y cola de trabajo.

---

## Verdad Absoluta (Supabase)

Si no está en Linear o Supabase VALHALLA, no existe. Las IAs leen Supabase al inicio de cada sesión, no archivos locales. Esto resuelve el desfaz de contexto entre agentes.

### Tablas Clave
- `jobs`: Cola de trabajo con prioridad y reintentos exponenciales.
- `events`: Log inmutable de actividad de los agentes.
- `memory`: Contexto persistente que reemplaza a `STATE.md`.
- `metrics_daily`: Control de presupuesto de tokens y tiempos de respuesta (SLA).

---

## Eficiencia de Tokens

- **Budget diario:** Límites estrictos de tokens por agente (ej: CLAU 150k, CODI 200k).
- **Heartbeat:** Monitoreo de salud del worker (CPU/RAM/Disk) sin consumo de LLM.
- **Prioridad:** Los jobs críticos se atienden antes que los de mantenimiento.

---

## Fases de Implementación

1. **Fase 1 (CLAU):** Migraciones Supabase + Seed de memoria.
2. **Fase 2 (CLAU):** Desarrollo del `valhalla-worker` en Docker.
3. **Fase 3 (CODI):** Dashboard "Ojo de Dios" en Angular 18.
4. **Fase 4 (CODO):** Workflows de n8n para orquestación Linear/Cron.
5. **Fase 5 (CLAU):** Migración de memoria de Jauría a VALHALLA.
