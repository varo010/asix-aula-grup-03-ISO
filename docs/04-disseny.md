# 04 · Diseño físico y lógico

Tasca 4 «Disseny físic i lògic» · RA principales: FDM RA4; ISO RA1 y RA3.

## Esquema físico
Ver [diagrames/](../diagrames/).

## Esquema lógico
Ver [diagrames/](../diagrames/).

## Direccionamiento

Datos del esquema [arquitectura-red](../diagrames/arquitectura-red.drawio). Red LAN 192.168.30.0/24.

| Equipo | Interfaz | IP | Máscara | Puerta de enlace | DNS | Método |
|---|---|---|---|---|---|---|
| Servidor de comunicaciones | WAN | 172.16.222.30 (la asigna el profesorado) | 255.255.255.0 | N/C | N/C | Fija |
| Servidor de comunicaciones | LAN | 192.168.30.1 | 255.255.255.0 | — | N/C | Fija |
| Switch LAN (principal) | N/C | 192.168.30.2 | 255.255.255.0 | 192.168.30.1 | N/C | N/C |
| Switch LAN (puestos de trabajo) | N/C | 192.168.30.3 | 255.255.255.0 | 192.168.30.1 | N/C | N/C |
| NAS / SAN | N/C | 192.168.30.5 | 255.255.255.0 | 192.168.30.1 | N/C | N/C |
| Nodo 1 Proxmox VE | N/C | 192.168.30.11 | 255.255.255.0 | 192.168.30.1 | N/C | N/C |
| Nodo 2 Proxmox VE | N/C | 192.168.30.12 | 255.255.255.0 | 192.168.30.1 | N/C | N/C |
| Nodo 3 Proxmox VE | N/C | 192.168.30.13 | 255.255.255.0 | 192.168.30.1 | N/C | N/C |
| VM Windows Server 2025 | N/C | 192.168.30.21 | 255.255.255.0 | 192.168.30.1 | N/C | N/C |
| VM Debian · servicios | N/C | 192.168.30.22 | 255.255.255.0 | 192.168.30.1 | N/C | N/C |
| VM Debian · datos | N/C | 192.168.30.23 | 255.255.255.0 | 192.168.30.1 | N/C | N/C |
| Dirección | N/C | 192.168.30.101 | 255.255.255.0 | 192.168.30.1 | N/C | N/C |
| Administración | N/C | 192.168.30.102 | 255.255.255.0 | 192.168.30.1 | N/C | N/C |
| Comercial | N/C | 192.168.30.103 | 255.255.255.0 | 192.168.30.1 | N/C | N/C |
| Producción | N/C | 192.168.30.104 | 255.255.255.0 | 192.168.30.1 | N/C | N/C |
| Informática | N/C | 192.168.30.105 | 255.255.255.0 | 192.168.30.1 | N/C | N/C |

## Camino del tráfico y aislamiento entre LAN

## Clúster: nodos, quórum, red y almacenamiento
