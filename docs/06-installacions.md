# 06 · Instalaciones y comunicaciones

Tasca 5 «Instal·lació i comunicacions» · RA principales: ISO RA1 d-h y FDM RA1 b-d.

> Cada bloque con **Qué poner aquí** está sin hacer. Cuando hagamos esa parte, borramos el texto orientativo y escribimos lo que hemos hecho y aprendido: el porqué y el cómo.

## 1. Requisitos, licencia, recursos e imagen

> **Qué poner aquí:** de cada sistema que instalemos, los requisitos que hemos comprobado, la licencia, los recursos que le damos y de dónde hemos descargado la imagen. Si el fabricante publica el hash, cómo hemos verificado la integridad.

| Sistema | Versión | Origen | SHA256 verificado | Ubicación |
|---|---|---|---|---|

## 2. Firmware, arranque, particionado y sistema de ficheros

> **Qué poner aquí:** por qué elegimos UEFI o BIOS, el orden de arranque, cómo particionamos y qué sistema de ficheros usamos. Comprobar requisitos como TPM y arranque UEFI; en las VM, qué dispositivos virtuales les hemos puesto.

## 3. Instalaciones

> **Qué poner aquí:** las opciones que elegimos al instalar cada sistema, la versión, los dispositivos y el software instalado y su origen.

## 4. Actualización y vía de recuperación

> **Qué poner aquí:** cómo hemos actualizado cada sistema, cómo comprobamos que funciona después y qué tenemos preparado para volver atrás si un cambio sale mal.

## 5. Incidencia de instalación o arranque

> **Qué poner aquí:** una incidencia real (o provocada en una VM que se pueda tirar): síntoma, hipótesis, comprobación, solución y vuelta al estado anterior. Se puede rellenar con la [plantilla de incidencias](../incidencies/_plantilla.md).

## Conexión permitida y conexión bloqueada

> **Qué poner aquí:** qué red es la LAN y cuál la exterior de pruebas, una conexión que se permite, otra que se bloquea y cómo leemos el registro de cada una.

## Servidores de comunicaciones

Las tres soluciones (MikroTik, OPNsense y GNU/Linux con nftables) se prueban una detrás de otra en el mismo escenario.

### MikroTik

> **Qué poner aquí:** usuario de administración, red en las interfaces WAN y LAN, salida a Internet de los equipos de la LAN, copia de la configuración en el servidor de copias y una prueba de fallo con su recuperación.

### OPNsense

> **Qué poner aquí:** lo mismo que en MikroTik: usuario de administración, red WAN y LAN, salida a Internet de la LAN, copia de la configuración y prueba de fallo con su recuperación.

### GNU/Linux con nftables

> **Qué poner aquí:** lo mismo que en MikroTik: usuario de administración, red WAN y LAN, salida a Internet de la LAN, copia de la configuración y prueba de fallo con su recuperación.

## Servidor NAS/SAN: TrueNAS

> **Qué poner aquí:** instalación del sistema, usuarios de administración, parámetros de red y el disco adicional (o la partición) donde guardamos las copias de configuraciones e imágenes de los equipos críticos.

## Servidor de virtualización: Proxmox

> **Qué poner aquí:** usuarios de administración, parámetros de red, clúster con los nodos y servidores virtualizados de la empresa. Además, respuestas propias a:
> - ventajas e inconvenientes de virtualizar servidores;
> - diferencia entre contenedores y máquinas virtuales, y cuándo usar cada uno;
> - migración de un contenedor y de una VM entre nodos: tiempo, espacio en disco y si se puede mejorar;
> - qué tiene sentido copiar del clúster al servidor de copias y qué no.

## Servidor de centralización de dominio: Windows Server 2025

> **Qué poner aquí:** usuarios de administración y parámetros de red. Explicar qué es un dominio y qué le aporta a nuestra empresa centralizar la gestión. Qué configuraciones o sistemas copiamos al servidor de copias y por qué (y qué no).

## Servidores de infraestructura: GNU/Linux

> **Qué poner aquí:** de cada servidor, usuarios de administración, parámetros de red, el software que hemos instalado para su función y cómo lo hemos configurado, y la copia de su configuración o imagen.

## Clientes: Windows 11 y GNU/Linux

> **Qué poner aquí:** de cada cliente, usuario de administración, parámetros de red, si lo unimos al dominio (y por qué) y la copia de su configuración o imagen.

## Ampliación: copias automatizadas

> **Qué poner aquí:** cómo hemos automatizado las copias de configuraciones o imágenes y, si lo conseguimos, la recuperación automática.
