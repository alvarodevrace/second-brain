# Workflow n8n — [Nombre del workflow]

> Documentación de workflow. Una ficha por workflow activo.

---

## Identificación

| Campo | Valor |
|---|---|
| ID n8n | `XXXXXXXXXXXXXXXX` |
| Nombre | WFX - Nombre |
| Estado | Activo / Inactivo / En desarrollo |
| Nodos | N |
| Última versión | vX — AAAA-MM-DD |
| Issue Linear | `ALV-XXX` |

## Propósito

<!-- ¿Qué hace este workflow? ¿Cuándo se dispara? -->

## Trigger

- Tipo: (Webhook / Cron / Manual / otro workflow)
- Endpoint/Schedule:

## Flujo de nodos

```
Trigger
  → Nodo A (tipo)
  → Nodo B (tipo)
  → Condición: [rama si / no]
    → Rama SI: Nodo C
    → Rama NO: Nodo D
```

## Dependencias externas

- Supabase tablas: `tabla1`, `tabla2`
- APIs: Evolution / Gmail / Payphone / etc.
- Credenciales n8n: (nombre de la credencial, NO el valor)

## Bugs conocidos / Comportamiento especial

<!-- Patrones que hay que recordar para mantener este workflow -->

## Historial de cambios relevantes

| Versión | Fecha | Cambio |
|---|---|---|
| v1 | | Creación inicial |

---

*Owner: CODO (ejecución) · CLAU (deploy / credenciales)*
