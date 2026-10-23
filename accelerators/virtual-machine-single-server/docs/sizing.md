# Guía de dimensionamiento

## 1. Recoger requisitos antes de elegir SKU

Registrar sin datos sensibles: SO y versión soportada, arquitectura CPU y dependencias del fabricante, servicios concurrentes, usuarios activos y patrón de uso, transacciones/solicitudes por segundo, latencia objetivo p95/p99, volumen y crecimiento de datos, ventana de batch, RPO/RTO y licenciamiento. El número de usuarios registrados no equivale a concurrencia.

Si existe servidor origen, medir al menos un ciclo representativo de **7 a 14 días**, incluyendo cierres, picos y procesos de backup; ampliar el período si el pico es mensual. Medir CPU, memoria disponible/working set, paginación, IOPS, MB/s, latencia de disco, cola, tráfico de red y latencia de aplicación. Las métricas de plataforma no dan toda la memoria del guest: configurar AMA/DCR o el recolector compatible antes de concluir.

## 2. Elegir familia

| Familia | Cuándo considerarla | Precaución |
| --- | --- | --- |
| B, burstable | Desarrollo, laboratorio o carga intermitente con CPU media baja. | Los créditos de CPU se agotan con carga sostenida; no dimensionar usando solo el pico de burst. Supervisar créditos y baseline de la SKU. |
| D, propósito general | Web, servicios empresariales y carga mixta con CPU/RAM equilibradas. | Punto inicial, no garantía de capacidad. Comparar generaciones GA y variantes Intel/AMD según región y compatibilidad. |
| E, memoria optimizada | Base de datos, caché o proceso que necesita más RAM por vCPU. | Validar IOPS, throughput y licencias; más RAM no corrige consultas ineficientes. |
| F, cómputo optimizado | Batch o cálculo con alta CPU y consumo acotado de memoria. | Comprobar memoria por proceso y rendimiento por núcleo, además de vCPU. |
| L y especializadas | Requisito demostrado de almacenamiento local o cómputo especial. | No son el baseline. El NVMe local puede ser temporal; diseñar persistencia/recuperación. GPU/HPC requieren un estudio propio. |

Priorizar una generación **GA** disponible, con horizonte de soporte apropiado y costo/rendimiento medido. No elegir preview por ser más reciente. Arm64 requiere imágenes, binarios, agentes y dependencias compatibles; no sustituye x64 de forma transparente.

## 3. Puntos iniciales para una prueba

Los siguientes son **ejemplos de capacidad para ensayar**, no mínimos de fabricante, recomendaciones universales ni promesas de usuarios soportados. Dsv5 es una referencia GA conocida; comparar con generaciones GA más nuevas disponibles en destino.

| Escenario | Capacidad inicial de prueba | Ejemplo de SKU | Qué validar primero |
| --- | --- | --- | --- |
| Web/API pequeña o servicio Linux ligero, sin DB local intensiva | 2 vCPU / 8 GiB | `Standard_D2s_v5` | RPS, TLS, latencia p95, CPU de runtime y memoria. |
| Web Windows/IIS o aplicación empresarial mixta | 4 vCPU / 16 GiB | `Standard_D4s_v5` | Working set conjunto de SO/agentes/app, concurrencia y reinicios. |
| Aplicación legacy con procesos más pesados | 8 vCPU / 32 GiB | `Standard_D8s_v5` | Rendimiento por núcleo, bloqueos y licencias; una app monohilo no escala solo con más vCPU. |
| SFTP/FTPS de volumen moderado | 2 vCPU / 8 GiB | `Standard_D2s_v5` | Sesiones simultáneas, cifrado, tamaños de archivo, MB/s y disco destino. |
| DB pequeña con memoria dominante | 4 vCPU / 32 GiB | `Standard_E4s_v5` | Cache hit, memoria del motor, log/data IOPS, latencia de escritura y backup nativo. |
| Batch con CPU sostenida y poca RAM | 4 vCPU / 8 GiB | `Standard_F4s_v2` | Tiempo de finalización, CPU sostenida y RAM por tarea. |
| Laboratorio intermitente | Según baseline y créditos de la SKU B elegida | Familia B compatible con imagen y región | Consumo de créditos y throttling durante tareas prolongadas. |

**RDP/SSH no determina el tamaño:** administrar un servidor no es la carga principal. Para usuarios ejecutando aplicaciones por RDP, medir perfiles, RAM por sesión, CPU y almacenamiento, y revisar RDS/AVD y licencias. No extrapolar usuarios por vCPU desde esta tabla.

Si web y DB comparten VM, sumar working sets y picos coincidentes; no sumar ciegamente los tamaños de dos filas ni asumir aislamiento. Evaluar separar la DB o usar PaaS si hay contención o riesgo inaceptable.

## 4. Dimensionar discos y red

- Separar SO, datos persistentes y, cuando el motor lo requiera, logs transaccionales. La separación organiza operación y rendimiento, pero no crea alta disponibilidad.
- Estimar capacidad con datos actuales + crecimiento del horizonte aprobado + espacio de mantenimiento. Como guardrail inicial, alertar al bajar de 20% libre; ajustar por crecimiento absoluto y necesidades del motor.
- Standard SSD puede servir para carga ligera; Premium SSD es un punto de partida para producción sensible a latencia. Elegir por IOPS/MB/s sostenidos y latencia medida, no solo por GiB.
- El rendimiento efectivo está limitado por **el disco y la VM**, además de caché, tamaño de I/O, paralelismo y aplicación. No usar valores de burst para un compromiso sostenido.
- Ejemplo: `Standard_D2s_v5` tiene un límite base de discos remotos Premium SSD sin caché de **3.750 IOPS y 85 MB/s**. Un disco con mayor capacidad no elimina ese límite de la VM. Revisar siempre la [tabla vigente de Dsv5](https://learn.microsoft.com/azure/virtual-machines/sizes/general-purpose/dsv5-series).
- No persistir datos, logs de recuperación ni únicas copias en disco temporal o SO efímero. Algunas series, como Dsv5, no incluyen disco temporal local.
- Ajustar host caching según documentación del motor/disco; no activar ReadWrite en discos de log transaccional sin soporte explícito.
- Premium SSD v2/Ultra son alternativas especializadas: confirmar soporte regional/zonal, restricciones del disco de SO, VM y Azure Backup antes de elegirlas.
- Comprobar Mbps frente a MB/s, latencia hacia dependencias y agregado de NIC. Accelerated Networking solo si está soportado por VM, SO e imagen; el ancho de banda anunciado no garantiza throughput extremo a extremo.

## 5. Validar y ajustar

1. Probar con datos sintéticos y el perfil de concurrencia previsto, incluyendo parches, escaneo y respaldo coincidentes.
2. Ejecutar carga sostenida durante el pico completo y suficiente tiempo para observar agotamiento de créditos/burst.
3. Definir criterios antes de probar. Como guardrails iniciales, investigar CPU p95 >70%, memoria disponible <20%, paginación sostenida o consumo de IOPS/MB/s >80% del límite durante el pico. No son umbrales oficiales de Azure ni sustituyen el SLO de la aplicación.
4. Aceptar solo si latencia p95/p99, tasa de errores y tiempo de batch cumplen el objetivo y queda margen de crecimiento acordado. Medir percentiles en un intervalo representativo; un promedio diario puede ocultar saturación.
5. Corregir el cuello de botella: CPU -> D/F o mayor SKU; RAM -> E/más memoria; I/O -> disco **y** límite de VM; red -> ruta/NIC/destino; aplicación -> consultas, locks o código.
6. Repetir pruebas después de cada ajuste y registrar comparación sanitizada. Un resize puede requerir reinicio/deallocate, cambiar IP dinámica y enfrentar falta de capacidad; aprobar ventana y rollback.

## 6. Disponibilidad y costo total

Antes de reservar capacidad, confirmar SKU e imagen en región/zona, cuota regional y por familia, restricciones de suscripción, soporte y capacidad efectiva al desplegar. Cuota disponible no garantiza stock.

Presupuestar cómputo, discos, snapshots, respaldo/retención, licencias Windows/SQL/RDS, Bastion, WAF, firewall/NAT, direcciones públicas, Private Endpoints, conectividad, transferencia, Defender, Monitor/ingesta/retención y pruebas de restore. Deallocate detiene el cobro de cómputo, no de todos los recursos asociados. Reservations/Savings Plan o Azure Hybrid Benefit requieren elegibilidad y uso estable comprobados.

Usar [Azure Pricing Calculator](https://azure.microsoft.com/pricing/calculator/) y precios de la región; no publicar una tarifa fija ni un dimensionamiento garantizado. Revisar mensualmente capacidad y recomendaciones de Advisor.
