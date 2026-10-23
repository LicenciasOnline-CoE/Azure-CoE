# Infraestructura prevista

Esta carpeta no contiene Bicep desplegable en esta etapa.

## Contrato de implementación

- Componer VM única, NIC privada, discos persistentes, NSG, identidad, login compatible, Key Vault, observabilidad y Azure Backup.
- Soportar perfiles Windows Server/Linux y aplicación privada por defecto. Entrada WAF pública solo por opción explícita y justificada; no agregar IP pública a la VM.
- Reutilizar red, DNS, conectividad y destinos existentes cuando estén autorizados; documentar ownership para no eliminarlos durante limpieza.
- Usar Azure Verified Modules adecuados, versiones fijadas y API GA soportadas. Revisar `shared/` cuando exista; crear composición local solo donde aporte valor.
- Parametrizar capacidad, región/zona, seguridad, diagnóstico, retención, redundancia y discos. Aplicar naming/tags y validar hostname Windows por separado.
- Separar aprovisionamiento ARM, asignación de roles, configuración del guest y publicación de aplicación. Managed identity no sustituye un mecanismo seguro de bootstrap.
- No emitir credenciales, tokens ni claves como outputs. Resolver secretos inevitables mediante Key Vault y un flujo seguro documentado.
- Desplegar DCR/asociaciones, diagnósticos y alertas; no declarar monitoreo completo por crear solamente Log Analytics.
- Crear política de backup e instancia protegida según soporte. Aprovisionar una bóveda no habilita automáticamente el respaldo de la VM.
- Validar decisiones irreversibles de backup antes de bloquear inmutabilidad; no exponer un toggle que reduzca protección sin revisión.

Antes de implementar deben aprobarse las [decisiones de arquitectura](../docs/architecture.md), los módulos/versiones y las pruebas de [aceptación](../docs/validation.md). No hay todavía garantías de idempotencia ni despliegue probado.
