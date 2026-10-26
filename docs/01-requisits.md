# 01 · Requisitos

Tasca 1 «Necessitats i sistemes» · RA principales: ISO RA1 a-c y FDM RA4 a-c.

Los datos no confirmados se marcan **N/C** con una propuesta de comprobación. Cualquier ejemplo es un **supuesto**.

Las necesidades se definen **antes** de elegir productos; la comparación y la elección están en [03-sistemes](03-sistemes.md).

## Datos de partida

| Dato | Valor | Supuesto / N/C |
|---|---|---|
| Departamentos | Dirección, Administración, Comercial, Producción e Informática | Enunciado |
| Número de usuarios | N/C | Confirmar con el profesorado (gerencia); mientras tanto, supuesto de 1 puesto por departamento en el laboratorio |
| Volumen de datos | N/C | Confirmar con gerencia; para el laboratorio se dimensiona con 2 discos en espejo (supuesto) |
| Red LAN | 192.168.30.0/24 | Asignada al grupo |
| WAN del cortafuegos | 172.16.222.0/24 | Pendiente de asignación |
| Arquitectura de referencia | 3 nodos de virtualización, NAS/SAN, cortafuegos, un conmutador y 5 clientes | Enunciado; no se afirma que el aula disponga ya de los 3 nodos |

## Necesidades por función

| Función | Necesidad | Supuesto / N/C |
|---|---|---|
| Comunicaciones | Conectar la LAN con la WAN, filtrar tráfico con IDS/IPS e inspección de capa 7 y dar VPN a los portátiles de Comercial | Varias WAN o BGP: supuesto. Ancho de banda N/C (medir en la Tasca 7) |
| Virtualización | Ejecutar los servidores en VM, hacer instantáneas y copias, y preparar un clúster de 3 nodos con almacenamiento compartido | Soporte VT-x/AMD-V de los PC del aula: N/C (Tasca 2) |
| Gestión centralizada | Directorio único de usuarios, equipos y grupos; GPO en clientes Windows; DNS interno; clientes Linux unidos al dominio | Implantación completa en fases posteriores (RA4 y RA5 de ISO) |
| Clientes | Ejecutar las aplicaciones de cada departamento, unirse al dominio, recibir actualizaciones automáticas y cifrar el disco | Aplicaciones concretas: supuesto (tabla siguiente) |
| Datos | Carpetas por departamento por SMB y NFS con los usuarios del dominio, cuotas por departamento y usuario, y almacenamiento NFS para Proxmox | Volumen N/C |
| Recuperación | Tolerar la caída de un disco, copias automáticas en un destino independiente del NAS y restauración comprobada (un cliente vuelve a abrir el fichero con contenido y permisos correctos) | Objetivos de tiempo de recuperación: se fijan en el plan de contingencia (Tasca 8) |
| Supervisión | Avisos del estado de discos y volúmenes, registros de los servidores y monitorización de recursos frente a la línea base | Umbrales de alerta: se fijan en la Tasca 7 |

## Necesidades por departamento

| Departamento | Tareas | Aplicaciones (supuesto) | SO propuesto |
|---|---|---|---|
| Dirección | Consulta de informes y correo; datos confidenciales | Office y consulta del ERP | Windows 11 Pro con BitLocker |
| Administración | Contabilidad, facturación y trámites con certificados | ERP, Excel con macros, certificados digitales y facturación electrónica | Windows 11 Pro |
| Comercial | Trabajo fuera de la oficina con portátiles | Office, CRM/ERP y cliente VPN | Windows 11 Pro con BitLocker |
| Producción | Terminales de planta con uso restringido | Software de gestión de producción y de etiquetas solo para Windows | Windows 11 Pro bloqueado con GPO |
| Informática | Administración de servidores, red y dominio | SSH, scripting, herramientas de red y RSAT | Ubuntu Desktop 26.04 LTS con VM de Windows 11 Pro |

La justificación de cada sistema está en [03-sistemes](03-sistemes.md#clientes) y en las decisiones [D-01 a D-05](../decisions/).
