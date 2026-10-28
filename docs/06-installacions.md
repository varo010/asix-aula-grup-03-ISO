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

El MikroTik hEX (RB750Gr3) es el router del grupo: une nuestra red interna (LAN, 192.168.30.0/24) con la red del aula (172.16.222.0/24), que es la que tiene salida a Internet. Lo configuramos desde el terminal de WinBox con comandos de RouterOS.

#### Cómo quedan los puertos

| Puerto | Papel | Dirección | Qué hay conectado |
|---|---|---|---|
| `ether1` | WAN (salida) | 172.16.222.216/24 | Red del aula (rack) |
| `ether2`–`ether5` | LAN, unidos en el puente `bridge_grupo3` | 192.168.30.1/24 (en el puente) | Nuestros equipos |

Un **puente** (*bridge*) hace que los cuatro puertos funcionen como uno solo, igual que un switch. Así, conectes un equipo en `ether2` o en `ether5`, está en la misma red y el router tiene una sola dirección para todos: 192.168.30.1.

#### Paso a paso

**1. Red interna (LAN): puente, dirección y DHCP**

- **Qué:**
  - creamos el puente `bridge_grupo3` con `ether2`–`ether5`;
  - le pusimos la dirección 192.168.30.1/24;
  - activamos un servidor DHCP que reparte las direcciones de la .100 a la .200, con puerta de enlace 192.168.30.1 y DNS 192.168.30.1.
- **Por qué:**
  - 192.168.30.0/24 es la red que el enunciado asigna al grupo 3;
  - el router es la puerta de enlace de todos (la .1);
  - con DHCP, un equipo que se conecta recibe solo su IP, su puerta de enlace y su DNS, sin configurar nada a mano.
- **Cómo:**

> Cuando saquéis el `/export` del router, escribid aquí cómo y qué habéis hecho: los comandos con los que creasteis el puente, la dirección de la LAN, el DHCP y el NAT (en la conversación solo quedaron las comprobaciones).

Comprobación de que la LAN y el DHCP están bien:

```
/ip address print
/ip dhcp-server network print
```

En el primero debe salir **una sola** 192.168.30.1/24, en `bridge_grupo3`. En el segundo, `gateway=192.168.30.1` y `dns-server=192.168.30.1`.

**2. Dirección de la WAN**

- **Qué:** dimos a `ether1` la dirección 172.16.222.216/24.
- **Por qué:** para salir a Internet, el router necesita una dirección dentro de la red del aula, que es la que conecta con el exterior. Las direcciones de esa red las asigna el profesorado para que no se repitan entre grupos.
- **Cómo:**

```
/ip address add address=172.16.222.216/24 interface=ether1
```

**3. Ruta por defecto**

- **Qué:** todo lo que no vaya a nuestras redes se envía a 172.16.222.1, la puerta de enlace del aula.
- **Por qué:** el router solo conoce las redes a las que está conectado directamente. Para llegar a Internet necesita saber a quién pasarle el tráfico.
- **Comprobación:**

```
/ip route print
```

La ruta `0.0.0.0/0` hacia 172.16.222.1 debe salir activa (`As`).

**4. NAT**

- **Qué:** activamos el NAT de salida (*masquerade*) por `ether1`.
- **Por qué:** las direcciones 192.168.30.x son privadas y la red del aula no sabe volver a ellas. Con NAT, el router cambia la dirección del equipo por la suya (172.16.222.216) al salir y la deshace cuando vuelve la respuesta.

**5. DNS**

- **Qué:** el router pregunta los nombres al DNS del centro (172.16.222.1) y responde a los equipos de la LAN.
- **Por qué:** los equipos de la LAN reciben por DHCP el DNS 192.168.30.1 (el router), y el router reenvía cada pregunta al DNS del centro. Si algún día cambia el DNS, solo hay que tocarlo en el router.
- **Cómo:**

```
/ip dns set servers=172.16.222.1 allow-remote-requests=yes
```

`allow-remote-requests=yes` es lo que permite que el router conteste las preguntas de DNS que le llegan de la LAN.

- **Comprobación:**

```
:put [:resolve google.com]
```

Devolvió 216.239.38.120, así que la cadena LAN → router → DNS del centro → Internet funciona.

#### Problemas que tuvimos y cómo los resolvimos

| Síntoma | Causa | Solución |
|---|---|---|
| `invalid value for argument address` al poner la IP de la WAN | Pegamos el comando del ejemplo con la `X` literal (`172.16.222.X`) en lugar del número | Repetir el comando con la dirección real: 172.16.222.216 |
| En `/ip route print` salían dos rutas a 192.168.30.0/24 marcadas con `+` | La 192.168.30.1/24 estaba dos veces, en el puente y en `ether1`, por un intento anterior con *Quick Set*. El router no sabía por cuál de las dos ir a la LAN | Borrar la de `ether1`: `/ip address remove [find interface=ether1 address="192.168.30.1/24"]`. Comprobado con `/ip route print`: la LAN ya solo sale por `bridge_grupo3` |
| El terminal mostraba `Line 1 of 2>` y se quedaba esperando | Pulsamos Intro con un corchete `[` sin cerrar y RouterOS esperaba el resto del comando | Ctrl+C para cancelar y repetir el comando completo en una sola línea |
| `/tool fetch` fallaba con `not allowed by device-mode` | Desde RouterOS 7.17, el router trae bloqueadas por seguridad herramientas como `fetch`. No es un fallo de red | Comprobar Internet de otra forma (`:resolve` y el navegador de un portátil) |

> Cuando repaséis el fallo de «no llegamos a 1.1.1.1», escribid aquí cómo y qué habéis hecho: qué salió en `/interface ethernet monitor ether1 once`, `/ping 172.16.222.1` y `/ip arp print where interface=ether1`, el conflicto de IP que visteis con ARP, el traceroute y cómo lo resolvisteis.

> Cuando resolváis el portátil sin Internet (IP 192.168.30.103 pero puerta de enlace 172.16.222.1, que no está en nuestra red), escribid aquí cómo y qué habéis hecho: si era un perfil IPv4 manual o que estaba conectado al switch del aula, qué cambiasteis (DHCP, IP fija fuera del pool o reserva en el MikroTik) y si acabó cargando la web.

#### Pendiente

> Cuando configuréis el usuario de administración, el nombre del router y el NTP, escribid aquí cómo y qué habéis hecho y por qué (el router tenía la fecha mal, 11 de febrero, y las evidencias saldrían con fechas erróneas). La contraseña no se escribe en el repositorio.

> Cuando hagáis la copia de la configuración (`/export` y `/system backup`) y la guardéis en el TrueNAS, escribid aquí cómo y qué habéis hecho: qué diferencia hay entre los dos ficheros, dónde los guardáis y cómo se restauran.

> Cuando provoquéis un fallo en el MikroTik y lo recuperéis, escribid aquí cómo y qué habéis hecho: qué fallo, cómo lo detectasteis, cómo lo recuperasteis con la copia y cuánto tardasteis.

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
