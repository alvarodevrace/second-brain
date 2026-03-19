# Patrones críticos y bugs conocidos — n8n

> Guía de referencia para CODO y CLAU. Aquí viven los patrones que queman tiempo si no se conocen.

---

## Patrones de nodos Supabase

### UPDATE — usar `fieldsUi.fieldValues` (NO `fieldsUi.values`)

El nodo Supabase para UPDATE espera la estructura:

```json
{
  "fieldsUi": {
    "fieldValues": [
      { "fieldId": "campo", "fieldValue": "valor" }
    ]
  }
}
```

**Incorrecto** (genera error silencioso): usar `fieldsUi.values`.

---

### INSERT después de UPDATE — usar HTTP Request REST

Si en un mismo flujo hay un UPDATE de Supabase seguido de un INSERT, **no usar el nodo Supabase para el INSERT**. Usar un nodo HTTP Request con la REST API de Supabase directamente.

**Por qué:** el nodo Supabase pierde el `pairedItem` chain después de ciertos nodos UPDATE, lo que rompe el mapeo de datos en el INSERT subsiguiente.

Ejemplo de endpoint REST:
```
POST https://bxatcmcommoqnxnyqchu.supabase.co/rest/v1/historial_pagos
Authorization: Bearer <service_role_key>
Content-Type: application/json
```

---

## Patrones de nodos HTTP Request

### typeVersion — mantener en v4.2

**No actualizar** el nodo HTTP Request a typeVersion `v4.4`. La versión v4.4 introduce cambios en el manejo de headers y body que rompen integraciones existentes.

Versión segura: `"typeVersion": 4.2`

---

### ❌ n8n REST API `/workflows/:id/run` no existe

La API pública de n8n v1 NO tiene endpoint para ejecutar un workflow directamente.
Llamar `POST /api/v1/workflows/:id/run` devuelve `405 POST method not allowed`.

**Patrón correcto desde BFF NestJS:**
- Usar el **webhook trigger** del workflow
- Llamar `POST {N8N_BASE_URL_sin_api_v1}/webhook/{path_del_nodo_webhook}`
- No enviar `X-N8N-API-KEY` en webhooks (son públicos)

**La API REST solo sirve para:**
- `GET /api/v1/workflows` — listar workflows
- `GET /api/v1/workflows/:id` — leer un workflow
- `GET /api/v1/executions` — consultar ejecuciones

**En env.ts del BFF:**
```typescript
get n8nWebhookBaseUrl() {
  return env.n8nBaseUrl.replace(/\/api\/v1\/?$/, '') + '/webhook';
}
```
Detectado: 2026-03-15 en `POST /api/v1/clients/C001/send-reminder`

---

## Patrones de Gmail

### Agregar `parameters.operation: "send"` explícito

El nodo Gmail no siempre infiere la operación correctamente. Siempre agregar:

```json
{
  "parameters": {
    "operation": "send",
    ...resto de params
  }
}
```

Sin esto, el nodo puede fallar silenciosamente o ejecutar una operación incorrecta.

---

## Patrones de expresiones y Code nodes

### `$env.*` — funciona en expresiones, NO en Code nodes

En **expresiones** de nodos (campo de texto con `{{ }}`): `$env.MI_VAR` funciona correctamente.

En **Code nodes** (JavaScript): `$env` no está disponible. Usar `$vars` o pasar el valor como parámetro desde un nodo anterior.

---

### ⚠️ Teléfonos: siempre sin prefijo `+`

Toda la plataforma usa números de teléfono **sin** prefijo `+`:
- `clientes.telefono_whatsapp` → `593960463743`
- `conversaciones_whatsapp.telefono_whatsapp` → `593960463743`
- Evolution API campo `number` → `593960463743`

Evolution API devuelve el número como `593960463743@s.whatsapp.net`.
Al hacer `remoteJid.split('@')[0]` se obtiene `593960463743` — **NO agregar `+`**.

**En WF3 nodo `Parsear Mensaje WA`:**
```javascript
const rawPhone = remoteJid.split('@')[0];
const phone = rawPhone.replace(/^\+/, '');  // ✅ correcto
// NO: rawPhone.startsWith('+') ? rawPhone : '+' + rawPhone  ❌
```

Si se agrega `+`, `Buscar Conv Activa` nunca encuentra la conversación creada por WF1
→ siempre `estado: sin_contexto` → el atleta no recibe respuesta de la IA.

Detectado y corregido: 2026-03-15 en WF3 ejecuciones 1407–1412

---

## Patrones de Coolify

### Actualizar env vars en bulk

```
PATCH /api/v1/services/{uuid}/envs/bulk
```

Este endpoint funciona correctamente para actualizar múltiples env vars de un servicio en una sola llamada. Es el método recomendado para sincronizar configuración desde el admin panel.

---

## Comportamientos conocidos de Evolution API

### Shape de respuesta de instancias

No asumir shape anidado `instance.instanceName`. El contrato real incluye:
- `name`
- `connectionStatus`

Verificar contra respuesta real si el contrato cambia.

---

*Owner: CODO (mantenimiento) · CLAU (plataforma)*
*Última actualización: 2026-03-19*
