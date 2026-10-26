# Red del entorno de pruebas del aula

Tasca 3 «Rack i interconnexió» · RA principales: FDM RA1 c y h; RA4 a-d y g-i.

Un dato no comprobado todavía se marca **N/C**.

## 1. Esquema de la red

Hecho con draw.io. Editable: [arquitectura-red.drawio](../diagrames/arquitectura-red.drawio). Exportación:

![Arquitectura de red del grupo 3](../diagrames/arquitectura-red.png)

## 2. Elementos de la red

| Elemento del esquema | Físico o virtual | Finalidad o uso | IP | En el aula |
|---|---|---|---|---|
| Red del aula 172.16.222.0/24 | Física | Red compartida por los grupos; salida al exterior | — | Red del rack |
| Switch projecte | Físico | Une la red del aula con el servidor de comunicaciones del grupo | — | N/C |
| Servidor de comunicaciones / cortafuegos | Físico (MikroTik) y virtual (OPNsense, Debian + nftables) | Enrutar y filtrar el tráfico entre la WAN y la LAN | WAN 172.16.222.30 · LAN 192.168.30.1 | MikroTik: G3-RT-01 (hEX). OPNsense: VM en G3-PC-01. Debian + nftables: N/C |
| Switch LAN (principal) | Físico | Conecta el clúster, el NAS/SAN y el switch de puestos de trabajo | 192.168.30.2 | N/C |
| Switch LAN (puestos de trabajo) | Físico | Conecta los cinco clientes | 192.168.30.3 | N/C |
| Nodo 1, 2 y 3 Proxmox VE | Físicos | Clúster de virtualización donde se ejecutan los servidores | 192.168.30.11 · .12 · .13 | N/C |
| VM Windows Server 2025 | Virtual (nodo 1) | Servidor de dominio | 192.168.30.21 | N/C |
| VM Debian · servicios | Virtual (nodo 2) | Servidor de servicios | 192.168.30.22 | N/C |
| VM Debian · datos | Virtual (nodo 3) | Servidor de datos | 192.168.30.23 | N/C |
| NAS / SAN | Físico | Copias, imágenes y datos | 192.168.30.5 | G3-PC-02 (TrueNAS) |
| Dirección | Físico | Cliente Windows 11 | 192.168.30.101 | N/C |
| Administración | Físico | Cliente Windows 11 | 192.168.30.102 | N/C |
| Comercial | Físico | Cliente Windows 11 | 192.168.30.103 | N/C |
| Producción | Físico | Cliente Linux | 192.168.30.104 | N/C |
| Informática | Físico | Cliente Linux | 192.168.30.105 | N/C |

Todos los equipos de la LAN usan la máscara 255.255.255.0 y la puerta de enlace 192.168.30.1. La configuración completa de cada interfaz está en [04-disseny](04-disseny.md#direccionamiento).

## 3. Identificación con adhesivos

Cada equipo lleva un adhesivo con su ID, el mismo del [inventario](02-inventari.md). Cada cable lleva un adhesivo en **cada extremo** con su ID y lo que une.

**Equipos**

| Adhesivo | Equipo | Pegado |
|---|---|---|
| G3-RT-01 | Router MikroTik hEX | ☐ |
| G3-SW-01 | Switch TL-SG116 | ☐ |
| G3-SW-02 | Switch LS1008G | ☐ |
| G3-SW-03 | Switch LS1008G | ☐ |
| G3-PC-01 | PC APD ALDA+ | ☐ |
| G3-PC-02 | PC Ticnova E70 SFF | ☐ |

**Cables** (una fila por cable en uso; formato del adhesivo: `G3-CAB-01 · RT-01/ether2 ↔ SW-01/p1`)

| Adhesivo | Extremo A (equipo/puerto) | Extremo B (equipo/puerto) | Pegado |
|---|---|---|---|
| G3-CAB-01 | N/C | N/C | ☐ |
| G3-CAB-02 | N/C | N/C | ☐ |
| G3-CAB-03 | N/C | N/C | ☐ |
| G3-CAB-04 | N/C | N/C | ☐ |

Evidencia: foto de los equipos y los cables etiquetados en `evidencies/t3-adhesivos-<fecha>.jpg` (N/C).

## 4. Ampliación: sala de servidores o CPD pequeño

Pendiente: lo redacta el grupo. ¿Qué elementos tendría que tener una sala de servidores pequeña para la empresa del enunciado y para qué serviría cada uno? Cada elemento, con su justificación.
