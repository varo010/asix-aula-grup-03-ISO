# 04 · Diseño físico y lógico

Tasca 4 «Disseny físic i lògic» · RA principales: FDM RA4; ISO RA1 y RA3.

## Esquema físico
Esquema de la Tasca 3 completado con la configuración de red de cada interfaz: [arquitectura-red-tasca4.drawio](../diagrames/arquitectura-red-tasca4.drawio).

## Esquema lógico
Ver el mismo esquema.

## Direccionamiento

Red LAN del grupo: 192.168.30.0/24. Todas las máscaras son 255.255.255.0 (/24). «—» indica un dato pendiente; las IP WAN las asigna el profesorado.

| Rango | Uso |
|---|---|
| .1 – .15 | Servidores de comunicaciones y VM del PC-01 |
| .16 – .99 | Servidores y servicios (IP fija) |
| .100 – .200 | PC físicos (portátiles de clase) y virtuales: IP dinámica por DHCP |
| .201 – .254 | Libres (reserva) |

MikroTik, OPNsense y Ubuntu Server son las alternativas de servidor de comunicaciones. La puerta de enlace de la LAN es la del MikroTik (192.168.30.1).

| Equipo | Físico o virtual | Interfaz | IP | Puerta de enlace | DNS | Método |
|---|---|---|---|---|---|---|
| MikroTik hEX | Físico | WAN | — | — | 8.8.8.8 | Fija |
| MikroTik hEX | Físico | LAN | 192.168.30.1 | — | 8.8.8.8 | Fija |
| OPNsense | VM en el PC-01 | WAN (puente NIC USB) | — | — | 8.8.8.8 | Fija |
| OPNsense | VM en el PC-01 | LAN (puente `enp2s0`) | 192.168.30.2 | — | 8.8.8.8 | Fija |
| Ubuntu Server (enrutamiento) | VM en el PC-01 | Puente `enp2s0` | 192.168.30.3 | 192.168.30.1 | 8.8.8.8 | Fija |
| Debian (reserva de Ubuntu Server, solo instalada) | VM en el PC-01 | Puente `enp2s0` | 192.168.30.4 | 192.168.30.1 | 8.8.8.8 | Fija |
| PC-01 · Lubuntu (anfitrión) | Físico | `enp2s0` | 192.168.30.15 | 192.168.30.1 | 8.8.8.8 | Fija |
| PC-01 · Lubuntu (anfitrión) | Físico | NIC USB `enxb44bd6c64adc` | Sin IP (puente WAN de OPNsense) | — | — | — |
| NAS / SAN · TrueNAS (PC-02) | Físico | — | 192.168.30.20 | 192.168.30.1 | 8.8.8.8 | Fija |
| VM Windows Server 2025 | Virtual (nodo 1) | — | 192.168.30.21 | 192.168.30.1 | 8.8.8.8 | Fija |
| VM Debian · servicios | Virtual (nodo 2) | — | 192.168.30.22 | 192.168.30.1 | 8.8.8.8 | Fija |
| VM Debian · datos | Virtual (nodo 3) | — | 192.168.30.23 | 192.168.30.1 | 8.8.8.8 | Fija |
| Nodo 1 Proxmox VE | Físico | — | 192.168.30.31 | 192.168.30.1 | 8.8.8.8 | Fija |
| Nodo 2 Proxmox VE | Físico | — | 192.168.30.32 | 192.168.30.1 | 8.8.8.8 | Fija |
| Nodo 3 Proxmox VE | Físico | — | 192.168.30.33 | 192.168.30.1 | 8.8.8.8 | Fija |
| Dirección (Windows 11) | Físico | — | Dinámica (.100 – .200) | 192.168.30.1 (por DHCP) | 8.8.8.8 (por DHCP) | DHCP |
| Administración (Windows 11) | Físico | — | Dinámica (.100 – .200) | 192.168.30.1 (por DHCP) | 8.8.8.8 (por DHCP) | DHCP |
| Comercial (Windows 11) | Físico | — | Dinámica (.100 – .200) | 192.168.30.1 (por DHCP) | 8.8.8.8 (por DHCP) | DHCP |
| Producción (Linux) | Físico | — | Dinámica (.100 – .200) | 192.168.30.1 (por DHCP) | 8.8.8.8 (por DHCP) | DHCP |
| Informática (Linux) | Físico | — | Dinámica (.100 – .200) | 192.168.30.1 (por DHCP) | 8.8.8.8 (por DHCP) | DHCP |
| Portátil 6 (Linux) | Físico | — | Dinámica (.100 – .200) | 192.168.30.1 (por DHCP) | 8.8.8.8 (por DHCP) | DHCP |
| Clientes virtuales | Virtual | — | Dinámica (.100 – .200) | 192.168.30.1 (por DHCP) | 8.8.8.8 (por DHCP) | DHCP |
| Switch projecte, Switch LAN principal (TL-SG116) y de puestos (LS1008G) | Físicos | — | Sin IP (no gestionables) | — | — | — |

## Camino del tráfico y aislamiento entre LAN

## Clúster: nodos, quórum, red y almacenamiento
