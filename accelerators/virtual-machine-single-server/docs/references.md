# Referencias oficiales

Fuentes de Microsoft consultadas para esta guía inicial el **23 de octubre de 2026**. Confirmar vigencia, estado GA, región, SKU, licencias y restricciones antes de implementar. La guía adapta controles a una VM; no reproduce íntegramente arquitecturas de múltiples instancias.

## Arquitectura y dimensionamiento

- [Azure Virtual Machines baseline architecture](https://learn.microsoft.com/azure/architecture/virtual-machines/baseline): controles de red, identidad, operación y seguridad; su disponibilidad multiinstancia no aplica automáticamente aquí.
- [Tamaños de VM](https://learn.microsoft.com/azure/virtual-machines/sizes/overview): familias y propósito.
- [Dsv5](https://learn.microsoft.com/azure/virtual-machines/sizes/general-purpose/dsv5-series): vCPU/RAM y límites de disco usados en los ejemplos.
- [Esv5](https://learn.microsoft.com/azure/virtual-machines/sizes/memory-optimized/esv5-series) y [Fsv2](https://learn.microsoft.com/azure/virtual-machines/sizes/compute-optimized/fsv2-series): verificar capacidades de los ejemplos de memoria/cómputo.
- [Tipos de Managed Disks](https://learn.microsoft.com/azure/virtual-machines/disks-types) y [rendimiento de discos y VM](https://learn.microsoft.com/azure/virtual-machines/disks-performance): capacidad frente a IOPS/throughput.
- [Cuotas de vCPU](https://learn.microsoft.com/azure/virtual-machines/quotas) y [calculadora de precios](https://azure.microsoft.com/pricing/calculator/): validación regional y estimación, sin tarifas fijas.

## Seguridad y operación

- [NSG](https://learn.microsoft.com/azure/virtual-network/network-security-groups-overview): reglas predeterminadas, prioridades y filtrado.
- [Autenticación Entra en Bastion](https://learn.microsoft.com/azure/bastion/bastion-entra-id-authentication): comprobar protocolo, SKU, cliente y extensiones compatibles.
- [Trusted Launch](https://learn.microsoft.com/azure/virtual-machines/trusted-launch): compatibilidad y controles de arranque.
- [WAF en Application Gateway](https://learn.microsoft.com/azure/web-application-firewall/ag/ag-overview): inspección HTTP(S), no protección de otros protocolos.
- [Azure Monitor Agent](https://learn.microsoft.com/azure/azure-monitor/agents/azure-monitor-agent-overview): recolección en el guest mediante DCR.
- [Azure Update Manager](https://learn.microsoft.com/azure/update-manager/overview) y [Defender for Servers](https://learn.microsoft.com/azure/defender-for-cloud/defender-for-servers-overview): capacidades y requisitos operativos/licenciamiento.

## Backup y recuperación

- [Configurar Azure VM Backup en Recovery Services vault](https://learn.microsoft.com/azure/backup/backup-azure-arm-vms-prepare): región, redundancia, política, copia inicial y jobs.
- [Política Enhanced](https://learn.microsoft.com/azure/backup/backup-azure-vms-enhanced-policy): frecuencias, Instant Restore, restricciones y costos.
- [Matriz de soporte de Azure VM Backup](https://learn.microsoft.com/azure/backup/backup-support-matrix-iaas): compatibilidad que debe verificarse antes de proteger/restaurar.
- [Restaurar VM desde el portal](https://learn.microsoft.com/azure/backup/backup-azure-arm-restore-vms): VM nueva, discos, reemplazo y Cross Region Restore.
- [Consistencia de aplicación en Linux](https://learn.microsoft.com/azure/backup/backup-azure-linux-app-consistent): framework pre/post scripts, no garantía automática.
- [Bóveda inmutable](https://learn.microsoft.com/azure/backup/backup-azure-immutable-vault-concept): protección y bloqueo irreversible.
- [Multi-User Authorization](https://learn.microsoft.com/azure/backup/multi-user-authorization-concept): Resource Guard y separación de aprobación.
- [Private Endpoints v2 para Azure Backup](https://learn.microsoft.com/azure/backup/backup-azure-private-endpoints-concept): distinguir protección de VM, recuperación de archivos y workloads/agentes.

Los guardrails de utilización, ejemplos de retención y periodicidad de pruebas son puntos iniciales propuestos por esta guía, no umbrales o garantías oficiales de Microsoft.
