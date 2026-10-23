# Operación

Asignar propietario operativo, contactos en un sistema autorizado, horario de soporte, RPO/RTO, SLO, ventanas y escalamiento. No guardar datos personales en el repositorio.

## Señales y alertas

| Señal | Acción requerida |
| --- | --- |
| Disponibilidad de aplicación/health probe | Prueba sintética desde el camino real de usuarios; diferenciar caída de app, red y VM. VM encendida no implica servicio sano. |
| CPU, memoria, paginación y créditos B | Revisar picos representativos con los guardrails de [dimensionamiento](sizing.md); ajustar por SLO, no por promedio diario. |
| Espacio, latencia y saturación de disco/VM | Vigilar datos, logs y SO; anticipar crecimiento y no ampliar solo GiB para un cuello de botella de VM. |
| Backup y último punto válido | Alertar por falla, consistencia insuficiente o antigüedad fuera del RPO; el operador debe investigar y comprobar recuperación. |
| Accesos, Defender y WAF | Alertar por login anómalo, malware, cambios de reglas, desprotección y bypass; conservar trazabilidad sanitizada. |
| Certificados y claves | Renovar antes del vencimiento; validar backend y procedimientos de recuperación de claves. |
| Pérdida de telemetría | Alertar por agente/DCR sin datos; ausencia de eventos no significa ausencia de incidentes. |
| Service Health/Resource Health y presupuesto | Planificar mantenimiento, responder incidentes Azure y revisar costo frente a consumo esperado. |

Enviar métricas de plataforma y diagnósticos a destinos aprobados. En el guest, usar AMA/DCR para eventos Windows o syslog y contadores necesarios; no asumir que activar diagnósticos de recurso captura todos los eventos de SO o aplicación. Parametrizar retención, limitar ingesta y probar action groups sin datos reales.

## Rutina

- **Diaria o según criticidad:** fallas de jobs, último punto de backup válido, alertas de seguridad, espacio y salud de app/agentes.
- **En cada ventana:** parchear SO/runtime/motor, confirmar compatibilidad, revisar backup válido y rollback, reiniciar si aplica y validar app y protección después.
- **Mensual:** capacidad/Advisor, costo, roles, accesos, certificados, soporte de imagen y excepciones de seguridad. Redimensionar con prueba y ventana.
- **Periódica según RTO/riesgo:** restauración aislada, ejercicio de incidentes y revisión de hardening. Como punto inicial, restore trimestral y después de cambios relevantes.

No automatizar reinicios o cambios de SKU sin tener en cuenta que existe una sola instancia. Backups previos a mantenimiento no eliminan la indisponibilidad ni garantizan rollback instantáneo.

## Incidente

1. Clasificar impacto y preservar logs. En sospecha de compromiso, aislar red de forma controlada sin destruir evidencia.
2. Bloquear identidades/accesos comprometidos y coordinar respuesta de seguridad. No asumir que el backup más reciente está limpio.
3. Elegir punto de recuperación íntegro y modalidad compatible con el objetivo de negocio; recuperar en red aislada.
4. Validar app, seguridad, datos y dependencias; autorizar retorno a servicio, rotación de secretos y cambio de tráfico.
5. Registrar RPO/RTO real, causa y mejoras. Si el objetivo no puede alcanzarse con una sola VM, cambiar arquitectura en lugar de prometerlo con un backup más frecuente.
