# ADR: Decisiones de Arquitectura Post-Demo y Roadmap SaaS

> **Estado:** Aprobado
> **Fecha:** 2026-03-20
> **Contexto:** Sesión de triaje de arquitectura tras la primera demo funcional del sistema Jauría CrossFit.

---

## 1. Objetivo SaaS Multi-Gym
**Decisión:** El sistema se rediseñará para ser un SaaS (Software as a Service) capaz de servir a múltiples gimnasios independientes. Jauría CrossFit se mantiene como el "Prototipo 0" y cliente principal.

**Razón:** Existe interés de terceros y el modelo de negocio escalable requiere multi-tenencia.

---

## 2. Diferimiento de Multi-Tenant (Base de Datos)
**Decisión:** La migración técnica a un esquema con `gym_id` en todas las tablas se **difiere** hasta la confirmación de un segundo cliente real.

**Razón:** El esfuerzo de refactorización de RLS, BFF y Frontend no se justifica mientras solo haya un cliente activo. Se mantiene la deuda técnica controlada en [JAU-172](https://linear.app/alvarodevrace/issue/JAU-172).

---

## 3. Diferimiento de Gateway de IA (LiteLLM / Langfuse)
**Decisión:** No se implementará LiteLLM ni Langfuse en la fase actual.

**Razón:** El volumen de llamadas a IA (principalmente en el workflow de validación de comprobantes) es bajo y el costo es manejable directamente vía OpenRouter. Se reevaluará cuando el volumen crezca o haya 5+ gimnasios activos.

---

## 4. Estrategia de Orquestación de Agentes (VALHALLA)
**Decisión:** Evolucionar el plan "OpenClaw" hacia **VALHALLA**, un sistema de orquestación basado en suscripciones CLI sobre API.

**Componentes:**
- **Worker local (Dell Inspiron):** Ejecución 8am-10pm vía Docker + systemd.
- **Verdad Absoluta:** Los agentes (CLAU, CODI, CODO, GEMA) leen su estado y memoria desde Supabase (Proyecto `valhalla`), eliminando la dependencia de archivos `.md` locales que se desincronizan.
- **Eficiencia:** Sistema de jobs con prioridad y budget de tokens diario por agente.
- **Dashboard:** "Ojo de Dios" en Angular 18 para monitoreo en tiempo real.

---

## 5. Resumen de Herramientas
- **BFF (NestJS):** Centro único de lógica y proxy de seguridad.
- **n8n:** Orquestador de flujos de trabajo y webhooks.
- **Supabase:** Fuente de verdad para datos de negocio y ahora para memoria de agentes.
- **Evolution API:** Gateway de WhatsApp.
- **Coolify:** Panel de control de infraestructura y despliegue continuo.
