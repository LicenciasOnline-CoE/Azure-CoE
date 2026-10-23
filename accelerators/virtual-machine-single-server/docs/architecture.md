# Arquitectura

## Alcance y disponibilidad

Una VM aloja la carga; los servicios de red, identidad, observabilidad y respaldo son externos a ella. El perfil base es privado. La publicación web es una variante opcional con justificación y aprobación.

Es adecuado para cargas cuyo negocio acepta interrupciones por mantenimiento, reinicios, fallas y restauración. No es adecuado para continuidad sin interrupciones, balanceo entre instancias ni tolerancia a la pérdida de una zona o región con recuperación inmediata.

Una VM zonal sigue dependiendo de una zona. Un availability set con una sola instancia no aporta redundancia de aplicación. Backup recupera datos; Azure Site Recovery puede apoyar recuperación ante desastres, pero es otro diseño y no sustituye backup ni alta disponibilidad. Si el RTO probado es inaceptable, diseñar múltiples instancias, replicación del motor o una alternativa PaaS.

## Componentes propuestos

| Componente | Decisión base |
| --- | --- |
| VM | Windows Server o Linux soportado por fabricante, agente de Azure sano, imagen Gen2 y Trusted Launch si son compatibles. No usar Spot para cargas persistentes que no toleren expulsión. |
| Discos | Managed Disks; SO persistente y datos separados. Standard SSD o Premium SSD según medición. Cifrado de plataforma; CMK solo con requisito y operación de claves aprobados. |
| Red | VNet y subred de aplicación privadas; NSG con permisos explícitos y firewall del SO. Sin IP pública en la NIC. |
| Administración | Azure Bastion o conectividad corporativa VPN/ExpressRoute ya autorizada. Subred y reglas dedicadas para Bastion cuando se use. |
| Entrada web opcional | Application Gateway WAF_v2, subred dedicada y backend HTTPS privado. IP pública solo en el gateway si se aprueba publicación a Internet. |
| Salida | Ruta explícita por firewall central para inspección; NAT Gateway si solo se requiere SNAT y se acepta que no filtra destinos. No depender de salida implícita. |
| Identidad | Identidad administrada para acceso de la carga a Azure; grupos Entra y roles de login separados para operadores. |
| Secretos | Key Vault con RBAC, Private Endpoint/DNS privado, acceso público deshabilitado y purge protection. |
| Observabilidad | Azure Monitor Agent (AMA), Data Collection Rules (DCR), Log Analytics, métricas de plataforma, diagnósticos y alertas. |
| Respaldo | Recovery Services vault, política Enhanced e instancia protegida. Redundancia y retención aprobadas antes de proteger la VM. |

No todos los componentes requieren recursos nuevos: reutilizar conectividad, Bastion, firewall y destinos de monitoreo existentes cuando sus límites y permisos lo permitan. No reutilizar una bóveda sin revisar región, políticas y separación administrativa.

## Flujos

```text
Perfil privado:
Usuarios autorizados -> VPN/ExpressRoute -> puerto privado de la aplicación
Operadores -> Bastion o red corporativa -> RDP/SSH privado -> VM

Variante web pública:
Internet -> HTTPS 443 -> Application Gateway WAF -> HTTPS 443 -> VM privada

Servicios:
VM (managed identity) -> Private Endpoint / DNS -> Key Vault
VM -> salida controlada y endpoints requeridos -> identidad, parches y monitoreo
Azure Backup -> protección de VM/discos -> Recovery Services vault
```

La flecha de backup representa el servicio de protección, no una regla genérica de salida desde el guest. El backup de VM tiene requisitos distintos a los agentes de aplicaciones; revisar su matriz de soporte y red. No abrir Internet indiscriminadamente para hacerlo funcionar.

## Red y dependencias

- Reservar rangos sin solapamiento para aplicación, gateway, Bastion y Private Endpoints según las restricciones del servicio.
- En NSG, permitir orígenes y destinos necesarios y después denegar el resto. La regla predeterminada `AllowVNetInBound` permite tráfico lateral y no implementa aislamiento por sí sola.
- Preservar reglas de control y health probes exigidas por Application Gateway y Bastion; no aplicarles una denegación genérica que rompa el servicio.
- Resolver DNS privado desde la VM, redes corporativas y runners privados. OIDC no proporciona conectividad.
- Inventariar dependencias de Entra ID, Key Vault, AMA, Defender, Backup, licencias, repositorios de paquetes, DNS y NTP antes de restringir salida. NAT Gateway no es un firewall.
- Definir certificados válidos y renovación; TLS 1.2 o superior en protocolos TLS. SSH/SFTP usan criptografía SSH, no TLS.

## Naming, tags y políticas

Usar `<tipo>-<workload>-<ambiente>-<region>-<instancia>`. Parametrizar un código corto de región y validar longitudes antes del despliegue. El nombre de recurso Azure y el hostname del guest son campos distintos: en Windows, el nombre de equipo no debe superar 15 caracteres; definir un hostname corto explícito. En Linux validar las restricciones de la imagen y de Azure; no truncar silenciosamente.

Aplicar `cliente`, `workload`, `ambiente`, `owner`, `centro-costo`, `criticidad`, `gestionado-por`, `iac` donde se admitan tags. Documentar excepciones para asociaciones, extensiones y otros recursos hijos.

En la futura ruta `standards/policies/`, centralizar políticas de tags, regiones/SKUs autorizados, denegación de IP pública en NIC, configuración de seguridad y auditoría de backup/diagnósticos. Distinguir efectos Audit, Deny y DeployIfNotExists, alcance, exclusiones y permisos de remediación. No afirmar cumplimiento hasta evaluar asignaciones y excepciones del entorno.

## Decisiones pendientes para implementar

- Propietario, región/zona, SO/imagen, arquitectura x64/Arm64 y soporte del proveedor.
- Escenario principal; no habilitar todos los puertos por seleccionar varios perfiles.
- RPO, RTO, ventana de mantenimiento, retención, redundancia y presupuesto.
- Conectividad, DNS, salida controlada, certificados y exposición pública excepcional.
- Plan Defender, reglas DCR, destinos/retención y permisos de cada identidad.
- Versiones GA de API/AVM, estrategia de bootstrap, rollback y pruebas con recursos reales.

La [arquitectura baseline de Microsoft](https://learn.microsoft.com/azure/architecture/virtual-machines/baseline) sirve como fuente de controles, no como promesa de equivalencia: utiliza múltiples instancias y capas, a diferencia de este caso.
