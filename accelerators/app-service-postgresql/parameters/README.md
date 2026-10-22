# Parámetros

Reservado para ejemplos `.bicepparam` cuando exista el contrato Bicep. No se crean archivos de parámetros ficticios que aparenten ser desplegables.

| Grupo | Entradas previstas |
| --- | --- |
| Identificación | Workload, entorno, región, instancia y tags. |
| Red | Rangos no superpuestos, subredes, vínculos DNS y recursos existentes, si aplica. |
| Aplicación | Runtime GA, SKU, instancias, autoscaling y endpoint de salud. |
| Base de datos | Versión soportada, SKU, almacenamiento, HA, backups y retención. |
| Identidad | Referencias parametrizadas al administrador Entra y a identidades autorizadas. |
| Entrada | Dominio, certificados, política WAF y configuración del origen. |
| Operación | Destinos de diagnóstico, retenciones y umbrales de alertas. |

Naming base: `<tipo>-<workload>-<ambiente>-<region>-<instancia>`. Tags: `cliente`, `workload`, `ambiente`, `owner`, `centro-costo`, `criticidad`, `gestionado-por`, `iac`.

Los ejemplos usarán placeholders, nunca identificadores reales, correos, datos de clientes o secretos. Los valores reales se suministrarán fuera del repositorio. `owner` debe representar una referencia organizativa, no datos personales.