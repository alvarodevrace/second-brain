# Plan: OpenClaw — Integración de CLAU en Entorno de Desarrollo Local

> **Estado:** Aprobado — En fase de integración
> **Owner:** CLAU
> **Actualización:** 2026-03-13

---

## Resumen

Establecer un entorno de desarrollo donde Claude (CLAU) tenga acceso directo a las herramientas del monorepo Jauría CrossFit, eliminando la necesidad de copiar/pegar código y permitiendo ejecución de comandos, tests y deploys directamente desde la interfaz de chat.

---

## Objetivos

1. **Eficiencia:** Reducir el ciclo de feedback entre cambio de código y validación.
2. **Contexto:** Permitir que CLAU lea todo el codebase y la documentación de una sola vez.
3. **Acción:** Habilitar a CLAU para ejecutar scripts de infra, backups y migraciones de Supabase.

---

## Arquitectura de Herramientas

| Herramienta | Función | Estado |
|---|---|---|
| **Claude Desktop** | Interfaz principal de chat | ✅ Activa |
| **MCP (Model Context Protocol)** | Conexión con el sistema de archivos y herramientas | ✅ Activo |
| **Local Filesystem MCP** | Acceso a archivos del monorepo | ✅ Activo |
| **Git MCP** | Commits, diffs y ramas | ✅ Activo |
| **PostgreSQL MCP** | Consultas directas a Supabase (vía connection string) | ⏳ Pendiente |
| **Google Search MCP** | Búsqueda de documentación técnica externa | ✅ Activo |

---

## Protocolo de Seguridad

1. **No secrets:** Nunca pedir a CLAU que escriba secrets en archivos controlados por git. Usar `.env` locales ignorados.
2. **Review:** Revisar siempre los diffs antes de aceptar un commit masivo.
3. **Rollback:** CLAU debe crear un checkpoint (tag o rama temporal) antes de cambios arquitectónicos profundos.

---

## Tareas de Configuración

- [x] Instalación de Claude Desktop
- [x] Configuración de `claude_desktop_config.json` con acceso al directorio del proyecto
- [x] Prueba de lectura/escritura de archivos
- [ ] Configuración de MCP para Supabase (PostgreSQL)
- [ ] Definición de reglas de "Definition of Done" para CLAU

---

## Flujo de Trabajo (Día a Día)

1. **Inicio:** CLAU lee `system/STATE.md` para entender qué se hizo ayer.
2. **Ejecución:** CLAU propone cambios, el usuario aprueba el diff.
3. **Validación:** CLAU corre `npm run test` o scripts locales.
4. **Cierre:** CLAU actualiza `system/STATE.md` y prepara el handoff.
