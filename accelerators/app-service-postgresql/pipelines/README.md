# Pipelines

Reservado para la documentación y configuración de invocación del workflow reutilizable en `.github/workflows/`, que todavía no existe. GitHub Actions solo descubre workflows ejecutables en esa ubicación, no dentro de esta carpeta.

## Controles previstos

- Lint y build de Bicep sin credenciales; validación de parámetros y controles de seguridad.
- Validación ARM y what-if únicamente en entornos autorizados con configuración disponible.
- OIDC, permisos mínimos, acciones fijadas a commit y aprobaciones de GitHub Environments para producción.
- No proporcionar credenciales o identidad federada a código no confiable de forks. No ejecutar código del PR mediante `pull_request_target` con privilegios.
- Separar infraestructura, publicación de aplicación y migraciones de esquema, con identidades y permisos distintos.
- Usar runner con conectividad privada y DNS para bootstrap PostgreSQL y publicación por SCM privado. OIDC no resuelve conectividad de red.
- Tratar salidas de what-if y logs como potencialmente sensibles; no publicar parámetros reales o tokens en PR ni artefactos.

La estrategia de runners y el alcance RBAC se definirán antes de implementar. No hay pipeline invocable en esta etapa.