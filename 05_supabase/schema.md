# Schema Supabase — Jauría CrossFit

> Referencia del schema de la base de datos.
> Project ID: `bxatcmcommoqnxnyqchu`

---

## Acceso

- **Project ID:** `bxatcmcommoqnxnyqchu` (parámetro `project_id`, NO `project_ref`)
- **URL:** `https://bxatcmcommoqnxnyqchu.supabase.co`
- **Anon key:** → ver `04_infraestructura/servicios.md`
- **Service role key:** → Coolify env vars (nunca exponer en frontend)

Para acceso detallado y RLS: ver `docs/ai-ops/SUPABASE-ACCESS.md` en el repo del proyecto.

---

## Tablas

### `clientes`

Membresías y datos de cobro. Usada por n8n.

| Columna | Tipo | Descripción |
|---|---|---|
| `id` | text (C001) | ID del cliente — formato `C001`, `C002`... |
| `nombre` | text | Nombre completo |
| `email` | text | Email principal |
| `telefono` | text | WhatsApp |
| `plan` | text | mensual / trimestral / semestral |
| `estado` | text | `Activo` / `Inactivo` / `Pendiente` |
| `fecha_proximo_pago` | date | Próxima fecha de cobro |

**Nota:** Para n8n, el campo `estado = 'Activo'` es el que determina si el cliente puede registrarse en el admin panel.

---

### `profiles`

Acceso al admin panel. Vincula `auth.users` con roles.

| Columna | Tipo | Descripción |
|---|---|---|
| `id` | uuid | = `auth.users.id` |
| `rol` | text | `admin` / `coach` / `atleta` |
| `id_cliente` | text | FK a `clientes.id` (solo atletas) |
| `nombre` | text | Nombre del usuario |
| `email` | text | Email |

**Nota crítica:**
- Atletas existen en `clientes` (n8n) Y pueden tener `profiles` (admin panel)
- Coach/admin existen SOLO en `profiles` (no en `clientes`)
- `profiles.id_cliente` es el vínculo entre los dos mundos

---

### `historial_pagos`

Registro de pagos procesados.

| Columna | Tipo | Descripción |
|---|---|---|
| `id` | uuid | PK |
| `id_cliente` | text | FK a `clientes.id` |
| `monto` | numeric | Monto pagado |
| `fecha_pago` | timestamptz | Fecha del pago |
| `metodo` | text | `payphone` / `transferencia` / `efectivo` |
| `comprobante_url` | text | URL del comprobante (si aplica) |
| `estado` | text | `verificado` / `pendiente` / `rechazado` |

---

### `conversaciones_whatsapp`

Conversaciones gestionadas por WF3.

| Columna | Tipo | Descripción |
|---|---|---|
| `id` | uuid | PK |
| `id_cliente` | text | FK a `clientes.id` |
| `mensajes` | jsonb | Array de mensajes de la conversación |
| `estado` | text | `abierta` / `cerrada` / `escalada` |
| `created_at` | timestamptz | |

---

### `clases`

Horarios de WOD, Open Gym, Barbell Club, Fundamentos.

| Columna | Tipo | Descripción |
|---|---|---|
| `id` | uuid | PK |
| `tipo` | text | `WOD` / `Open Gym` / `Barbell Club` / `Fundamentos` |
| `dia_semana` | int | 0=domingo ... 6=sábado |
| `hora_inicio` | time | |
| `hora_fin` | time | |
| `capacidad_max` | int | |

---

### `inscripciones`

Registros de atletas a clases.

| Columna | Tipo | Descripción |
|---|---|---|
| `id` | uuid | PK |
| `id_clase` | uuid | FK a `clases.id` |
| `id_profile` | uuid | FK a `profiles.id` |
| `fecha` | date | Fecha de la clase |
| `asistio` | boolean | |

**Regla:** Solo atletas con membresía activa (`clientes.estado = 'Activo'`) pueden inscribirse.

---

### `leads`

Formulario de contacto de la landing page.

| Columna | Tipo | Descripción |
|---|---|---|
| `id` | uuid | PK |
| `nombre` | text | |
| `email` | text | |
| `telefono` | text | |
| `mensaje` | text | |
| `created_at` | timestamptz | |

---

### `auditoria_config`

Log de cambios de configuración desde el admin panel.

| Columna | Tipo | Descripción |
|---|---|---|
| `id` | uuid | PK |
| `usuario_id` | uuid | FK a `profiles.id` |
| `accion` | text | Descripción del cambio |
| `valor_anterior` | jsonb | Estado previo |
| `valor_nuevo` | jsonb | Estado nuevo |
| `created_at` | timestamptz | |

---

## Flujo de alta de atleta (crítico)

```
Coach crea cliente en tabla `clientes` (con estado='Activo')
  → n8n WF1 puede cobrar recordatorios
  → Atleta va a /registro en admin panel
  → App verifica que email existe en `clientes` con estado='Activo'
  → App crea cuenta auth.users + profile con id_cliente vinculado
  → Atleta puede acceder al panel
```

---

*Owner: CLAU*
*Última actualización: 2026-03-13*
