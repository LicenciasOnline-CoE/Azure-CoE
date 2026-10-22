# Instrucciones del Azure CoE

## Propósito
Este repositorio concentra prácticas y aceleradores técnicos de Azure reutilizables por partners de Latinoamérica. Los activos deben permitir despliegues repetibles y documentados, sin depender de trabajo a medida.

## Reglas generales
- Mantén el contenido neutral, genérico y técnico. No incluyas nombres de clientes, identificadores reales de tenant o suscripción, secretos, correos ni datos personales.
- Usa placeholders en parámetros de ejemplo. No guardes valores reales en el repositorio.
- No incluyas material confidencial de Microsoft ni información sobre programas de incentivos.
- Escribe la documentación en español; usa inglés para código, identificadores y comentarios técnicos.
- Prefiere servicios y características GA. No uses características preview si existe una alternativa GA.
- Reutiliza las convenciones de `standards/` y los módulos de `shared/`.

## Seguridad por defecto
- Usa Managed Identity y Microsoft Entra ID; evita credenciales almacenadas.
- Guarda los secretos requeridos únicamente en Azure Key Vault.
- Deniega acceso público por defecto. Si un escenario requiere exposición pública, documenta el motivo y los controles compensatorios.
- Exige TLS 1.2 o superior cuando el servicio lo permita.
- Habilita diagnósticos y auditoría, con destinos y retención parametrizables.
- Aplica mínimo privilegio, Azure Policy y validación de seguridad en los despliegues.

## Naming y tags
- Convención base CAF: `<tipo>-<workload>-<ambiente>-<region>-<instancia>`.
- Respeta las restricciones de longitud y caracteres propias de cada recurso. Documenta cualquier excepción.
- Los recursos deben recibir estos tags obligatorios: `cliente`, `workload`, `ambiente`, `owner`, `centro-costo`, `criticidad`, `gestionado-por`, `iac`.
- Los valores de ejemplo deben ser placeholders; no inventes datos reales de clientes.

## Stack y automatización
- Usa Bicep para infraestructura como código y Azure Verified Modules cuando exista un módulo adecuado.
- Mantén las Azure Policy en `standards/policies/` y documenta su alcance y efecto.
- Usa GitHub Actions reutilizables para validación y despliegue.
- Autentica GitHub Actions en Azure con OIDC y permisos de mínimo privilegio; no uses secretos de cliente de servicio.
- Los cambios de infraestructura deben incluir lint, build y validación. Los PR deben ejecutar what-if cuando exista configuración de entorno para hacerlo.

## Commits y pull requests
- Usa Conventional Commits: `tipo(scope): descripción`, por ejemplo `feat(app-service): add deployment template`.
- Mantén los commits enfocados y la descripción breve.
- Los PR deben explicar propósito, alcance, riesgos, validaciones ejecutadas y cambios de seguridad o configuración.
- No incluyas en PR evidencia con datos personales o material confidencial.

## Definición de terminado de un acelerador
- Es parametrizable y no contiene valores específicos de un cliente.
- Incluye infraestructura, parámetros de ejemplo, pipeline invocable, aplicación de ejemplo y documentación de despliegue.
- Aplica los estándares de naming, tags, identidad, red, TLS, diagnóstico y auditoría pertinentes.
- Pasa lint y build; incluye validación automatizada y what-if cuando el entorno lo permita.
- Documenta prerequisitos, permisos, despliegue, validación, operación, limpieza y limitaciones.
- Tiene propietario técnico, entradas de catálogo y versión SemVer propia.