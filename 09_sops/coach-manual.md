# Manual de Operación — Jauría CrossFit
> Para el coach. Versión 1.0 — 2026-03-17

---

## Lo que hace el sistema solo (no necesitas hacer nada)

| Acción automática | Cuándo ocurre |
|---|---|
| Recordatorio de cobro por WhatsApp | Todos los días a las 8:00 AM — solo a clientes con vencimiento en 3 días o menos |
| Validación de comprobante de pago | Inmediatamente cuando un cliente te envía una imagen por WhatsApp |
| Actualización de estado del cliente | Automático cuando el comprobante es válido (pasa a "Activo") |
| Notificación de nuevo lead | Cuando alguien llena el formulario en la página web |
| Confirmación de pago Payphone | Automático cuando se procesa el pago online |

---

## Lo que haces tú

### Gestión de clientes

- **Crear un nuevo cliente** → Panel admin → Clientes → Nuevo cliente
  - Llenar: nombre, teléfono (formato: `593XXXXXXXXX`, sin el `+`), email, plan, fecha de vencimiento
  - Marcar checkbox "Acepta recibir mensajes por WhatsApp" si el atleta dio su consentimiento
- **Renovar membresía** → busca el cliente → actualizar fecha de vencimiento y estado
- **Marcar pago recibido manualmente** (si hubo problema con el comprobante) → editar cliente → cambiar estado a "Activo"

### Recordatorio manual

Cuando quieres enviar un recordatorio puntual a un cliente específico:
→ Panel admin → Clientes → buscar cliente → botón "Enviar recordatorio"

El mensaje sale por WhatsApp desde el número del gym de forma inmediata.

### Reconectar WhatsApp si se cae

La conexión de WhatsApp se puede caer espontáneamente. Sabrás que pasó porque:
- Recibirás una alerta en Telegram: `⚠️ [WHATSAPP] Sesión caída`
- Los clientes dejarán de recibir mensajes automáticos

**Cómo reconectar:**
1. Abre el panel admin → Dashboard
2. Busca el widget "Estado WhatsApp" (verde = conectado, rojo = desconectado)
3. Si está rojo → clic en "Reconectar"
4. Abre WhatsApp en el teléfono del gym → escanea el QR que aparece en pantalla
5. El widget cambia a verde → listo

---

## Alertas que recibirás en Telegram

| Mensaje | Qué significa | Qué hacer |
|---|---|---|
| `🔴 [ERROR] WF1...` | El recordatorio automático falló para un cliente | Revisar con Álvaro si se repite |
| `⚠️ [WHATSAPP] Sesión caída` | El chatbot está desconectado | Reconectar desde el panel (ver arriba) |
| `🟢 [PAGO] ...` | Comprobante validado exitosamente | No necesitas hacer nada |
| `🟡 [LEAD] ...` | Nuevo prospecto del formulario web | Contactar al prospecto |
| `🔴 [JS ERROR] ...` | Error técnico en el sistema | Avisar a Álvaro |

---

## Casos frecuentes

### "Un cliente dice que no recibió el recordatorio"
1. Verifica que tiene el checkbox de WhatsApp marcado en su ficha
2. Verifica que el teléfono está en formato `593XXXXXXXXX` (sin `+`, sin espacios)
3. Si todo está correcto, avisa a Álvaro

### "El comprobante no se validó automáticamente"
El sistema rechaza imágenes borrosas, recortadas o que no sean comprobantes claros.
- Pide al cliente que reenvíe la imagen con mejor calidad
- Si sigue fallando, válida el pago manualmente en el panel

### "Un cliente figura como activo pero no pagó"
- Busca el cliente en el panel → verifica historial de pagos
- Si no hay comprobante válido, cambia el estado a "Vencido" manualmente

### "WhatsApp no responde pero el widget dice conectado"
- Cierra y vuelve a abrir el panel
- Si sigue igual, avisa a Álvaro

---

## Qué NO debes tocar nunca

- **n8n** (`n8n.alvarodevrace.tech`) — solo Álvaro
- **Evolution API** (`evolution.alvarodevrace.tech`) — solo Álvaro (usa el panel para lo de WhatsApp)
- **Supabase** — solo Álvaro
- **Coolify** — solo Álvaro

Si algo no funciona y no está cubierto en este manual → escribe a Álvaro con una captura de pantalla.

---

## Contacto técnico

**Álvaro Carrera** — `alvarodevrace@outlook.com`
Para incidentes urgentes: Telegram directo
