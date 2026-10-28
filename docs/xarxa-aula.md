# Red del entorno de pruebas del aula

Tasca 3 «Rack i interconnexió» · RA principales: FDM RA1 c y h; RA4 a-d y g-i.

Un dato no comprobado todavía se marca **N/C**.

## 1. Esquema de la red

![Esquema de red](../diagrames/arquitectura-red-tasca4.png)
## 2. Elementos de la red
 
| Elemento del esquema | Físico o virtual | Finalidad o uso | En el aula |
|---|---|---|---|
| Red del aula 172.16.222.0/24 | Física | Red compartida por los grupos; salida al exterior | Red del rack |
| Switch projecte | Físico | Une la red del aula con el servidor de comunicaciones del grupo | Switch compartido |
| Servidor de comunicaciones / cortafuegos | Físico (MikroTik) y virtual (OPNsense, Debian + nftables) | Enrutar y filtrar el tráfico entre la WAN y la LAN | MikroTik: G3-RT-01 (hEX). OPNsense: VM en G3-PC-01. Debian + nftables:  VM en G3-PC-01 |
| Switch LAN (principal) | Físico | Conecta el clúster, el NAS/SAN y el switch de puestos de trabajo | Switch 1 G.3 |
| Switch LAN (puestos de trabajo) | Físico | Conecta los cinco clientes | Switch 2 G.3 |
| Nodo 1, 2 y 3 Proxmox VE | Físicos | Clúster de virtualización donde se ejecutan los servidores | Proxmox compartido en clase |
| VM Windows Server 2025 | Virtual (nodo 1) | Servidor de dominio | Proxmox compartido en clase |
| VM Debian · servicios | Virtual (nodo 2) | Servidor de servicios | Proxmox compartido en clase |
| VM Debian · datos | Virtual (nodo 3) | Servidor de datos | Proxmox compartido en clase |
| NAS / SAN | Físico | Copias, imágenes y datos | G3-PC-02 (TrueNAS) |
| Dirección | Físico | Cliente Windows 11 | Pc1.Lan |
| Administración | Físico | Cliente Windows 11 | Pc2.Lan |
| Comercial | Físico | Cliente Windows 11 | Pc3.Lan |
| Producción | Físico | Cliente Linux | Pc4.Lan |
| Informática | Físico | Cliente Linux | Pc5.Lan |
 


La configuración de red de cada interfaz (Tasca 4) está en [04-disseny](04-disseny.md#direccionamiento).

## 3. Identificación con adhesivos
### - Cableado:
 
- Sw-Clase-mikrotik
- Sw-Clase-lubuntu
- SwPrincipal-mikrotik
- SwPrincipal-Cluster
- SwPrincipal-Lubuntu
- SwPrincipa- TrueNAS
- SwPrincipal-Sw-Secundario
- Sw-Secundario-PC1
- Sw-Secundario-PC2
- Sw-Secundario-PC3
- Sw-Secundario-PC4
- Sw-Secundario-PC5
- Sw-Secundario-PC6

### - Dispositivos:
- Sw-Principal
- Sw-Secundario
- Mikrotik
- Lubuntu
- TrueNas

## 4. Ampliación: sala de servidores o CPD pequeño
Si estuviéramos en una sala de servidores o en un CPD, deberíamos tener:

- **Un SAI:** si se fuera la luz, deberíamos tener una alternativa para que no se apague ningún servidor y, a causa de ello, perder información.
- **Un servidor de copias de seguridad**, tanto físicas como lógicas, por si pasara alguna catástrofe natural.
