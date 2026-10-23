# Despliegue

No hay infraestructura desplegable en esta etapa. Esta secuencia es el contrato para la implementación futura; el procedimiento manual de [respaldo](backup.md) puede utilizarse en una VM existente compatible, bajo autorización.

## Prerrequisitos

- Aprobar escenario, propietario, imagen/SO/licencias, región/zona, dimensionamiento, presupuesto, RPO/RTO y riesgo de single-server.
- Comprobar disponibilidad de SKU, cuota regional/por familia, imagen GA soportada y compatibilidad de Trusted Launch, discos, agentes y backup.
- Disponer de red privada, rangos no solapados, DNS, salida controlada y ruta administrativa funcional. No improvisar una IP pública para resolver conectividad.
- Revisar políticas Azure aplicables; permisos de red, recursos, monitoreo y backup acotados. Separar permisos para asignar roles de los necesarios para desplegar; no usar Owner por defecto.
- Preparar grupos Entra de login/operación, federación OIDC de CI/CD y entornos protegidos. Usar valores reales solo en configuración externa autorizada, nunca en commits.
- Definir certificados, distribución desde Key Vault, protección de emergencia y bootstrap del SO sin contraseñas embebidas.
- Para web pública, aprobar dominio, WAF, certificados frontend/backend y costos. Para RDS/legacy, confirmar soporte y licencias del fabricante.

## Secuencia prevista

1. Resolver decisiones de [arquitectura](architecture.md) y seleccionar perfil con [dimensionamiento](sizing.md) y [seguridad](security.md).
2. Cuando exista Bicep: lint/build, validación de parámetros/políticas y revisión de seguridad automatizada. Ejecutar validación ARM y what-if en entorno autorizado; revisar destrucciones, RBAC y costos.
3. Preparar red, DNS, rutas, administración y Key Vault. Aprovisionar observabilidad, bóveda y controles de backup antes de registrar protección.
4. Aprovisionar identidad, VM, discos, NIC/NSG y extensiones compatibles. Comprobar acceso privado y agentes antes de endurecer reglas adicionales.
5. Configurar SO, hardening, parches, firewall, protección de endpoint y auditoría. Rechazar imagen no soportada o bootstrap que deje credenciales en outputs.
6. Instalar **solo** el perfil de aplicación aprobado, sin endpoints de administración públicos; configurar certificados, secretos e identidades.
7. Si se publica web, preparar WAF y probes, ajustar en entorno de prueba y habilitar prevención antes de dirigir tráfico productivo.
8. Habilitar [backup](backup.md), completar copia inicial/programada y restauración de prueba.
9. Ejecutar [validación](validation.md), probar alertas y registrar evidencia sanitizada. Aprobar operación y publicación solo después de cumplir todos los controles.

## Rollback y límites

Un despliegue ARM no es transaccional y what-if no prueba conectividad, licencias, consistencia de DB ni restauración. Mantener configuración versionada, respaldo previo válido, ventana y pasos específicos de reversión del guest/app. No suponer que redesplegar Bicep revierte cambios de datos.

Si falla la aceptación, no habilitar exposición pública; aislar la carga y corregir. No borrar bóveda, discos o claves como rollback automático. En producción, todo corte/restauración requiere coordinación y aprobación de pérdida de datos potencial.
