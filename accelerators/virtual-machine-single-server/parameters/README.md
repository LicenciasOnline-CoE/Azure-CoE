# Parámetros previstos

No hay archivos de parámetros ejecutables. La futura implementación deberá validar este contrato y publicar ejemplos con placeholders, no valores de un entorno real.

| Grupo | Entradas previstas |
| --- | --- |
| Identificación | `workloadName`, `environmentName`, `location`, `regionCode`, `instance`, `computerName`, tags obligatorios. |
| VM | `osType`, referencia/versionado de imagen, `vmSize`, `zone`, usuario de bootstrap no compartido y mecanismo seguro de acceso. |
| Discos | Tamaño/SKU del SO y lista de discos de datos, caching, cifrado y política de eliminación explícita. |
| Red | Red/subred existente o nueva, rangos, DNS, orígenes autorizados, rutas de administración/salida; VM sin IP pública. |
| Aplicación | Perfil elegido, puertos mínimos, publicación web opcional, dominio/certificados referenciados desde Key Vault. |
| Identidad | Identidades/grupos y roles por alcance; no contraseñas ni claves privadas. |
| Observabilidad | Destino, DCR, diagnósticos, retención y action group; no correos personales como ejemplos. |
| Backup | Bóveda nueva/existente, redundancia, Enhanced, horario/zona horaria/ventana, retenciones, Instant Restore y alertas. |
| Gobierno | Política de protección, propietario técnico por rol/equipo y excepciones aprobadas fuera del repositorio. |

Usar `<workload>`, `<environment>`, `<region>`, `<instance>`, `<resource-id>` y `<group-object-id>` como marcadores documentales. No son valores desplegables. Ejemplos técnicos de SKU o retención no deben interpretarse como datos de clientes.

No generar un parámetro general `allowedPorts` abierto sin validación: relacionar reglas con perfil y origen. Rechazar combinaciones incompatibles de imagen, SKU, arquitectura, disco, zona o backup; no degradar seguridad con defaults silenciosos.
