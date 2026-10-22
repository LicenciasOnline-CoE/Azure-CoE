# Despliegue

No hay infraestructura ni comandos desplegables en esta etapa. Esta guía define el procedimiento que deberá completarse y probarse junto con Bicep.

## Prerrequisitos por resolver

- Aprobar las decisiones de [arquitectura](architecture.md), región, capacidad, costos y responsable de operación.
- Disponer de un entorno Azure autorizado, permisos mínimos para recursos/red/DNS y permisos específicos de asignación de roles cuando sean necesarios; no usar Owner por defecto.
- Contar con un administrador Entra de PostgreSQL, preferentemente un grupo, y una identidad separada para bootstrap y migraciones.
- Preparar dominio/certificado, registro de aplicación Entra, redirects y configuración del proxy para autenticación de usuarios.
- Definir conectividad privada y DNS para administración, bootstrap y publicación por SCM; un runner público estándar no adquiere esa conectividad mediante OIDC.
- Proporcionar parámetros reales fuera del repositorio y configurar federación OIDC y entornos protegidos de GitHub.

## Secuencia prevista

1. Ejecutar lint/build, validación de parámetros y revisión de políticas de seguridad.
2. Ejecutar validación ARM y what-if en un entorno autorizado; revisar cambios destructivos, RBAC y costos. What-if no valida permisos SQL ni conectividad de la aplicación.
3. Aprovisionar red, DNS, identidad, servicios PaaS y observabilidad según dependencias.
4. Configurar el origen privado de Front Door, aprobar su conexión y verificar HTTPS; mantener bloqueado el acceso público directo.
5. Desde una ruta privada autorizada, registrar la identidad de aplicación en PostgreSQL y asignar permisos mínimos; ejecutar migraciones con identidad separada.
6. Publicar la aplicación, configurar autenticación y verificar resolución DNS, tokens y health checks.
7. Ejecutar las pruebas de [seguridad](security.md), pruebas funcionales y de restauración. Registrar solo resultados sanitizados y aprobar la promoción.

## Criterios de aceptación

Entrada pública funcional para usuarios autorizados, ausencia de bypass del WAF, base de datos privada, telemetría visible, restauración comprobada y documentación reproducible. El procedimiento detallado y el rollback por componente se completarán cuando existan IaC y aplicación; no se considera listo para producción antes de ello.