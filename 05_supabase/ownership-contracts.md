# SUPABASE ACCESS

## Principio base

La diferencia entre agentes es de `ownership`, no solo de permisos tecnicos.

## CLAU

Owner de:

- schema
- migraciones
- RLS
- RPCs
- redirects de auth
- reglas de acceso
- incidentes estructurales de datos

## CODI

Owner dentro de la capa app de:

- consumo de datos desde Angular o NestJS BFF
- fixes de logica de aplicacion apoyados en el contrato actual
- queries o updates compatibles con el schema vigente
- validacion de contratos entre app y Supabase

## CODO

Puede necesitar contexto de datos para workflows, pero no es owner de la estructura.

`CODO` si puede:

- leer tablas, columnas y payloads necesarios para un workflow
- validar que un workflow usa correctamente el contrato existente

`CODO` no debe decidir unilateralmente:

- nuevas tablas
- nuevas columnas
- nuevas RPCs
- cambios de auth
- cambios estructurales del contrato de datos

## Regla final

- `CLAU` diseña y cambia la estructura
- `CODI` implementa y corrige sobre esa estructura dentro de la app
- `CODO` automatiza sobre esa estructura sin redefinirla.
