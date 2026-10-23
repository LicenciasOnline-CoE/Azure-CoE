# Aplicación de ejemplo prevista

Esta etapa no incluye código de aplicación ni scripts de configuración del guest.

La implementación posterior deberá entregar una aplicación web mínima reproducible para Windows Server y Linux, con health check, datos sintéticos, logs sanitizados, HTTPS y publicación privada detrás del frontend aprobado. Debe fijar versiones soportadas y no instalar simultáneamente RDP de usuarios, FTP o un motor de DB por comodidad.

Si se usa un secreto o certificado, obtenerlo desde Key Vault mediante identidad/flujo compatible sin copiarlo a parámetros o logs. Definir qué archivos/configuraciones son reconstruibles y qué datos deben recuperarse del backup.

La prueba debe demostrar que, tras restore aislado, arranca el servicio y se recupera un dato sintético conocido. Los escenarios SFTP/FTPS, DB y legacy se cubren como guías; aplicaciones/servicios de ejemplo adicionales requieren una etapa aprobada y pruebas propias.
