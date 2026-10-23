# Automatización prevista

Todavía no existen pipelines ejecutables para este acelerador. La implementación deberá usar workflows reutilizables de GitHub Actions y mantener la autenticación separada de la conectividad al guest.

## Validación de PR

1. Validación de parámetros, naming/tags y opciones por perfil.
2. Bicep lint/build y pruebas estáticas de seguridad: sin IP pública en VM, sin administración abierta, identidad y backup/diagnóstico configurados.
3. Validación ARM y what-if cuando exista configuración de entorno y federación autorizada; informar explícitamente si no se ejecutan. No presentar un skip como validación exitosa.
4. Permisos de workflow mínimos; no exponer identidades de despliegue a código no confiable de forks.

## Despliegue y pruebas

- Workflow invocable con entorno y parámetros externos aprobados, `id-token: write` solo para jobs que requieren OIDC y permisos Azure por alcance.
- Sin client secrets; configurar federación y environments protegidos con aprobaciones antes de producción.
- Runner con conectividad privada para bootstrap/publicación/pruebas en el guest. Un runner público puede ejecutar ARM sin tener acceso a la VM; OIDC no cambia esto.
- Aprovisionamiento, configuración de guest y ejemplo en fases con fallas explícitas, sin imprimir secretos.
- Verificar login privado, aplicación, telemetría, copia inicial/programada y restore aislado. Gates de promoción según la [lista de aceptación](../docs/validation.md).
- Limpieza de recursos temporales identificados, sin borrar backups, claves o servicios compartidos; no ejecutar destrucción automáticamente en un PR.

No invocar un workflow inexistente ni documentar comandos de deploy como si ya estuvieran implementados.
