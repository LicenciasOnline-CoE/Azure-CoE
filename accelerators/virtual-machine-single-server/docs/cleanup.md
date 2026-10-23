# Limpieza y retiro

Retirar una VM no significa que sea correcto borrar sus respaldos. Este procedimiento requiere autorización y revisión de dependencias; no proporciona un comando de borrado masivo.

1. Confirmar fin de servicio, obligaciones de conservación, aprobación del propietario y ventana. Exportar solo configuraciones/evidencia sanitizadas que deban conservarse.
2. Inventariar recursos propios y compartidos: VM, NIC, discos/snapshots, WAF/Bastion/firewall/NAT, IP, DNS, Private Endpoints, Key Vault, identidades/RBAC, DCR, alertas y bóveda. No eliminar servicios compartidos.
3. Confirmar backup final válido y recuperación comprobada cuando deban conservarse datos. Registrar retención y responsable de acceso posterior.
4. Retirar entrada de tráfico/DNS con plan de reversión; detener jobs e integraciones y revocar permisos que ya no se necesiten.
5. Elegir en Azure Backup entre **detener protección conservando datos** o continuar backups durante la transición, según política. Revisar consecuencias de facturación y retención en el modo seleccionado; no asumir expiración automática de todo lo retenido.
6. Eliminar solo VM y recursos dependientes propios aprobados; comprobar si discos/NIC se conservan por su configuración. Deallocate no elimina discos ni sus costos.
7. No eliminar el grupo de recursos de la bóveda como limpieza de la app. Eliminar datos de backup exige aprobación específica, compatibilidad con retención/inmutabilidad, MUA y soft delete. Respetar períodos obligatorios; **no intentar eludirlos**.
8. Mantener claves/versiones de Key Vault y permisos necesarios mientras existan backups cifrados que las requieran. No purgar claves como parte de borrar la VM.
9. Limpiar únicamente recursos de restore/staging identificados y sin dependencia activa; revisar costos residuales de backup, discos, logs, IP y servicios de red.
10. Cerrar accesos/alertas que ya no correspondan, conservar auditoría durante el plazo aprobado y registrar resultado.

La inmutabilidad bloqueada es irreversible; respaldos retenidos y soft-deleted pueden seguir ocupando almacenamiento y generando cargos. Planificar estos costos antes del despliegue, no deshabilitar controles para acelerar el retiro.
