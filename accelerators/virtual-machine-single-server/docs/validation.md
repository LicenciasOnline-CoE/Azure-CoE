# Lista de aceptación

Esta lista define pruebas futuras; **no es evidencia de pruebas ejecutadas**. Usar datos sintéticos. Registrar fuera del repositorio identificadores del entorno cuando sean necesarios; publicar solo resultados sanitizados, sin IP/tenant/suscripción reales, cuentas, secretos ni contenido de usuarios.

| Área | Criterio verificable |
| --- | --- |
| Alcance | Escenario, SO, propietario, presupuesto y riesgo de single-server aprobados; decisiones pendientes resueltas para el entorno. |
| Capacidad | Prueba sostenida de pico cumple latencia p95/p99, errores, batch y margen acordado; registra CPU/RAM/IOPS/MB/s/red y no depende de burst. |
| Red | NIC sin IP pública; acceso permitido desde origen aprobado y rechazado desde orígenes no autorizados, incluida otra subred. Salida y DNS funcionan solo según dependencias aprobadas. |
| Web, si aplica | HTTPS frontend/backend válido, WAF en prevención y pruebas controladas de bloqueo; origen privado sin bypass, autenticación de app y probes sanos. |
| Administración | RDP/SSH privado permitido solo a operadores autorizados; login no autorizado rechazado; Entra/MFA según flujo compatible y acceso de emergencia auditado. |
| Archivos, si aplica | FTP plano/anonymous deshabilitados; SFTP o FTPS compatible, identidad aislada, permisos mínimos y auditoría; no usar WAF como control del protocolo. |
| DB/legacy, si aplica | Puertos restringidos, TLS donde aplique, cuenta de servicio mínima, licencias/soporte y consistencia del backup comprobados. |
| SO y endpoint | Imagen soportada, parches, reinicios, hardening, estado de Defender/EDR y Trusted Launch compatible revisados. |
| Identidad y secretos | Roles por alcance, sin secretos en parámetros/logs/outputs; Key Vault privado con purge protection; distribución/renovación probadas. |
| Observabilidad | Métricas y eventos de SO/app llegan al destino y retención acordados; alertas llegan al operador y existe runbook. |
| Backup | Protección habilitada, discos correctos, copia inicial y programada exitosas, punto con consistencia exigida y alerta por incumplimiento. |
| Restauración | Restore aislado desde punto válido (incluido vault tier), datos y app funcionales, identidad/red revisadas y RPO/RTO medidos dentro del objetivo. |
| Gobierno | Naming/hostname válidos, tags o excepciones, políticas aplicables evaluadas y permisos de operación documentados. |
| Automatización futura | Bicep/AVM versionados, lint/build y validación automatizada exitosos; ARM validate/what-if con entorno; despliegue OIDC protegido y ejemplo funcional. |
| Cierre | Recursos temporales identificados/retirados, evidencia sanitizada, operación aprobada, propietario y release definidos. |

Si un criterio no aplica, justificarlo por perfil. No tratar un control fallido como no aplicable. La etapa documental no cumple todavía los criterios de automatización, despliegue ni pruebas reales, por lo que el acelerador permanece en diseño.

## Registro mínimo por prueba

Fecha, versión del activo, perfil/SO/SKU sin identificadores sensibles, procedimiento, criterio esperado, resultado medido, conformidad, hallazgo, acción y referencia de evidencia sanitizada. Para restore, agregar antigüedad/consistencia del punto y tiempos hasta servicio utilizable.
