# Limpieza

Plan de retiro futuro. No hay recursos creados por esta etapa ni scripts de eliminación disponibles.

1. Identificar recursos exclusivos del acelerador y recursos compartidos. No eliminar un resource group, workspace, VNet o zona DNS compartidos por comodidad.
2. Obtener autorización de retiro y resolver retenciones, obligaciones de auditoría y respaldo. Un servidor eliminado no debe considerarse recuperable solo porque tenía backups automáticos.
3. Detener nuevas solicitudes, trabajos y escrituras; conservar o exportar los datos autorizados mediante un procedimiento probado fuera del repositorio.
4. Retirar rutas y dominios públicos; coordinar DNS y certificados para evitar registros huérfanos y riesgo de apropiación de subdominios.
5. Eliminar la aplicación, conexiones privadas y servicios exclusivos siguiendo dependencias; desconectar la integración VNet antes de retirar subredes. Respetar locks aprobados y políticas de recuperación.
6. Retirar roles SQL, asignaciones RBAC, federaciones OIDC y registros de aplicación que sean exclusivos; no borrar identidades compartidas.
7. Conservar diagnósticos conforme a retención, retirar dependencias DNS/red exclusivas y verificar recursos residuales y facturación.

No incluir respaldos, datos de producción, identificadores reales ni evidencia sensible en este repositorio. Los detalles ejecutables y las verificaciones de borrado seguro se completarán al implementar la infraestructura.