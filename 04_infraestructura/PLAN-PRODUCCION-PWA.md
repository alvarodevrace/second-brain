# Plan: Producción completa → PWA — Jauría CrossFit

> Última actualización: 2026-03-17
> Owner: CLAU
> Estado: En preparación — dominio pendiente de compra

---

## Contexto

Migración completa a producción con dominio real, sistema de alertas Telegram para el admin, seguridad y compliance, y PWA para atletas.

**Admin/dueño del sistema:** `alvarodevrace@outlook.com` / Telegram de Álvaro
- Recibe todos los errores, alertas y novedades de TODA la plataforma por Telegram
- `CORREO_ADMIN` en n8n = `alvarodevrace@outlook.com`
- El coach tendrá su propio email profesional (`CORREO_COACH`) — pendiente de contratar

---

## PROFESIONALIZACIÓN DEL COACH

El coach necesita estos elementos para que el sistema opere de forma profesional en producción.
Estos no son opcionales: sin ellos hay riesgos legales, operativos y de credibilidad.

### 1. Número de WhatsApp dedicado al negocio 🔴 Crítico

**Situación actual:** el chatbot de cobros (WF3, WF6) usa un número de WhatsApp.
Si ese número es el personal del coach hay tres problemas reales:
- Atletas mezclan conversaciones personales con cobros → pérdida de credibilidad y control
- Si el coach cambia de número personal, el sistema entero se rompe
- WhatsApp puede banear el número por mensajes automáticos masivos → pierde su número personal

**Recomendación:**
- Comprar un chip/SIM física dedicado solo al gym (Claro/CNT Ecuador ~$3 + datos ~$5/mes)
- Registrar ese número como WhatsApp Business con:
  - Foto: logo de Jauría CrossFit (NO foto personal)
  - Nombre de perfil: "Jauría CrossFit"
  - Descripción: "Sistema de membresías — consultas sobre cobros"
  - Horario de atención configurado
  - Respuesta automática de ausencia activada

**Impacto técnico (CLAU ejecuta cuando el coach tenga el chip):**
1. Pausar WF3 y WF6
2. Desconectar instancia actual de Evolution API
3. Crear nueva instancia o reconectar con el número nuevo
4. Re-escanear QR con el número del gym
5. Reactivar workflows
→ Ventana de interrupción: ~20 min. Requiere coordinar con el coach.

---

### 2. Correo profesional 🟠 Importante

**Situación actual:** los workflows n8n envían emails usando el Gmail de Álvaro.
En producción los atletas deben ver "Jauría CrossFit" como remitente, no a una persona.

**Recomendación:**
- `coach@jauriacrossfit.com` o `notificaciones@jauriacrossfit.com`
- **Google Workspace** (~$6/mes) → más profesional, compatible directo con Gmail OAuth en n8n
- **Zoho Mail** (gratis hasta 5 usuarios con dominio propio) → si quiere empezar gratis
- **Titan Mail** (~$1/mes) → opción más barata con dominio propio

**Impacto técnico (CODO ejecuta cuando el coach tenga el correo):**
- Re-autenticar credencial Gmail en n8n con la cuenta de Jauría
- Actualizar "From Name" = "Jauría CrossFit" en WF1, WF2, WF4, WF5, WF6
- Actualizar variable `CORREO_COACH` en n8n env vars

---

### 3. Datos fiscales y cuenta bancaria separada 🟠 Importante para Payphone

Para activar Payphone en producción se necesita:
- **RUC activo** con actividad económica "Servicios de acondicionamiento físico" (CIIU R9311.01)
- **Cuenta bancaria del negocio** separada de la cuenta personal
  - Payphone deposita ahí → queda limpio para declarar al SRI
- **Obligación tributaria:** si factura >$500/mes debe emitir facturas electrónicas (SRI)
  - Opción: portal SRI On-line (gratis)

Payphone pedirá estos datos al momento de activar el token de producción.

---

### 4. Reglamento interno del gym 🟡 Legal básico

Para tener respaldo ante disputas y cumplir LOPDP:
- Documento firmado por cada atleta al ingresar con:
  - Política de cancelación de membresía
  - Días de congelamiento permitidos
  - **Consentimiento explícito** para recibir mensajes de cobro por WhatsApp
  - Política de privacidad (datos tratados por Supabase, Evolution API, Payphone)
- Puede ser digital (formulario de Google o campo en el registro del panel)

→ El checkbox de consentimiento en el flujo de registro del panel admin cubre la parte digital.
→ Agregar página `/privacidad` en la landing cubre la parte web.

---

### 5. Manual de operación para el coach (1 página)

El coach necesita saber exactamente qué hace él y qué hace el sistema:

| Acción | Quién |
|---|---|
| Crear nuevo cliente en el panel | Coach |
| Enviar recordatorio manual de cobro | Coach (botón en panel) |
| Validar comprobante de pago | Sistema automático (WF3) |
| Recordatorio diario de vencimientos | Sistema automático (WF1) |
| Marcar pago como recibido si hay problema | Coach (panel) |
| **Reconectar WhatsApp si se cae** | Coach (panel admin — widget sesión) |
| Revisar alertas de error | Coach (Telegram) |

→ CLAU debe crear este documento en `docs/coach-manual.md` antes del go-live.

---

## SEGURIDAD Y COMPLIANCE

### Seguridad técnica

| Área | Riesgo | Acción | Owner |
|---|---|---|---|
| Secrets en frontend (`coolifyToken`, `evolutionApiKey`) | 🔴 Expuestos en JS del browser | ALV-97: proxies BFF | CLAU + CODI |
| WF6 fallback API key hardcodeada | 🔴 Token expuesto en JSON del workflow | Fix WF6 (2 nodos) | CODO |
| CORS BFF con `ALLOWED_ORIGINS=localhost` | 🟠 Cualquier origen puede llamar el BFF | Cambiar antes de deploy | CODI |
| Rate limiting BFF ausente | 🟠 Fuerza bruta en auth, spam en leads | Agregar `@nestjs/throttler` | CODI |
| Redirect URLs Supabase incluyen localhost | 🟠 Filtración potencial de tokens | Limpiar antes de deploy | CLAU |
| RLS Supabase | ✅ 9 tablas activas | Mantener | — |
| SSL | ✅ Coolify Traefik lo provee en prod | OK en prod | — |

### Compliance — Ecuador (LOPDP)

| Requisito | Estado | Acción | Owner |
|---|---|---|---|
| Consentimiento explícito WhatsApp | ❌ No documentado | Checkbox en registro de atleta | CODI |
| Política de privacidad visible | ❌ No existe | Página `/privacidad` en landing | CODI |
| Derecho a eliminar datos | ⚠️ Solo admin puede borrar | Proceso documentado (manual por ahora) | CLAU |
| Retención de datos | ❌ Sin política | Definir: historial_pagos X años, conversaciones X meses | CLAU |
| Terceros que reciben datos | ⚠️ Evolution API / Payphone | Incluir en política de privacidad | CODI |

---

## SISTEMA DE ALERTAS TELEGRAM

### Arquitectura

Álvaro recibe alertas en Telegram de:
- Errores en cualquier workflow n8n (WF5 actualizado)
- Caída/reconexión de sesión WhatsApp (Evolution API monitor)
- Deploy completado/caído en Coolify (webhook → n8n → Telegram)
- Alertas de Glitchtip (errores JS frontend y BFF)
- Eventos de negocio: nuevo lead, pago validado, error de comprobante

### Implementación paso a paso

**Paso 1 — Crear bot (CLAU, ~15 min):**
1. Crear bot en Telegram via `@BotFather` → obtener `BOT_TOKEN`
2. Enviar cualquier mensaje al bot y consultar `https://api.telegram.org/bot{TOKEN}/getUpdates` → obtener `CHAT_ID` de Álvaro
3. Configurar en n8n: `$env.TELEGRAM_BOT_TOKEN` y `$env.TELEGRAM_CHAT_ID`

**Paso 2 — WF5 actualizado (CODO, después de Paso 1):**
- Agregar nodo Telegram al WF5 Error Handler además del email actual
- Formato de mensaje:
```
🔴 [ERROR] {nombre_workflow}
Cliente: {id} - {nombre}
Error: {mensaje}
{timestamp UTC-5}
```

**Paso 3 — Alertas de negocio opcionales (CODO):**
- WF3 comprobante validado → `🟢 [PAGO] {cliente} | ${monto} | {banco}`
- WF4 nuevo lead → `🟡 [LEAD] {nombre} | {email} | {programa}`
- WF6 error de envío → `🔴 [WA ERROR] No se pudo enviar recordatorio a {cliente}`

**Paso 4 — Monitoreo sesión WhatsApp (CLAU):**
- WF nuevo (cron cada hora): consultar `GET /instance/connectionState/jauriacrossfit` en Evolution API
- Si estado ≠ `open` → enviar alerta Telegram: `⚠️ [WHATSAPP] Sesión caída — reconectar en el panel admin`
- Nombre del workflow: WF-WHATSAPP-MONITOR

**Paso 5 — Coolify → Telegram (CLAU):**
- Coolify Settings → Notifications → webhook URL de n8n
- n8n recibe el evento y lo formatea para Telegram

**Paso 6 — Glitchtip → Telegram (CLAU):**
- Glitchtip tiene webhooks de alerta configurables
- Apuntar webhook a endpoint n8n que reenvía a Telegram

---

## WIDGET SESIÓN WHATSAPP EN PANEL ADMIN

**Issue a crear: `[CODI] Widget estado sesión WhatsApp + botón reconectar`**

El coach debe poder ver y reconectar la sesión sin tocar Evolution API directamente:
- Widget en Dashboard o sidebar: `● WhatsApp: Conectado / Desconectado`
- Botón "Reconectar" → llama BFF → BFF llama Evolution API `POST /instance/connect`
- Esto requiere que el BFF exponga un endpoint proxy para Evolution API (parte de ALV-97)

---

## BLOQUEADORES ACTUALES

| Componente | Estado |
|---|---|
| NestJS BFF | ❌ Solo localhost:4400 |
| Admin Panel Angular | ❌ Solo localhost:4300 |
| Landing Page | ❌ Solo localhost (+ bug ng build) |
| Dominio `jauriacrossfit.com` | ❌ Pendiente compra |
| Payphone producción | ⚠️ Esperando token real + RUC coach |
| Número WhatsApp dedicado | ❌ Pendiente compra SIM |
| Correo profesional coach | ❌ Pendiente contratación |

---

## TAREAS EJECUTABLES HOY (sin esperar el dominio)

### CLAU:
- [ ] WF6: eliminar fallback API key hardcodeada (2 nodos)
- [ ] Crear Telegram bot + configurar `$env.TELEGRAM_BOT_TOKEN` y `$env.TELEGRAM_CHAT_ID` en n8n
- [ ] Validar que las 14 variables `$env` en n8n tienen valores reales
- [ ] Crear issues Linear: Telegram, compliance, widget WhatsApp, monitor sesión, manual coach
- [ ] Crear `WF-WHATSAPP-MONITOR` (cron hourly, alerta sesión caída)

### CODI:
- [ ] Rate limiting BFF: agregar `@nestjs/throttler` en NestJS
- [ ] Commitear ~25 archivos pendientes en `jauria-admin`
- [ ] ALV-89: migrar pantalla Workflows al BFF

### CODO:
- [ ] WF5: agregar nodo Telegram al Error Handler (después de que CLAU configure el bot)
- [ ] WF6: eliminar fallback `|| 'EvoApiJauria2026_...'` y URL hardcodeada
- [ ] ALV-135: validar que WF1 recordatorio ya está en 3 días (system/STATE.md dice que está parcheado)

---

## PLAN DE DEPLOY (cuando llegue el dominio)

### Paso 1 — Pre-deploy (CLAU)
- [ ] Datos reales del coach (ALV-50)
- [ ] Fix secrets en `environment.prod.ts` (ALV-97)
- [ ] CORS del BFF actualizado a dominio prod
- [ ] Supabase Redirect URLs: solo dominios prod

### Paso 2 — Email remitente Jauría (CODO, después de que coach tenga correo)
- [ ] Re-autenticar credencial Gmail en n8n con cuenta Jauría
- [ ] Actualizar WF1, WF2, WF4, WF5, WF6 con nueva credencial
- [ ] "From Name" = "Jauría CrossFit" en todos

### Paso 3 — Coolify deploy (CLAU)
```
jauria-api   → https://api.jauriacrossfit.com    (BFF NestJS)
jauria-admin → https://admin.jauriacrossfit.com  (Angular admin)
jauria-web   → https://jauriacrossfit.com        (Landing page)
```
- SSL automático Traefik
- Secrets en Coolify (no en repo)
- Restart policy: always
- Health check: `GET /api/v1/health`

### Paso 4 — PWA (CODI, post-deploy)
- [ ] `ng add @angular/pwa`
- [ ] manifest: nombre "Jauría CrossFit", ícono lobo rojo, color `#B71C1C`
- [ ] ngsw-config: cache assets + rutas
- [ ] Validar banner "Instalar" en Chrome Android

### Paso 5 — Payphone (cuando coach tenga RUC + cuenta bancaria)
- [ ] Token real en Coolify secrets (BFF) + n8n env
- [ ] Registrar `https://n8n.alvarodevrace.tech/webhook/payphone-notificacion` en panel Payphone
- [ ] Test con pago real

### Paso 6 — Migración número WhatsApp (CLAU + coach)
- [ ] Coach compra SIM dedicada y configura WhatsApp Business
- [ ] CLAU coordina migración en Evolution API (~20 min downtime)
- [ ] Re-test WF3 y WF6 con número nuevo

---

## COMPLIANCE — Mínimo viable para go-live (CODI)

- [ ] Checkbox consentimiento WhatsApp en formulario de registro de atleta
- [ ] Página `/privacidad` en landing con:
  - Datos recopilados: nombre, teléfono, email, comprobantes de pago
  - Finalidad: gestión de membresías y cobros
  - Terceros: Supabase (almacenamiento), Evolution API (WhatsApp), Payphone (pagos)
  - Derecho a solicitar eliminación: contactar a `coach@jauriacrossfit.com`

---

## CHECKLIST PRE-LAUNCH COMPLETO

### Seguridad:
- [ ] WF6: fallback API key Evolution removida
- [ ] WF6: URL Evolution → variable `$env`
- [ ] ALV-97: proxies BFF para `coolifyToken` y `evolutionApiKey`
- [ ] Rate limiting en BFF (`@nestjs/throttler`)
- [ ] CORS BFF: solo dominio prod

### n8n:
- [ ] 14 variables `$env` con valores reales
- [ ] `CORREO_ADMIN` = `alvarodevrace@outlook.com` ✅
- [ ] `CORREO_COACH` = email profesional del coach
- [ ] Credencial Gmail = cuenta `@jauriacrossfit.com`
- [ ] Telegram bot configurado en WF5

### Coolify/Infra:
- [ ] 3 servicios con dominios y SSL
- [ ] Secrets en Coolify (no en código)
- [ ] Restart policies activas
- [ ] Webhooks Coolify → Telegram configurados

### Monitoreo:
- [ ] WF-WHATSAPP-MONITOR activo (cron hourly)
- [ ] Glitchtip webhook → Telegram configurado
- [ ] DSN correcto en prod (BFF + Admin + Landing)

### Supabase:
- [ ] Redirect URLs: solo dominios prod
- [ ] RLS leads: INSERT desde anon

### Compliance LOPDP:
- [ ] Consentimiento WhatsApp en registro de atleta
- [ ] Página `/privacidad` en landing

### Datos:
- [ ] ALV-50: datos reales del coach
- [ ] ALV-53: landing-content.ts actualizado
- [ ] ALV-132: horarios corregidos ✅

### Payphone:
- [ ] Token real
- [ ] Webhook registrado
- [ ] Test de pago real

### Coach profesional:
- [ ] Número WhatsApp dedicado (SIM gym) configurado en Evolution API
- [ ] Correo profesional `@jauriacrossfit.com` operativo
- [ ] Credencial Gmail n8n actualizada con correo del gym
- [ ] RUC activo + cuenta bancaria separada
- [ ] Manual de operación entregado al coach (`docs/coach-manual.md`)
- [ ] Widget estado sesión WhatsApp en panel admin operativo

---

## ISSUES LINEAR A CREAR

| # | Título | Owner | Prioridad |
|---|---|---|---|
| ALV-136 | Telegram bot: configurar bot + alertas WF5 + monitor sesión WA | CLAU | High |
| ALV-137 | Widget estado sesión WhatsApp + botón reconectar en panel admin | CODI | Normal |
| ALV-138 | Compliance LOPDP: consentimiento WhatsApp + página /privacidad | CODI | High |
| ALV-139 | Manual de operación para el coach | CLAU | Normal |
| ALV-140 | Migración número WhatsApp a número dedicado del gym | CLAU | Normal |
| ALV-141 | Coolify + Glitchtip: webhooks hacia n8n → Telegram | CLAU | Normal |
