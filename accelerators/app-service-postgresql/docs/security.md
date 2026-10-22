# Seguridad

Controles objetivo, todavía no implementados. Consulta los límites y decisiones en la [arquitectura](architecture.md).

## Excepción de exposición pública

La aplicación debe ser consumible desde Internet por requisito confirmado. La excepción afecta solo a la entrada web, no a PostgreSQL ni a interfaces administrativas. La recomendación es publicar mediante Front Door Premium con WAF y mantener el origen App Service privado mediante Private Link, deshabilitando explícitamente su acceso público.

La publicación pública no autoriza acceso anónimo a funciones o datos. El diseño inicial exige Entra ID y autorización por función. Cualquier ruta anónima deberá tener propósito y controles aprobados. El responsable y la revisión periódica de la excepción deben asignarse antes de desplegar.

## Amenazas y controles

| Riesgo | Control requerido |
| --- | --- |
| Bypass del WAF | Origen sin acceso público; solo conexiones privadas aprobadas. Probar acceso directo al hostname de App Service. |
| Abuso y ataques HTTP | WAF en prevención, reglas administradas, rate limiting ajustado, límites de payload y validación de entrada. |
| Acceso indebido | Entra para usuarios, autorización de aplicación y pruebas de permisos. WAF no reemplaza autorización ni correcciones de código. |
| Robo de credenciales | Identidad administrada y PostgreSQL Entra-only; sin autenticación básica de publicación ni contraseñas en settings. |
| Escalada en base de datos | Rol de ejecución limitado a objetos y operaciones necesarios; identidad separada para migraciones. Nunca administrador en runtime. |
| Intercepción | TLS 1.2+ en saltos compatibles; cliente PostgreSQL con validación de certificado y hostname. Sin desactivar verificación TLS. |
| Inyección SQL | Consultas parametrizadas, validación y mínimo privilegio; WAF no sustituye estos controles. |
| Fuga por telemetría | No registrar tokens, datos personales, cuerpos ni parámetros sensibles; revisar `pgaudit` para evitar capturar datos de sentencias. |
| Pipeline comprometido | OIDC, mínimo RBAC, revisión de cambios, acciones fijadas y entornos protegidos; no identidades privilegiadas para PR no confiables. |

## Diagnóstico y auditoría

Habilitar categorías soportadas de App Service, Front Door/WAF y PostgreSQL, Activity Log y telemetría de aplicación. Configurar auditoría PostgreSQL mediante `pgaudit`, con categorías seleccionadas y exportación de logs. Parametrizar destinos y retenciones; no confundir retención del workspace con retención de backups. Revisar el impacto de costo y rendimiento.

Si se incorpora Key Vault, usar identidad administrada, RBAC mínimo, red privada, diagnósticos y controles de recuperación. No guardar secretos en parámetros, outputs o artefactos del pipeline.

## Comprobaciones para la implementación futura

- [ ] Solo la entrada aprobada responde desde Internet; PostgreSQL y el origen público de App Service no son accesibles directamente.
- [ ] WAF está asociado a las rutas públicas, en prevención, y se prueba sin datos reales.
- [ ] Usuario sin autenticar y usuario sin permisos no acceden a funciones protegidas.
- [ ] La aplicación conecta por DNS privado, TLS validado y token de identidad administrada.
- [ ] El rol SQL de ejecución no puede administrar roles, cambiar esquemas ni acceder a objetos ajenos.
- [ ] SCM, FTP y publicación básica no abren una vía administrativa pública.
- [ ] Se generan y consultan eventos de auditoría, sin datos sensibles.
- [ ] Azure Policy y la revisión de IaC verifican la línea base; las excepciones se documentan.

Ninguna casilla representa una prueba ejecutada en esta etapa documental.