# Infraestructura

Esta carpeta alojará Bicep. Actualmente no contiene archivos desplegables.

## Contrato previsto

- Componer red, DNS, App Service Plan, aplicación e identidad administrada, PostgreSQL, observabilidad y entrada WAF según el diseño aprobado.
- Usar Azure Verified Modules adecuados con versiones fijadas; encapsular en [modules](modules/README.md) solo las composiciones locales necesarias.
- Parametrizar región, entorno, tamaños, capacidad, disponibilidad, red existente o nueva, destinos de diagnóstico y retenciones.
- Aplicar naming CAF y los tags obligatorios donde el recurso admita tags; documentar excepciones de longitud, caracteres y recursos hijos.
- No emitir tokens, credenciales ni cadenas con secretos como outputs.
- Separar el aprovisionamiento ARM del bootstrap de roles y esquemas en PostgreSQL. Este último requiere identidad autorizada y conectividad privada.

El alcance de despliegue, las API GA, las versiones de módulos y el proceso de bootstrap se aprobarán antes de implementar. Consulta la [arquitectura](../docs/architecture.md).