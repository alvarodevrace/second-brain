# DAILY OPS — Rutina de Agentes

## Flujo Diario

### 1. Sincronización (CLAU + GEMA)
- CLAU lee `system/STATE.md` y Linear.
- GEMA genera el `status-report` si es necesario.
- CLAU hace el routing de tareas a CODI y CODO.

### 2. Ejecución (CODI / CODO)
- Trabajan en sus repositorios/workflows asignados.
- Si necesitan info de arquitectura o manuales, preguntan a GEMA.
- Documentan bloqueos en `system/HANDOFF.md`.

### 3. Cierre (Todos)
- Cada agente ejecuta su `CLOSE-OF-DAY`.
- GEMA actualiza `system/STATE.md` con los avances dictados por CLAU.
- GEMA sube cualquier aprendizaje duradero al `second-brain` de GitHub.

## Señales para cambiar de owner

- **Angular o UX** -> `CODI`
- **Backend NestJS BFF app-level** -> `CODI`
- **Workflows n8n, webhooks, ejecuciones** -> `CODO`
- **Coolify, secrets, schema, RLS, migraciones** -> `CLAU`
- **Duda de arquitectura o incidente cross-layer** -> `CLAU`
- **Documentación, análisis de logs o reportes** -> `GEMA`
