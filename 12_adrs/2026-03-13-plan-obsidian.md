# Plan Obsidian — second-brain

> **Estado:** Aprobado con alcance reducido. Ejecutar semana 2026-03-17+ cuando ALV-49 (WhatsApp E2E) esté cerrado.

---

## Resumen Original

Objetivo: retirar Notion del flujo operativo y adoptar `Obsidian + Git` como sistema central de conocimiento, manteniendo `Linear` como fuente de verdad de tareas.

Resultado esperado:
- conocimiento técnico y operativo en Markdown
- repositorio dedicado `second-brain`
- agentes leyendo contexto desde Markdown
- base lista para futura indexación RAG

---

## Revisión Arquitectónica de CLAU — Veredicto

> Escrito 2026-03-13. Revisar antes de ejecutar.

### Veredicto general

**✅ Fases 1–5 aprobadas.** El plan es correcto en dirección y estructura.
**🔴 Fases 6–7 (VPS + RAG) diferidas.** No hay caso de uso concreto y el VPS no está documentado para acceso SSH.

El problema no es la dirección, sino el momento: hay blockers de producción activos (ALV-49, ALV-50, WhatsApp desconectada). Ejecutar el plan completo ahora sería desvío de recursos.

---

### Qué hacer vs. qué diferir

| Entregable | Veredicto | Razón |
|---|---|---|
| E1: Crear repo + estructura | ✅ Hacer | Bajo riesgo, alto valor |
| E2: Plantillas | ✅ Hacer | Cubren gaps reales (ADRs, incidentes) |
| E3: Reparto de fuente de verdad | ✅ Hacer | Ya está implícito, solo formalizarlo |
| E4: Migración desde Notion + limpiar agents/clau/CLAUDE.md | ✅ Hacer (alcance reducido) | Solo 4 páginas con valor real |
| E5: Protocolo CLAU + CODI | ✅ Hacer | `claude-role.md` + `codex-role.md` |
| E6: VPS `/opt/brain` | 🔴 Diferir | SSH al VPS no documentado; no hay caso de uso n8n real todavía |
| E7: RAG (pgvector, embeddings) | 🔴 Diferir indefinidamente | Es un proyecto aparte; prerequisito: 3 meses de uso real del brain |

---

### Problema principal que esto resuelve

`agents/clau/CLAUDE.md` hoy tiene ~714 líneas mezclando onboarding con documentación técnica profunda (WF3 31 nodos, schema Supabase completo, bugs n8n). Eso dificulta el arranque de nuevas sesiones y aumenta el riesgo de leer contexto desactualizado.

**Meta concreta:** reducir `agents/clau/CLAUDE.md` de ~714 → ~200 líneas de onboarding puro. El resto vive en el brain con punteros.

Secciones a extraer al brain:
- WF3 arquitectura detallada (31 nodos) → `06_n8n/wf3-arquitectura.md`
- Patrones y bugs críticos n8n → `06_n8n/patrones-bugs.md`
- Supabase schema completo → `05_supabase/schema.md`
- Tabla de workflows (IDs + estados) → `06_n8n/workflows.md`

---

### Riesgos identificados

**1. Fragmentación documental si no se mantiene**
Si el brain se crea y no se actualiza durante sprints intensos, terminamos con 4 sistemas de verdad en paralelo.
→ **Mitigación:** agregar regla en `START-OF-DAY.md`: "si cierras un cambio estructural de arquitectura, creas o actualizas el ADR correspondiente antes de cerrar sesión."

**2. agents/clau/CLAUDE.md queda obsoleto durante la migración**
Al mover contenido al brain, agents/clau/CLAUDE.md debe actualizarse en el mismo commit.
→ **Mitigación:** migrar sección por sección, atómica: (1) crear archivo en brain, (2) reemplazar sección en agents/clau/CLAUDE.md con puntero `→ ver second-brain/06_n8n/patrones-bugs.md`.

**3. CODI no puede acceder al repo del brain directamente**
El plan original asigna a CODI "revisar estructura y validar". CODI no tiene MCPs ni puede clonar repos.
→ **Mitigación:** CLAU es owner único del brain. CODI reporta gaps documentales en HANDOFF al trabajar en Angular. CLAU los incorpora.

---

### Criterios de éxito (cómo saber que está bien hecho)

1. Un agente nuevo puede arrancar leyendo solo `agents/clau/CLAUDE.md` (onboarding) + archivos del brain relevantes, sin necesitar Notion
2. `agents/clau/CLAUDE.md` tiene menos de 250 líneas y solo contiene: stack, comandos, MCPs, credenciales de referencia, punteros al brain
3. Existe al menos un ADR real documentado en `12_adrs/` (ej: decisión NestJS BFF vs Next.js)
4. No hay contenido duplicado entre agents/clau/CLAUDE.md y el brain

---

## Plan de Ejecución (Semana 2026-03-17+)

### Prerequisito

- ALV-49 cerrado (WhatsApp E2E test pasado) o en estado que permita desviar foco

### Paso 1 — CLAU crea la estructura base (Día 1, ~1h)

1. Crear repo `alvarodevrace/second-brain` en GitHub
2. Crear estructura de carpetas:

```text
second-brain/
  00_inbox/
  01_projects/
  02_people/
  03_arquitectura/
  04_infraestructura/
  05_supabase/
  06_n8n/
  07_agentes/
  08_glosario/
  09_sops/
    templates/
  10_referencias/
  12_adrs/
  README.md
```

3. Crear `README.md` con: propósito, qué vive aquí, qué no, reglas, relación con Linear y `system/ops/`
4. Crear las 7 plantillas en `09_sops/templates/`: cliente, ADR, SOP, incidente, workflow-n8n, servicio-infra, rol-agente
5. Crear `07_agentes/claude-role.md` y `07_agentes/codex-role.md`

### Paso 2 — CLAU extrae contenido de agents/clau/CLAUDE.md (Día 1-2, ~2h)

Por cada sección, en un solo commit:
1. Crear el archivo en el brain con el contenido
2. Reemplazar la sección en agents/clau/CLAUDE.md con puntero al brain

Secciones a migrar (en orden de prioridad):
- [ ] WF3 arquitectura (31 nodos) → `06_n8n/wf3-arquitectura.md`
- [ ] Patrones y bugs críticos n8n → `06_n8n/patrones-bugs.md`
- [ ] Schema Supabase completo → `05_supabase/schema.md`
- [ ] Tabla de workflows IDs → `06_n8n/workflows.md`
- [ ] Variables de entorno Coolify → `04_infraestructura/coolify-env-vars.md`

### Paso 3 — Migración selectiva desde Notion (Día 2-3, ~1h)

Solo migrar las páginas con contenido real no duplicado en `system/ops/`:
- [ ] Arquitectura general → `03_arquitectura/sistema-cobros.md`
- [ ] Infraestructura → `04_infraestructura/`
- [ ] Workflows docs → `06_n8n/`
- [ ] ADRs implícitos en bitácora → `12_adrs/`

No migrar: bitácoras de sesión (ya en HANDOFF), snapshots de tareas (en Linear), notas vacías.

### Paso 4 — Actualizar reglas operativas (Día 3, ~30min)

- [ ] Agregar en `system/ops/SYSTEM.md`: regla "cambio estructural → ADR en brain antes de cerrar sesión"
- [ ] Agregar en `system/ops/START-OF-DAY.md`: paso "leer ADR/archivo del brain relevante al trabajo del día"
- [ ] Sincronizar agents/AGENTS.md con agents/clau/CLAUDE.md reducido

### ~~Paso 5 — VPS y RAG~~ (DIFERIDO)

No ejecutar hasta que:
- SSH al VPS esté documentado con usuario + autenticación
- Haya un caso de uso real de n8n leyendo desde `/opt/brain`

---

## Decisiones Ya Fijadas (del plan original)

- Notion sale del flujo operativo.
- `Linear` sigue siendo la fuente de verdad de tareas y ownership.
- El brain vive en un repo separado, no dentro de este monorepo.
- `system/ops/*` se mantiene en este repo como memoria operativa inmediata del proyecto.
- El brain no almacenará secretos reales.
- CLAU es owner único del brain (CODI aporta feedback textual en HANDOFF).

---

## Archivo Maestro

Este archivo actúa como plan maestro de migración hasta que el repo `second-brain` exista y esté estabilizado.
