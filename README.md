# Azure CoE

Repositorio del Centro de Excelencia (CoE) para diseñar, validar y compartir capacidades técnicas reutilizables en Microsoft Azure. Su objetivo es que los partners de Latinoamérica puedan adoptar aceleradores de forma repetible, con documentación suficiente para desplegarlos y operarlos sin depender de implementaciones a medida.

## Principios

- **Genérico y neutral:** no se incluyen nombres de clientes, identificadores reales de tenant o suscripción, secretos, correos ni datos personales. Los ejemplos usan placeholders.
- **Seguro por defecto:** identidad administrada y Microsoft Entra ID; secretos en Azure Key Vault; acceso público denegado salvo justificación documentada; TLS 1.2 o superior; diagnósticos y auditoría habilitados.
- **Reutilizable y validable:** infraestructura parametrizable, documentación de despliegue y operación, y controles automatizados antes de publicar cambios.
- **Basado en GA:** se prefieren servicios y características de disponibilidad general; no se usan características preview si existe una alternativa GA.
- **Documentación en español:** el código, los identificadores y los comentarios técnicos se escriben en inglés.

## Estructura objetivo

La estructura del repositorio crecerá alrededor de estos activos:

| Ruta | Propósito |
| --- | --- |
| `.github/` | Instrucciones para contribuciones asistidas, prompts reutilizables y workflows de GitHub Actions. |
| `standards/` | Estándares de naming, tags, seguridad, organización de suscripciones y Azure Policy. |
| `shared/` | Módulos Bicep reutilizables entre aceleradores. |
| `accelerators/` | Catálogo de soluciones desplegables y plantilla base para nuevos aceleradores. |
| `governance/` | Charter, intake, alcance, quality gates y roadmap del CoE. |
| `enablement/` | Material para sesiones, decks y preguntas frecuentes. |
| `evidence/` | Índice y plantillas de registro sin información personal ni confidencial. |
| `docs/adr/` | Registros de decisiones de arquitectura (ADR). |

La estructura indicada es el objetivo del repositorio; la disponibilidad de cada activo dependerá de su etapa de implementación.

## Aceleradores

El primer acelerador cubre **Azure App Service + PostgreSQL Flexible Server**. Consulta el [catálogo de aceleradores](accelerators/README.md) y su [documentación inicial](accelerators/app-service-postgresql/README.md). Actualmente está en etapa de diseño; todavía no incluye infraestructura ni pipelines ejecutables. Cada acelerador deberá incluir infraestructura, parámetros de ejemplo, pipeline invocable, aplicación de ejemplo y documentación para desplegar, validar, operar y limpiar los recursos.

Para usar un acelerador, sigue su documentación y sustituye únicamente los placeholders en los parámetros de ejemplo. No agregues valores reales de clientes ni credenciales al repositorio.

## Estándares técnicos

- Naming base CAF: `<tipo>-<workload>-<ambiente>-<region>-<instancia>`, respetando las restricciones particulares de cada recurso.
- Tags obligatorios: `cliente`, `workload`, `ambiente`, `owner`, `centro-costo`, `criticidad`, `gestionado-por`, `iac`.
- Infraestructura como código con Bicep y Azure Verified Modules cuando sean adecuados.
- Workflows de GitHub Actions con OIDC y permisos de mínimo privilegio; sin secretos de cliente de servicio.
- Validaciones de lint, build y, cuando el entorno esté configurado, what-if antes de desplegar.

Consulta las instrucciones de contribución y los estándares del repositorio a medida que se publiquen.

## Versionado

Cada acelerador mantiene su propio versionado SemVer. Los cambios incompatibles, las nuevas capacidades y las correcciones se reflejarán en el changelog correspondiente al acelerador.

## Contribuir

Antes de proponer un cambio, verifica que sea genérico, parametrizable y coherente con los requisitos de seguridad del CoE. Los pull requests deben describir el propósito, el alcance, los riesgos y las validaciones ejecutadas. La convención de commits es Conventional Commits: `tipo(scope): descripción`.

No incluyas material confidencial de Microsoft ni detalles de programas de incentivos de partners.