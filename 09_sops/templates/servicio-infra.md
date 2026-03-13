# Servicio de Infraestructura — [Nombre del servicio]

> Ficha de servicio desplegado en Coolify o en el VPS.

---

## Identificación

| Campo | Valor |
|---|---|
| Nombre | |
| URL de producción | |
| Tipo | Docker / Nixpacks / Static |
| Plataforma | Coolify / VPS directo |
| UUID Coolify | (si aplica) |
| Proyecto Coolify | |
| Issue Linear de deploy | `ALV-XXX` |

## Propósito

<!-- ¿Qué hace este servicio dentro de la plataforma? -->

## Variables de entorno críticas

> No escribir valores reales aquí. Solo documentar qué variables existen.

- `VAR_NAME` — descripción de qué contiene

## Healthcheck

- URL: `/health` o similar
- Criterio: responde 200 en < Xs

## Acceso de administración

- Panel: (URL del panel si tiene)
- Credenciales: → ver Coolify / vault (NO escribir aquí)

## Dependencias

- Servicios que dependen de este:
- Servicios de los que depende este:

## Procedimientos comunes

### Reiniciar el servicio

```bash
# vía Coolify UI o API
```

### Ver logs

```bash
# vía Coolify UI > Logs
```

## Issues relacionados

- `ALV-XXX`

---

*Owner: CLAU*
*Última revisión: AAAA-MM-DD*
