# 06 · Instalaciones y comunicaciones

Tasca 5 «Instal·lació i comunicacions» · RA principales: ISO RA1 d-h y FDM RA1 b-d.

## 1. Requisitos, licencia, recursos e imagen

> Cuando preparéis cada instalación, escribid aquí cómo y qué habéis hecho: requisitos comprobados, licencia, recursos asignados, de dónde sale la imagen y cómo habéis verificado su integridad (si el fabricante publica el hash).

| Sistema | Versión | Origen | SHA256 verificado | Ubicación |
|---|---|---|---|---|

## 2. Firmware, arranque, particionado y sistema de ficheros

> Cuando instaléis cada sistema, escribid aquí cómo y qué habéis hecho con el firmware (UEFI o BIOS), el orden de arranque, el particionado y el sistema de ficheros, y por qué. Incluid la comprobación de TPM y UEFI y, en las VM, los dispositivos virtuales.

## 3. Instalaciones

> Cuando instaléis cada sistema, escribid aquí cómo y qué habéis hecho: opciones elegidas, versión, dispositivos y software instalado con su origen.

## 4. Actualización y vía de recuperación

> Cuando actualicéis cada sistema, escribid aquí cómo y qué habéis hecho: cómo comprobáis que funciona después y qué tenéis preparado para volver atrás si un cambio sale mal.

## 5. Incidencia de instalación o arranque

> Cuando resolváis una incidencia de instalación o arranque (real o provocada en una VM descartable), escribid aquí cómo y qué habéis hecho: síntoma, hipótesis, comprobación, solución y vuelta al estado anterior. Podéis usar la [plantilla de incidencias](../incidencies/_plantilla.md).

## Conexión permitida y conexión bloqueada

> Cuando probéis el filtrado, escribid aquí cómo y qué habéis hecho: qué red es la LAN y cuál la exterior de pruebas, una conexión permitida, otra bloqueada y cómo interpretáis el registro de cada una.

## Servidores de comunicaciones

Las tres soluciones (MikroTik, OPNsense y GNU/Linux con nftables) se prueban una detrás de otra en el mismo escenario.

### MikroTik

> Cuando configuréis el MikroTik, escribid aquí cómo y qué habéis hecho: usuario de administración, red en las interfaces WAN y LAN, salida a Internet de la LAN, copia de la configuración en el servidor de copias y una prueba de fallo con su recuperación.

### OPNsense

> Cuando instaléis y configuréis OPNsense, escribid aquí cómo y qué habéis hecho: usuario de administración, red WAN y LAN, salida a Internet de la LAN, copia de la configuración y prueba de fallo con su recuperación.

### GNU/Linux con nftables

> Cuando instaléis y configuréis el GNU/Linux con nftables, escribid aquí cómo y qué habéis hecho: usuario de administración, red WAN y LAN, salida a Internet de la LAN, copia de la configuración y prueba de fallo con su recuperación.

## Servidor NAS/SAN: TrueNAS

> Cuando instaléis y configuréis TrueNAS, escribid aquí cómo y qué habéis hecho: instalación, usuarios de administración, parámetros de red y el disco adicional (o la partición) para las copias de configuraciones e imágenes de los equipos críticos.

## Servidor de virtualización: Proxmox

> Cuando instaléis y configuréis Proxmox, escribid aquí cómo y qué habéis hecho: usuarios de administración, parámetros de red, clúster con los nodos y servidores virtualizados de la empresa. Además, responded con vuestras palabras:
> - ventajas e inconvenientes de virtualizar servidores;
> - diferencia entre contenedores y máquinas virtuales, y cuándo usar cada uno;
> - migración de un contenedor y de una VM entre nodos: tiempo, espacio en disco y si se puede mejorar;
> - qué tiene sentido copiar del clúster al servidor de copias y qué no.

## Servidor de centralización de dominio: Windows Server 2025

> Cuando instaléis y configuréis Windows Server 2025, escribid aquí cómo y qué habéis hecho: usuarios de administración, parámetros de red y qué configuraciones o sistemas copiáis al servidor de copias y por qué (y qué no). Explicad también qué es un dominio y qué le aporta a la empresa centralizar la gestión.

## Servidores de infraestructura: GNU/Linux

> Cuando instaléis y configuréis cada servidor de infraestructura, escribid aquí cómo y qué habéis hecho: usuarios de administración, parámetros de red, software instalado para su función y su configuración, y la copia de su configuración o imagen.

## Clientes: Windows 11 y GNU/Linux

> Cuando instaléis y configuréis cada cliente, escribid aquí cómo y qué habéis hecho: usuario de administración, parámetros de red, si lo unís al dominio (y por qué) y la copia de su configuración o imagen.

## Ampliación: copias automatizadas

> Cuando automaticéis las copias, escribid aquí cómo y qué habéis hecho y, si lo conseguís, cómo funciona la recuperación automática.
