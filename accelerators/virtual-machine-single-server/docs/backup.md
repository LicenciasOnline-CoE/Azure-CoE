# Habilitar respaldo y comprobar la recuperación

Este procedimiento manual orienta la configuración de **Azure VM Backup en un Recovery Services vault**. No usa un Backup vault de Azure Data Protection para este perfil. La futura automatización deberá reproducirlo con Bicep y validarse en un entorno autorizado. Los nombres de paneles del portal pueden cambiar.

## 1. Definir objetivos y comprobar soporte

- **RPO:** pérdida máxima aceptable de datos. **RTO:** tiempo máximo hasta recuperar el servicio, incluyendo datos, red, identidad y validación funcional.
- Definir clasificación, retención, residencia de datos, región secundaria si aplica, responsable y presupuesto. Ejemplo para discutir, no obligación universal: copia diaria, 30 días diarios, 12 semanas y 12 meses; no inferir conservación legal de este ejemplo.
- Para cambios durante todo el día y un objetivo cercano a 4 horas, evaluar Enhanced cada 4 horas con ventana de 24 horas. Enhanced permite intervalos de 4/6/8/12/24 horas. La frecuencia es un objetivo de programación, **no garantiza** el RPO: fallas, duración del job y consistencia pueden ampliarlo. Alertar por antigüedad del último punto válido.
- Si la DB exige RPO de minutos o recuperación a un instante, agregar backups nativos/logs del motor o protección específica soportada, con almacenamiento duradero, cifrado y permisos mínimos. Una copia de VM diaria no lo cumple.
- Consultar la [matriz de soporte de Azure VM Backup](https://learn.microsoft.com/azure/backup/backup-support-matrix-iaas): SO, imagen, discos, cifrado, Trusted Launch, límites, disco compartido, exclusiones y modos de restore. No confiar en soporte de backup sin confirmar también restore.
- Verificar VM Agent/extensión de backup, espacio libre y salud del guest. No guardar el único backup dentro de la propia VM ni en disco temporal.

## 2. Preparar permisos y bóveda

1. Usar una identidad/grupo autorizado. **Backup Contributor** permite administrar protección dentro de su alcance; crear la bóveda y restaurar a redes/discos/VM requiere permisos adicionales específicos sobre esos recursos. **Backup Reader** sirve para observación y **Backup Operator** para las tareas permitidas por su definición vigente. No otorgar Owner general.
2. Crear un **Recovery Services vault** en la misma región que la VM; este baseline propone misma suscripción para simplificar operación. Puede localizarse desde la búsqueda del portal o el panel de resiliencia/backup.
3. Preferir un grupo de recursos de protección separado del de la carga, con administración y ciclo de vida propios. Aplicar naming y tags donde estén soportados.
4. **Antes de registrar la primera VM**, abrir propiedades/configuración de backup y seleccionar redundancia:
   - **LRS:** menor costo, copias locales; no cubre pérdida de zona/región por sí sola.
   - **ZRS:** resiliencia zonal en la región donde esté soportada; no recuperación regional.
   - **GRS:** réplica geográfica según región emparejada y restricciones de residencia. Si se necesita restore iniciado por el usuario en la región secundaria, evaluar y habilitar **Cross Region Restore** donde esté soportado. GRS por sí solo no garantiza acceso inmediato, capacidad, RTO ni infraestructura lista en otra región.
5. No asumir que se podrá cambiar redundancia después de proteger elementos. Aprobar esta decisión antes del onboarding; revisar restricciones de migración vigentes.
6. Mantener **soft delete** habilitado, con retención aprobada; no depender de funciones preview de eliminación/recuperación de la bóveda. Activar inmutabilidad y ensayar políticas primero; bloquearla solo con aprobación: el estado bloqueado es irreversible y no permite reducir protección/retención arbitrariamente.
7. Configurar **Multi-User Authorization mediante Resource Guard** cuando corresponda, con aprobador separado de los administradores de backup y permisos mínimos. No concentrar capacidad de desproteger y aprobar en la misma identidad.
8. Configurar diagnósticos, alertas de fallas/eventos de seguridad y destinatarios mediante un action group aprobado. Definir retención de logs.

### Red y cifrado

Mantener el acceso público deshabilitado cuando sea compatible con el escenario y verificar el flujo real de Azure VM Backup. Los backups de discos administrados son una operación de plataforma; no necesitan por sí solos habilitar salida general a Internet desde el guest ni un Private Endpoint de bóveda para el tráfico de snapshots.

Si el tipo de protección/restore elegido requiere Private Endpoints, prepararlos con DNS y endpoints dependientes **antes** del registro cuando lo exija el servicio. SQL/SAP HANA en VM, agentes y recuperación de archivos tienen requisitos distintos al backup de discos de VM; no copiar sus reglas sin análisis. Para una implementación nueva, revisar la experiencia v2 de Private Endpoints y sus restricciones. Comprobar acceso a Azure Platform/VM Agent y dependencias requeridas sin romper la política de red.

Para discos/backup con claves administradas por el cliente, preservar claves, versiones y permisos de Key Vault durante toda la retención y recuperación. Cifrar no sirve si al restaurar ya no existe la clave; no purgar claves ni deshabilitar purge protection.

## 3. Crear política y habilitar protección

1. En la bóveda, abrir **Backup policies / Directivas de copia de seguridad**, agregar una de tipo **Azure Virtual Machine**, subtipo **Enhanced**.
2. Definir frecuencia, hora, **zona horaria** y ventana de ejecución. Revisar cambios de horario estacional y evitar coincidencia innecesaria con batch/parches.
3. Definir retención de puntos diarios/semanales/mensuales/anuales según necesidad; revisar los valores automáticos del portal, no aceptarlos sin análisis.
4. Establecer retención de snapshots de **Instant Restore** según RTO y costo. Enhanced admite hasta 30 días, pero el límite depende de frecuencia/cantidad de snapshots; debe ser compatible con retención en bóveda. No confundir snapshot operativo con conservación de largo plazo.
5. En **Backup / Configurar protección**, elegir origen Azure, tipo **Azure Virtual machines**, seleccionar bóveda, política y VM, y habilitar backup. Alternativamente, usar el panel **Backup** de la VM.
6. Revisar discos incluidos: SO y todos los datos necesarios para recuperar la app. Toda exclusión necesita motivo, propietario y recuperación alternativa probada. No incluir almacenamiento temporal como si fuera recuperable.
7. Esperar que termine la configuración. Confirmar que la VM aparece en **Backup items** sin alertas de configuración.
8. Ejecutar **Backup now / Realizar copia de seguridad ahora**, indicar retención del punto manual según lo permitido y seguirlo en **Backup jobs** hasta estado **Completed**.
9. Revisar punto de recuperación, timestamp y nivel de consistencia; confirmar transferencia a bóveda además de snapshot cuando corresponda. Habilitar protección sin una copia exitosa no significa estar protegido.
10. Comprobar después la primera ejecución **programada** y la entrega de alertas. No dar por finalizada la habilitación solo por el backup manual.

Elegir Enhanced para este diseño por sus capacidades; revisar soporte actual y costos. No asumir que puede volver a Standard después de proteger con Enhanced.

## 4. Verificar consistencia según aplicación

| Carga | Verificación requerida |
| --- | --- |
| Windows con aplicaciones compatibles | VSS writers sanos y punto application-consistent cuando se requiera. Revisar el resultado real; no basta con que el job termine. |
| Linux | No asumir consistencia de aplicación. Revisar modo soportado y punto generado; si se necesita application-consistent, implementar y probar pre/post scripts compatibles con la aplicación que congelen/reanuden I/O correctamente. |
| SQL u otro motor | Comprobar integridad y arranque del motor; usar protección de workload o backups nativos con logs según soporte/RPO. Coordinar cadenas de logs y política del motor, no duplicar herramientas sin análisis. |
| Web, archivos y legacy | Recuperar configuraciones, permisos/ACL, certificados, dependencias y datos coherentes; documentar qué se reconstruye desde código y qué debe respaldarse. |

Un punto crash-consistent puede recuperar un SO, pero no prueba que una transacción de negocio esté íntegra. Si no se obtiene el nivel exigido, tratarlo como incumplimiento y alertar, aunque el job figure exitoso.

## 5. Prueba de restauración aislada

Realizar antes de producción, después de cambios importantes y con periodicidad acordada (por ejemplo trimestral). **Backup sin restore probado no cumple la aceptación.**

1. Aprobar prueba, costos, permisos, RPO/RTO objetivo y punto a usar. Incluir al menos un restore desde **vault tier**, no solo Instant Restore, para validar dependencia de largo plazo.
2. Preparar VNet/subred de recuperación aisladas, sin IP pública ni rutas/credenciales que permitan escribir en producción. Bloquear integraciones salientes, correos, jobs y transferencias automáticas. Evitar colisiones de hostname, dominio y DNS.
3. En **Backup items -> Azure Virtual Machine**, seleccionar VM, **Restore VM**, punto y modalidad soportada:
   - **Create new VM** para una VM de prueba donde sea compatible.
   - **Restore disks** para reconstrucción controlada o configuraciones que requieran personalización.
   - **Replace existing disks** solo para recuperación real aprobada y compatible; es disruptivo, no es la opción base de ensayo.
4. Seleccionar grupo, red/subred, zona y almacenamiento de staging si se requiere. Confirmar cuota, SKU/capacidad, cifrado y permisos. No asumir que siempre se restaura a otro SO, región o zona.
5. Medir desde el inicio del incidente simulado hasta aplicación utilizable; incluir provisión, arranque y validación, no solo duración del job.
6. Verificar arranque, discos/montajes, archivos/ACL, integridad del motor, autenticación y transacciones sintéticas. Reaplicar/revisar NSG, extensiones, identidad administrada/RBAC, certificados, DNS, DCR y alertas: no asumir que todo recurso externo o identidad se recupera con los discos.
7. Medir RPO efectivo comparando último dato/transacción recuperada con el instante simulado; contrastar timestamp y consistencia del punto. Registrar tiempos y resultado sin datos reales.
8. Si no cumple, corregir frecuencia, motor, método o arquitectura y repetir. No declarar éxito con solo un estado Completed.
9. Eliminar únicamente recursos temporales identificados; conservar backups según retención. Documentar hallazgos y próxima prueba.

Para una recuperación real, acordar corte de escrituras, validación y cambio de DNS/tráfico con rollback. Cross Region Restore requiere además diseño de red, claves, dependencias y capacidad en destino; no está resuelto por esta guía de una VM.

## 6. Operación y costos

Alertar por job fallido, falta de punto válido dentro del RPO aprobado, salud de extensiones/consistencia y operaciones destructivas. Asignar responsable y escalamiento; ejecutar la prueba del action group.

Presupuestar instancia protegida, almacenamiento de bóveda, crecimiento/retención, snapshots de Instant Restore, redundancia, restore, discos de staging/prueba y protección específica de DB. Inmutabilidad y soft delete pueden mantener costos después del retiro de la VM. No reducir retención ni deshabilitar protección para resolver un presupuesto sin aprobación.

Consultar [fuentes oficiales de backup](references.md) y el procedimiento de [limpieza](cleanup.md).
