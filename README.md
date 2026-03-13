# second-brain — Jauría CrossFit

Sistema central de conocimiento técnico y operativo del proyecto **Jauría CrossFit** y la plataforma **alvarodevrace**.

---

## Propósito

Este repositorio reemplaza a Notion como fuente de verdad documental. Contiene:

- Arquitectura del sistema y decisiones técnicas (ADRs)
- Documentación de workflows n8n, Supabase, infraestructura
- Roles de agentes IA (CLAU, CODI, CODO)
- Plantillas operativas (SOP, incidentes, onboarding)
- Referencias externas y glosario

---

## Qué vive aquí

| Carpeta | Contenido |
|---|---|
| `00_inbox/` | Notas sin clasificar, borradores rápidos |
| `01_projects/` | Estado de proyectos activos (JauriaCrossfit, etc.) |
| `02_people/` | Perfiles: coach, clientes tipo, equipo |
| `03_arquitectura/` | Decisiones de arquitectura del sistema |
| `04_infraestructura/` | Coolify, Evolution API, n8n config, VPS |
| `05_supabase/` | Schema, RLS, RPCs, acceso |
| `06_n8n/` | Workflows, patrones, bugs conocidos |
| `07_agentes/` | Roles CLAU, CODI, CODO |
| `08_glosario/` | Términos del proyecto |
| `09_sops/templates/` | Plantillas operativas |
| `10_referencias/` | Links externos, APIs, docs terceros |
| `12_adrs/` | Architecture Decision Records |

---

## Qué NO vive aquí

- Secretos, API keys, tokens reales → usar Coolify env vars o vault
- Tareas activas y ownership → Linear
- Bitácoras de sesión, handoffs → `docs/ai-ops/HANDOFF.md` en el repo del proyecto
- Estado operativo inmediato → `docs/ai-ops/STATE.md` en el repo del proyecto

---

## Relación con otros sistemas

| Sistema | Rol |
|---|---|
| **Linear** | Fuente de verdad de tareas, issues, ownership |
| **`docs/ai-ops/`** (JauriaCrossfit repo) | Memoria operativa inmediata: STATE, START-OF-DAY, HANDOFF, SYSTEM |
| **`CLAUDE.md`** (JauriaCrossfit repo) | Onboarding de sesión, stack, comandos, MCPs, punteros al brain |
| **Este repo** | Conocimiento estructural duradero: arquitectura, docs, decisiones |

---

## Reglas de mantenimiento

1. **Si cierras un cambio estructural de arquitectura**, crear o actualizar el ADR correspondiente en `12_adrs/` antes de cerrar sesión.
2. **No duplicar contenido** entre este repo y `docs/ai-ops/`. Si algo vive aquí, `CLAUDE.md` o `docs/ai-ops/` apuntan aquí.
3. **CLAU es owner único** del brain. CODI y CODO reportan gaps documentales en `HANDOFF.md`; CLAU los incorpora.
4. **No almacenar secretos reales** en ningún archivo de este repositorio.
5. Mantener este `README.md` actualizado con la tabla de carpetas.

---

## Cómo arrancar una sesión usando este brain

Un agente nuevo puede arrancar leyendo:
1. `CLAUDE.md` del repo JauriaCrossfit (onboarding y punteros)
2. `docs/ai-ops/STATE.md` (estado operativo inmediato)
3. El archivo del brain relevante al trabajo del día

---

*Owner: CLAU — Platform Architect / Orchestration Lead*
