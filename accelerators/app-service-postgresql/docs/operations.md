# Operación

Requisitos operativos del diseño, no runbooks ejecutados. Los umbrales, retenciones, responsables y objetivos deben acordarse antes del despliegue.

## Observabilidad

| Señal | Acción prevista |
| --- | --- |
| Disponibilidad y HTTP 5xx | Correlacionar Front Door, salud del origen y errores de aplicación; alertar según SLO. |
| Bloqueos WAF y picos de solicitudes | Detectar abuso y falsos positivos sin desactivar globalmente la protección. |
| CPU, memoria, latencia y colas | Ajustar capacidad y autoscaling con máximos aprobados y presupuesto. |
| Conexiones, almacenamiento y latencia PostgreSQL | Revisar pool total, consultas e índices antes de aumentar capacidad. |
| Errores de autenticación y auditoría | Revisar renovación de tokens, roles, cambios administrativos y eventos anómalos. |
| Certificados, DNS y conexión privada | Detectar expiración o fallo de resolución/ruta sin abrir acceso público como solución. |

Centralizar diagnósticos y auditoría con retenciones parametrizadas; limitar acceso a logs. Mantener presupuestos y alertas de gasto para cómputo, HA, Front Door, tráfico y telemetría.

## Disponibilidad y recuperación

- Elegir SKU e instancias de App Service compatibles con resiliencia zonal si se requiere; confirmar soporte regional. Health checks no crean redundancia por sí solos.
- Evaluar HA de PostgreSQL y backups automáticos con retención definida. HA protege frente a ciertos fallos, no frente a borrado lógico ni pérdida regional.
- Definir RPO/RTO, retención y necesidad de redundancia geográfica antes de crear el servidor; algunas elecciones no se pueden cambiar sin migración.
- Probar restauración a un servidor separado y verificar datos, DNS, permisos Entra y reconexión. Medir tiempo y pérdida de datos, sin prometer objetivos no probados.
- Reintentar conexiones tras failover con backoff y límites; evitar repetir escrituras no idempotentes sin control.

## Cambios e incidentes

Aplicar parches y actualizar runtime, versiones de PostgreSQL y dependencias dentro de soporte. Revisar cambios de esquema por compatibilidad; revertir aplicación no revierte automáticamente una migración destructiva. La estrategia de slots requiere revisión de compatibilidad con el ingreso Private Link elegido y no se da por implementada.

Los futuros runbooks deben cubrir caída del origen, fallo de token, DNS privado, saturación, restauración y rollback. Registrar pruebas con datos sintéticos y resultados sanitizados; no adjuntar logs crudos al repositorio. La operación requiere un responsable técnico asignado.