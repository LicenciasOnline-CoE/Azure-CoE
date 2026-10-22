# Aplicación de ejemplo

Reservado para una aplicación mínima que demuestre conexión segura a PostgreSQL. El lenguaje, framework y runtime GA están pendientes de selección; no hay aplicación ejecutable todavía.

## Requisitos de diseño

- Autenticación de usuarios mediante Entra ID y autorización explícita por función; definir audiencia y modelo de acceso antes de implementar. Acceso público de red no significa acceso anónimo.
- Identidad administrada para adquirir tokens de PostgreSQL, sin contraseñas persistentes. Renovar tokens al abrir nuevas conexiones; no fijarlos como configuración de larga duración.
- Pool de conexiones dimensionado junto con el máximo de instancias y conexiones del servidor; timeouts y reintentos acotados solo para operaciones transitorias seguras.
- TLS con validación de cadena y nombre del servidor; consultas parametrizadas y migraciones bajo una identidad diferente de la de ejecución.
- Endpoint de salud sin datos sensibles, compatible con las sondas de Front Door y App Service; no abrir la aplicación completa para permitir sondas.
- Telemetría correlacionada sin tokens, cuerpos de solicitud, datos personales ni parámetros SQL sensibles.
- Pruebas de acceso autorizado y denegado, renovación de tokens, reconexión y errores de base de datos.

La muestra usará únicamente datos sintéticos no personales.