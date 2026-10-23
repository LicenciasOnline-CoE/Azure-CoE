# Protección por aplicación y protocolo

## Controles comunes

- VM sin IP pública. NSG y firewall del SO permiten solo flujos necesarios, incluyendo restricciones de tráfico lateral; no confiar en `AllowVNetInBound`.
- Identidad administrada con roles mínimos para servicios Azure que la soporten. No autentica automáticamente usuarios ni aplicaciones legacy.
- Operadores en grupos Entra con roles **Virtual Machine User Login** o **Virtual Machine Administrator Login** según tarea, permisos de lectura/conexión adicionales y extensión de login compatible. Contributor/Owner no equivalen a login del guest.
- MFA, Conditional Access y PIM según soporte de cliente/flujo y licencias. Validar la combinación SO, extensión, Bastion SKU y cliente; no afirmar que todas las combinaciones aceptan Entra ID. No usar características preview para completar el baseline.
- Para Entra ID con Bastion, preferir SSH GA en Linux y un flujo GA de cliente nativo RDP compatible en Windows (Bastion Standard o superior y requisitos de dispositivo/cliente). A la fecha de consulta, RDP con Entra ID desde el portal está en public preview y queda fuera del baseline. No confundir ese estado con la disponibilidad de Bastion o RDP en general.
- Sin cuentas compartidas. Definir acceso de emergencia auditado; toda credencial o clave privada inevitable se conserva solo en Key Vault, con recuperación y rotación controladas. Nunca en parámetros, logs o scripts.
- Imagen soportada, hardening del SO, Secure Boot/vTPM y Trusted Launch cuando sean compatibles. Revisar drivers/extensiones antes de habilitarlos.
- Parches mediante Azure Update Manager o herramienta corporativa compatible, con evaluación, ventana y reinicio probados. Su costo/licenciamiento depende del entorno.
- Evaluar Microsoft Defender for Servers y habilitar protección de endpoint compatible con su licencia, alertas y responsable de respuesta. No basta con activar un plan en el portal.
- Discos cifrados; TLS >=1.2 donde aplique y algoritmos SSH vigentes. Guardar secretos/certificados en Key Vault, con distribución controlada y renovación.
- AMA/DCR para eventos de seguridad, accesos y servicios; Activity Log y diagnósticos de servicios Azure hacia destinos/retenciones aprobados. Sanitizar logs: no capturar contraseñas, tokens, cookies o contenido sensible.

## Matriz de escenarios

Los puertos son convenciones habituales; confirmar los reales. No crear reglas para todos los escenarios a la vez.

| Perfil | Camino y puertos | Controles específicos |
| --- | --- | --- |
| Web pública | Internet -> WAF HTTPS 443 -> VM privada HTTPS 443. HTTP 80 solo en frontend si se aprueba redirección. | Application Gateway WAF_v2; regla backend solo desde su subred, certificados válidos, validación de hostname/cadena, health probes y logs. No permitir bypass directo al origen. |
| Web interna | Red autorizada -> frontend privado -> HTTPS de app. | VPN/ExpressRoute, DNS privado, autenticación de aplicación y WAF según riesgo. Ser privado no elimina OWASP ni abuso de usuarios autorizados. |
| RDP administrativo | Bastion o red administrativa autorizada -> TCP 3389 privado. | NLA, Entra login soportado, mínimo privilegio, timeout/bloqueo, restricciones de redirección de disco/clipboard según política. Nunca `0.0.0.0/0:3389`. |
| SSH administrativo | Bastion o red administrativa -> TCP 22 privado. | Login Entra compatible o claves gestionadas; bloquear root remoto y contraseñas cuando el flujo lo permita, mantener host keys verificadas y auditoría. Nunca `0.0.0.0/0:22`. |
| SFTP | Red privada autorizada -> TCP 22 del servicio. | SSH cifrado, cuentas/grupos de transferencia separados, chroot o aislamiento equivalente, sin shell/forwarding si no se necesitan, permisos por carpeta, cuotas, auditoría y escaneo de archivos. |
| FTPS por dependencia | Red privada autorizada -> TCP 21 explícito + rango pasivo acotado; 990 solo si se exige modo implícito. | TLS >=1.2 obligatorio en control **y datos**, certificado válido, deshabilitar FTP sin cifrar y anonymous, configurar rango pasivo/NAT y permisos mínimos. No abrir rangos dinámicos completos. |
| DB en VM | Clientes autorizados -> puerto privado del motor; por ejemplo TCP 1433, 5432 o 3306. | Sin exposición a Internet; bind a interfaz requerida, TLS, roles mínimos, auditoría y backups nativos. Si solo la usa la app local, preferir loopback/IPC y no abrir el puerto en NSG. |
| Empresarial/legacy | Red corporativa -> puertos documentados por fabricante. | Inventariar RPC/SMB/licencias/AD/DNS, limitar origen/destino, cuenta de servicio sin privilegios innecesarios, segmentación y controles compensatorios para protocolos sin cifrado. |

## Web: lo que WAF no resuelve

Autenticación/autorización de aplicación siguen siendo necesarias: integrar Entra ID cuando sea compatible. Aplicar actualizaciones de runtime, validación de entradas, límites de carga, cookies seguras y protección de sesiones. Configurar inicialmente las reglas WAF en detección en un entorno de prueba, ajustar falsos positivos y pasar a **prevención antes de producción**, con exclusiones mínimas justificadas.

No aceptar certificados inválidos en backend como solución a health probes. Definir renovación y alertas de expiración. WAF inspecciona HTTP(S): **no protege RDP, SSH, SFTP, FTPS o el protocolo SQL**. DDoS Protection protege otros aspectos de la exposición pública y no sustituye controles de aplicación.

## RDP para usuarios no es administración

Las sesiones administrativas de Windows Server no son una solución multiusuario de aplicaciones. Si usuarios trabajan por RDP, revisar RDS (roles, CAL y licencias aplicables) o Azure Virtual Desktop con una arquitectura dedicada, capacidad por sesión, aislamiento de perfiles y diseño de gateway. No publicar RDP directo ni asumir que Bastion reemplaza una plataforma de escritorio remoto.

JIT de Defender puede reducir la ventana de exposición si el plan lo permite; no sustituye Bastion/VPN ni justifica RDP público. Bastion es acceso administrativo, no un gateway productivo general para transferencias o usuarios finales.

## FTP, SFTP y publicación de archivos

**FTP sin cifrar no forma parte del diseño admitido:** expone credenciales y datos. SFTP no es FTP con TLS; es un subsistema de SSH. FTPS usa FTP con TLS y requiere puertos de datos además del canal de control.

Preferir SFTP privado si la aplicación lo soporta. Confirmar si el servidor SFTP soporta el mecanismo de autenticación elegido: habilitar Entra SSH en la VM no implica que los clientes/usuarios SFTP hereden ese soporte.

Si un partner externo necesita enviar archivos, preferir conectividad privada o evaluar un servicio administrado compatible. No añadir IP pública a la VM por comodidad. Una excepción pública requiere otro diseño aprobado: endpoint dedicado, orígenes acotados cuando sea posible, autenticación fuerte soportada, rate limiting, monitoreo, aislamiento y riesgo/costo documentados. No se considera cubierta por el perfil base ni por el WAF web.

## Base de datos y legacy

Preferir un servicio administrado cuando las restricciones lo permitan. En una VM el equipo opera motor, parches, integridad, licencias, log truncation y recuperación. Separar data/log según fabricante y aplicar límites de memoria para no dejar sin RAM al SO y agentes.

Si una app requiere SMB, RPC dinámico o autenticación heredada, documentar exactamente qué flujos necesita; no abrir toda la VNet ni rangos amplios sin análisis. Si no puede cifrar, aislar el tramo en red privada y registrar excepción con responsable y vencimiento. El firewall no corrige una aplicación sin soporte.

## Evidencia mínima

Probar login autorizado y rechazo de no autorizados, puertos desde redes permitidas/denegadas, bloqueo del bypass WAF, TLS/SSH, actualización y detección de endpoint, recepción de logs y alertas. Ver la [lista de validación](validation.md) y las [referencias oficiales](references.md).
