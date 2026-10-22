# App Service + PostgreSQL Flexible Server

Acelerador para una aplicación web pública en Azure App Service con persistencia relacional PaaS en Azure Database for PostgreSQL Flexible Server, sin acceso público a la base de datos.

**Estado:** diseño inicial. **Versión objetivo:** 0.1.0, sin release publicado. **Responsable técnico:** pendiente de asignación por el CoE.

Esta etapa contiene estructura y documentación, no una plantilla desplegable ni una certificación de seguridad. No se crean recursos ni se generan costos por utilizar estos documentos.

## Decisiones confirmadas

- Motor PostgreSQL Flexible Server.
- Aplicación accesible desde Internet; esto no implica acceso anónimo a sus funciones o datos.
- Implementación por etapas: arquitectura y estructura primero; Bicep, aplicación y automatización después de aprobar el diseño.

## Diseño recomendado

Entrada pública HTTPS protegida por WAF, origen App Service privado y conexión a PostgreSQL mediante integración VNet y DNS privado. Front Door Premium con Private Link es la recomendación inicial, pendiente de aprobación de costo y disponibilidad regional. No se ha aprobado exposición directa del origen App Service.

La identidad administrada autentica la aplicación contra PostgreSQL mediante Entra ID. No se almacenan contraseñas de base de datos; cualquier secreto adicional requerido se almacena únicamente en Key Vault.

## Contenido

| Carpeta | Propósito |
| --- | --- |
| [infra](infra/README.md) | Contrato de la futura infraestructura Bicep y sus módulos. |
| [parameters](parameters/README.md) | Entradas previstas y reglas para ejemplos con placeholders. |
| [pipelines](pipelines/README.md) | Validación y despliegue futuros con OIDC. |
| [app](app/README.md) | Requisitos de la futura aplicación de ejemplo. |
| [docs](docs/README.md) | Arquitectura, seguridad, despliegue, operación y limpieza. |

Empieza por la [arquitectura](docs/architecture.md) y sus decisiones pendientes. Los cambios se registran en el [changelog](CHANGELOG.md).