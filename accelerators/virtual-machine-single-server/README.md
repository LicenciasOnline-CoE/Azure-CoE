# Azure VM single-server

Acelerador de referencia para una carga de trabajo en **una máquina virtual de Azure**, con Windows Server o Linux. Orienta la elección de capacidad, la protección según la aplicación y la recuperación de datos mediante Azure Backup.

**Estado:** guía y diseño inicial. **Versión objetivo:** 0.1.0, sin release publicado. **Responsable técnico:** pendiente de asignación por el CoE.

Esta etapa no contiene Bicep, parámetros ejecutables, aplicación ni pipelines. No es una solución certificada ni lista para producción. Leer los documentos no crea recursos; ejecutar sus procedimientos en Azure sí puede generar costos.

## Ruta de uso

1. Confirmar si una sola VM es aceptable con la [arquitectura y sus límites](docs/architecture.md).
2. Elegir un punto de partida y comprobarlo con la [guía de dimensionamiento](docs/sizing.md).
3. Aplicar la [matriz de protección por aplicación y protocolo](docs/security.md).
4. Seguir los [prerrequisitos y la secuencia de despliegue](docs/deployment.md).
5. [Habilitar el respaldo y probar una restauración](docs/backup.md).
6. Acordar [operación y alertas](docs/operations.md) y completar la [lista de aceptación](docs/validation.md).

## Escenarios

- Aplicación web interna o publicada mediante WAF.
- Administración por RDP en Windows Server y SSH en Linux.
- Transferencia de archivos mediante SFTP o FTPS cuando exista dependencia.
- Base de datos instalada en la VM.
- Aplicación empresarial/legacy, incluidos casos donde los usuarios requieren sesiones remotas.

No se recomienda instalar todos estos roles en una misma VM. Son perfiles alternativos; una combinación requiere analizar consumo agregado, conflictos de puertos y mayor impacto de un compromiso.

## Principios

- VM sin IP pública; entrada y salida explícitas y de mínimo privilegio.
- Microsoft Entra ID e identidad administrada donde exista soporte; secretos inevitables solo en Key Vault.
- SO soportado, Trusted Launch cuando sea compatible, parches, protección de endpoint y auditoría.
- Disco de SO y datos separados; sin información persistente en almacenamiento temporal.
- Azure Backup con política explícita, alertas y recuperación probada.
- Una VM es un punto único de falla. WAF, Bastion, discos redundantes y backup **no la convierten en alta disponibilidad**.

## Contenido y evolución

| Ruta | Contenido |
| --- | --- |
| [docs](docs/README.md) | Guías operativas, decisiones y fuentes oficiales. |
| [infra](infra/README.md) | Contrato de la futura infraestructura Bicep. |
| [parameters](parameters/README.md) | Entradas previstas y reglas de ejemplos. |
| [pipelines](pipelines/README.md) | Contrato de validación y despliegue con OIDC. |
| [app](app/README.md) | Alcance de la futura aplicación de ejemplo. |
| [CHANGELOG.md](CHANGELOG.md) | Cambios del acelerador. |

La etapa posterior deberá implementar y probar estos contratos, asignar propietario técnico y publicar una versión SemVer. Las rutas objetivo `standards/` y `shared/` todavía no existen en el repositorio; cuando estén disponibles deberán reutilizarse, sin duplicar estándares o módulos.
