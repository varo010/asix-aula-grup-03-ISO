# 05 · Instalaciones y comunicaciones

Tasca 5 «Instal·lació i comunicacions» · RA principales: ISO RA1 d-h y FDM RA1 b-d.

## 1. Requisitos, licencia, recursos e imagen

> Cuando preparéis cada instalación, escribid aquí cómo y qué habéis hecho: requisitos comprobados, licencia, recursos asignados, de dónde sale la imagen y cómo habéis verificado su integridad (si el fabricante publica el hash).

| Sistema | Versión | Origen | SHA256 verificado | Ubicación |
|---|---|---|---|---|
| TrueNAS | SCALE 25.10.7 (Goldeye), `TrueNAS-SCALE-25.10.7.iso` | ISO de la página oficial de TrueNAS | N/C | G3-PC-02 (Ticnova), instalado en el SSD de 256 GB |
| Lubuntu | 24.04.4 Desktop amd64, `lubuntu-24.04.4-desktop-amd64.iso` | ISO de la página oficial de Lubuntu | N/C | G3-PC-01, sistema operativo principal |
| OPNsense | 26.7 | OVA de la página oficial de OPNsense | N/C | VM de VirtualBox en G3-PC-01 |
| Debian | 13.1.0 netinst amd64, `debian-13.1.0-amd64-netinst.iso` | ISO del servidor de recursos del centro: http://deb.cipfpbatoi.lan/recursos/ISO/ | N/C | VM de VirtualBox en G3-PC-01 |

## 2. Firmware, arranque, particionado y sistema de ficheros

> Cuando instaléis cada sistema, escribid aquí cómo y qué habéis hecho con el firmware (UEFI o BIOS), el orden de arranque, el particionado y el sistema de ficheros, y por qué. Incluid la comprobación de TPM y UEFI y, en las VM, los dispositivos virtuales.

## 3. Instalaciones

> Cuando instaléis cada sistema, escribid aquí cómo y qué habéis hecho: opciones elegidas, versión, dispositivos y software instalado con su origen.

| Equipo | Qué hemos instalado | Estado |
|---|---|---|
| G3-PC-02 (Ticnova) | TrueNAS como sistema del equipo | Instalado y configurado: pool, datasets, usuario y carpetas SMB (ver [TrueNAS](#servidor-nassan-truenas)) |
| G3-PC-01 | Lubuntu como sistema operativo principal, con VirtualBox | Instalado |
| G3-PC-01 (VM) | OPNsense en una máquina virtual de VirtualBox | Instalado, sin configurar |
| G3-PC-01 (VM) | Debian en una máquina virtual de VirtualBox | Instalado y configurado como router con nftables (ver [GNU/Linux con nftables](#gnulinux-con-nftables)) |

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

Explicación sencilla, con el esquema y los problemas que tuvimos: [Tasca5_MikroTik_Configuracion_Grupo3.pdf](../lliuraments/Tasca5_MikroTik_Configuracion_Grupo3.pdf).

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

Instalado en una máquina virtual de VirtualBox dentro del Lubuntu de G3-PC-01. Todavía no está configurado.

> Cuando instaléis y configuréis OPNsense, escribid aquí cómo y qué habéis hecho: usuario de administración, red WAN y LAN, salida a Internet de la LAN, copia de la configuración y prueba de fallo con su recuperación.

### GNU/Linux con nftables

Hemos convertido una máquina virtual con Debian (sin escritorio, solo texto) en un **router**: un equipo que hace de puerta de salida a Internet para nuestra red interna, igual que el MikroTik pero hecho con un Linux normal. La máquina se llama `debian-enrutador` y funciona dentro de VirtualBox, en el Lubuntu de G3-PC-01.

Documento original del grupo, con todos los comandos: [Tasca5_Debian_nftables_Configuracion_Grupo3.pdf](../lliuraments/Tasca5_Debian_nftables_Configuracion_Grupo3.pdf).

#### La idea en pocas palabras

Una casa con dos puertas: una da a la calle (Internet) y otra al pasillo interior (nuestra red). El Debian está en medio. Los equipos de dentro le pasan lo que quieren enviar fuera, él lo saca por la puerta de la calle **con su propio nombre** y, cuando llega la respuesta, se la devuelve a quien la pidió. Además, hace de **portero**: desde fuera no deja entrar a nadie que no haya sido invitado desde dentro.

```
Equipo de la red interna (192.168.30.x)
        │
        ▼
 [ Debian · puerta interior 192.168.30.3 ]
 [ Debian · puerta a la calle 172.16.222.134 ]
        │
        ▼
     Internet
```

#### Las dos «puertas» (tarjetas de red)

| Puerta | Nombre en Debian | Dirección | Qué hace |
|---|---|---|---|
| Hacia fuera (WAN) | `enp0s3` | 172.16.222.134, la da automáticamente la red del centro | Sale a Internet a través de la red del aula |
| Hacia dentro (LAN) | `enp0s8` | 192.168.30.3, fija, puesta a mano | Es la puerta por la que entran los equipos de nuestra red |

#### Paso a paso

**1. Darle dos tarjetas de red en VirtualBox**

- **Qué:** con la máquina apagada, en *Configuración › Red* activamos dos adaptadores:
  - **Adaptador 1 en «Adaptador puente»** sobre la tarjeta física del PC: la máquina virtual aparece en la red del aula como un equipo más y recibe su dirección (172.16.222.134).
  - **Adaptador 2 en «Red interna»**, con un nombre que comparten todas las máquinas de nuestra LAN: es como un cable que solo une a las máquinas virtuales que usan ese mismo nombre.
- **Por qué:** un router necesita estar a la vez en las dos redes. El orden importa: el adaptador 1 aparece en Debian como `enp0s3` y el 2 como `enp0s8`; si se cambian, las reglas del paso 4 dejan de funcionar.
- **Detalles que nos hicieron perder tiempo:** el adaptador 2 viene **desactivado** de fábrica (hay que marcar *Habilitar adaptador de red*) y en *Avanzadas* tiene que estar marcado *Cable conectado*. Si no, Debian ve la tarjeta «apagada».

**2. Poner la dirección fija de la puerta interior**

- **Qué:** le dijimos a Debian que la tarjeta `enp0s8` tenga siempre la dirección 192.168.30.3.
- **Por qué:** los equipos de dentro tienen que saber siempre dónde está su puerta de salida; si la dirección cambiara, se perderían. La puerta de fuera no se toca porque ya recibe su dirección sola.
- **Ojo:** a esta tarjeta **no** se le pone puerta de enlace. La salida a Internet ya la da la otra tarjeta; si tuviera dos salidas, Debian no sabría por cuál ir.

<details><summary>Comandos</summary>

Se añade al final del fichero `/etc/network/interfaces`:

```
auto enp0s8
iface enp0s8 inet static
    address 192.168.30.3/24
```

Y se enciende y comprueba con `ifup enp0s8` y `ip -br a` (deben salir las dos tarjetas `UP` con sus direcciones).

</details>

**3. Permitir que Debian pase tráfico de una puerta a otra**

- **Qué:** activamos el «reenvío» (*IP forwarding*).
- **Por qué:** por seguridad, Linux viene de fábrica sin pasar paquetes de una tarjeta a otra: lo que le llega por una puerta no lo saca por la otra. Un router tiene que hacer justo eso, así que hay que activarlo.

<details><summary>Comandos</summary>

```
echo "net.ipv4.ip_forward=1" > /etc/sysctl.d/99-forward.conf
sysctl --system
sysctl net.ipv4.ip_forward
```

El último debe responder `net.ipv4.ip_forward = 1`.

</details>

**4. Poner las normas del portero y la salida con su nombre (nftables)**

**nftables** es el programa de Linux que decide qué tráfico pasa y cuál no. Escribimos tres normas:

- **Lo que va al propio Debian:** por defecto se rechaza todo, salvo: lo que viene de nuestra red interna, las respuestas a conexiones que él mismo ha abierto, el `ping` (para comprobar que está vivo) y la conexión remota por SSH.
- **Lo que pasa a través de Debian:** por defecto se rechaza todo, salvo lo que va **de dentro hacia fuera** y sus respuestas. Desde Internet nadie puede iniciar una conexión hacia nuestros equipos.
- **Salida con su nombre (NAT o *masquerade*):** todo lo que sale por la puerta de fuera lleva la dirección del Debian (172.16.222.134) en lugar de la del equipo interno. Hace falta porque las direcciones 192.168.30.x son privadas y la red del aula no sabría devolver las respuestas.

<details><summary>Fichero <code>/etc/nftables.conf</code></summary>

```
#!/usr/sbin/nft -f

flush ruleset

table inet filter {
        chain input {
                type filter hook input priority filter; policy drop;
                iif lo accept
                ct state established,related accept
                ct state invalid drop
                icmp type echo-request accept
                iifname "enp0s8" accept
                tcp dport 22 accept
        }
        chain forward {
                type filter hook forward priority filter; policy drop;
                ct state established,related accept
                iifname "enp0s8" oifname "enp0s3" accept
        }
        chain output {
                type filter hook output priority filter; policy accept;
        }
}

table ip nat {
        chain postrouting {
                type nat hook postrouting priority srcnat; policy accept;
                oifname "enp0s3" masquerade
        }
}
```

Para revisarlo, aplicarlo y que se cargue solo al encender:

```
nft -c -f /etc/nftables.conf     # solo revisa que esté bien escrito
nft -f /etc/nftables.conf        # lo aplica
systemctl enable --now nftables  # lo deja activo en cada arranque
nft list ruleset                 # muestra las normas cargadas
```

</details>

**5. Configurar los equipos de la red interna**

Cada equipo que quiera salir a Internet a través del Debian se configura así:

| Dato | Valor | Qué significa |
|---|---|---|
| Dirección | Una libre de la red 192.168.30.x | Su «número de casa» en nuestra red |
| Máscara | 255.255.255.0 | Indica que todos los 192.168.30.x son vecinos |
| Puerta de enlace | 192.168.30.3 | A quién le pasa lo que va a Internet: el Debian |
| DNS | 10.239.3.7, 10.239.3.8 y 172.16.20.1 | La «guía telefónica» que traduce nombres (google.com) en direcciones. Son los del centro, porque los públicos, como 1.1.1.1, pueden estar bloqueados en esta red |

Las máquinas virtuales cliente tienen su adaptador en **«Red interna» con el mismo nombre** que el adaptador 2 del Debian; si no, no están «enchufadas» a él.

**6. Comprobar que todo sigue igual después de reiniciar**

Cada pieza queda guardada en un fichero para que no se pierda al apagar:

| Qué | Dónde se guarda |
|---|---|
| Dirección fija de la puerta interior | `/etc/network/interfaces` |
| Normas del portero y NAT | `/etc/nftables.conf` (y el servicio `nftables` activado) |
| Reenvío de tráfico | `/etc/sysctl.d/99-forward.conf` |

Tras `reboot` se comprueba con `ip -br a`, `sysctl net.ipv4.ip_forward` y `nft list ruleset`: la puerta interior con 192.168.30.3, el reenvío en 1 y la regla `masquerade` en la tabla `nat`.

#### Cómo se comprueba desde un equipo de dentro

Se prueba en este orden, porque así, si algo falla, se sabe en qué paso está el problema:

| Prueba | Si funciona, quiere decir que… |
|---|---|
| `ping 192.168.30.3` | el equipo llega al Debian |
| `ping 1.1.1.1` (o `ping 10.239.3.7` si el centro bloquea 1.1.1.1) | el Debian deja salir y hace el NAT |
| `ping google.com` | además funcionan los nombres (DNS) |

> Cuando hagáis las pruebas desde un cliente, escribid aquí cómo y qué habéis hecho: qué equipo usasteis y qué respondió cada prueba.

#### Problemas que tuvimos y cómo los resolvimos

| Síntoma | Causa | Solución |
|---|---|---|
| La tarjeta interior (`enp0s8`) salía apagada y sin dirección | El adaptador 2 no estaba activado en VirtualBox y Debian tampoco tenía configurada esa tarjeta | Habilitar el adaptador 2 (con *Cable conectado*) y añadir la tarjeta en `/etc/network/interfaces` |
| Los equipos de dentro no salían a Internet | En las normas del NAT, la cadena estaba enganchada en `output` en vez de en `postrouting`, con la prioridad equivocada o sin la regla `masquerade` | Enganchar la cadena en `postrouting`, prioridad `srcnat`, y añadir `masquerade` |
| Las teclas salían cambiadas al escribir en la máquina | No era cosa de Debian, sino de Remmina, el programa con el que nos conectamos en remoto al PC que tiene la máquina virtual | Corregir la distribución del teclado en Remmina |
| `sudo` no funcionaba después de dar permisos al usuario | El permiso de administrador (grupo `sudo`) solo se aplica al volver a iniciar sesión | Cerrar sesión y volver a entrar |
| Comandos como `sysctl` o `nft` no se encontraban | Se entró como administrador con `su` sin el guion, y no se cargaban las rutas de los programas de administración | Usar siempre `su -` (con guion) |

Un aviso que sale al activar `nftables`, `Failed to query local AF_VSOCK CID`, es normal en máquinas virtuales y no afecta.

#### Pendiente

> Cuando hagáis la copia de la configuración del Debian y la guardéis en el TrueNAS, escribid aquí cómo y qué habéis hecho: qué ficheros copiáis y cómo se restauran.

> Cuando provoquéis un fallo en el Debian y lo recuperéis, escribid aquí cómo y qué habéis hecho: qué fallo, cómo lo detectasteis, cómo lo recuperasteis con la copia y cuánto tardasteis.

## Servidor NAS/SAN: TrueNAS

TrueNAS Community Edition 25.10.7 (Goldeye) está instalado en G3-PC-02 (Ticnova). Lo configuramos el 27/10 y el 03/11 siguiendo los apartados 7 y 8 de la guía de Iván, adaptados a nuestro hardware: usamos el disco real de 2 TB en lugar de un disco virtual de 15 GB.

Los pasos y las horas salen del **registro de auditoría** de TrueNAS, que apunta cada cambio que se hace en el equipo. El estado actual y el registro completo están en [`evidencies/t5-truenas-configuracion-20261103.txt`](../evidencies/t5-truenas-configuracion-20261103.txt). Las contraseñas no se escriben en el repositorio.

### Cómo queda

| Qué | Valor |
|---|---|
| Acceso web | `https://192.168.30.20` con el usuario `truenas_admin` |
| Red | `enp1s0` con la IP fija 192.168.30.20/24, sin DHCP |
| Disco del sistema | SSD de 256 GB (`sda`), pool `boot-pool`: solo guarda TrueNAS |
| Disco de datos | HDD Seagate de 2 TB (`sdb`), pool `Pool_Principal`: 1,76 TiB libres |
| Carpetas (datasets) | `Documentos` y `Configuraciones`, con compresión LZ4 |
| Usuario para las carpetas | `userred` («Usuario de red»): solo sirve para entrar por SMB |
| Carpetas compartidas | `\\192.168.30.20\documentos` y `\\192.168.30.20\configuraciones` |
| Servicio SMB | En marcha y con arranque automático |

### Las palabras que hay que entender

- **Pool:** ZFS (el sistema de ficheros de TrueNAS) junta uno o varios discos en un solo «disco grande», que es el pool. Los datos se guardan siempre dentro de un pool.
- **Stripe:** es la forma de montar un pool con un solo disco. Tiene todo el espacio, pero **ninguna protección**: si el disco se estropea, se pierde todo. Para tener protección hacen falta al menos dos discos (*Mirror*, que es como un RAID 1).
- **Dataset:** es una carpeta especial dentro del pool, con sus propias opciones (compresión, límite de espacio, permisos, instantáneas). Hacemos uno por cada tipo de datos.
- **LZ4:** es una compresión rápida que TrueNAS activa por defecto. Ocupa menos espacio y casi no gasta CPU.
- **SMB:** es el protocolo para compartir carpetas en red. Es el que usa el explorador de Windows, y Linux y macOS también lo entienden. Una carpeta compartida por SMB se abre como si fuera un disco de red.
- **Usuario SMB:** es un usuario que solo sirve para entrar a las carpetas compartidas. No entra a la web de administración ni por SSH. Así, si alguien conoce su contraseña, no puede tocar la configuración del NAS.
- **Permisos 777:** el dueño, el grupo y *todos los demás* pueden leer, escribir y entrar (`rwx` tres veces).

### Paso a paso: los clics que hicimos

#### 27/10 · Red, desde la pantalla del propio TrueNAS

Al terminar la instalación, TrueNAS muestra un menú de texto con opciones numeradas en su propio monitor.

1. Opción **1) Configure network interfaces** → elegimos la tarjeta `enp1s0` → quitamos el DHCP (`ipv4_dhcp: No`) → añadimos el alias **192.168.30.20/24** → guardamos y aplicamos los cambios (*commit*). Tuvimos que repetirlo varias veces hasta que quedó bien guardado (10:42–10:50).
2. Opción **2) Configure network settings** → intentamos cambiar el nombre del equipo a `truenas_g3`. **Falló dos veces**, porque un nombre de equipo no puede tener guion bajo (`_`): solo letras, números y guion (`-`). Por eso el equipo se sigue llamando `truenas`. En esa misma pantalla dejamos vacíos la puerta de enlace y los DNS.
3. Desde un portátil de la red, entramos en el navegador a `https://192.168.30.20` con el usuario `truenas_admin`.
4. **System → General Settings → Localization → Settings:** zona horaria **Europe/Madrid** y teclado **es** (15:46).

#### 03/11 · Pool y datasets (apartado 7 de la guía)

5. **Storage → Create Pool** → *Name*: `Pool_Principal` → en *Data* elegimos el disco **sdb** (el HDD de 2 TB) con el diseño **Stripe** → TrueNAS avisa de que sin redundancia se pierden los datos si falla el disco → marcamos que lo entendemos → **Create Pool** (11:43).
6. **Datasets** → seleccionamos `Pool_Principal` → **Add Dataset** → *Name*: `Configuraciones` → **Save** (11:43). Lo borramos (**Delete**, 11:44) y lo volvimos a crear igual (11:44).
7. **Add Dataset** otra vez → *Name*: `Documentos` → resto de opciones por defecto (preset *Generic*, compresión heredada LZ4) → **Save** (11:44).

#### 03/11 · Usuario y carpetas compartidas (apartado 8 de la guía)

8. **Credentials → Users → Add:**
   - *Full Name*: `Usuario de red`; *Username*: `userred`; *Password*: la del grupo.
   - Marcado **SMB User** y **Create New Primary Group**.
   - Sin carpeta personal (`/var/empty`) y sin consola (`nologin`).
   - **Save** (11:48).
9. **Shares → Windows (SMB) Shares → Add** → *Path*: `/mnt/Pool_Principal/Documentos` → *Name*: `documentos` → *Purpose*: por defecto → **Save** (11:49).
10. Sale el aviso de que el servicio SMB está apagado → **Enable Service**. TrueNAS lo arranca y lo deja en arranque automático (11:49). Esto es lo que pide el apartado 8.3 de la guía (*Running* + *Start Automatically*).
11. **Add** otra vez → *Path*: `/mnt/Pool_Principal/Configuraciones` → *Name*: `configuraciones` → **Save** (11:49).
12. **Credentials → Users** → `userred` → **Edit** → *Auxiliary Groups*: `builtin_users` → **Save** (11:51).

#### 03/11 · Problema con los permisos

13. **Datasets → Configuraciones → Permissions → Edit** → en el editor de ACL añadimos el usuario `userred` con lectura, escritura y ejecución → **Save Access Control List**. **Falló**, y lo volvimos a intentar marcando *Apply permissions recursively*. Lo probamos 5 veces entre las 12:13 y las 12:19, y no se guardó ninguna.
14. Mientras tanto, **Shares → Windows (SMB) Shares → Config Service** → marcamos **Enable SMB1 support** → **Save** (12:17). También abrimos y guardamos la carpeta `configuraciones` sin cambiar nada (12:17).
15. Al final usamos el editor sencillo de permisos (Unix): **Datasets → Documentos → Permissions → Edit** → *User*: `userred` → todas las casillas marcadas (**777**) → **Save** (12:19). Lo mismo en `Configuraciones`, con *Group*: `root` (12:20). Esto sí funcionó.

> Cuando reviséis este problema, escribid aquí con vuestras palabras qué error salía al entrar a la carpeta o al guardar la ACL. Por el registro creemos que la ACL falló porque en las ACL POSIX, al añadir un usuario concreto, hace falta también una entrada **MASK**, pero no lo vimos en pantalla.

> Cuando comprobéis la conexión desde un cliente, escribid aquí cómo y qué habéis hecho: desde qué equipo, qué dirección escribisteis (`\\192.168.30.20\documentos` en Windows o `smb://192.168.30.20/documentos` en Linux), con qué usuario y si pudisteis crear y abrir un fichero.

### Qué falta o hay que mejorar

1. **Puerta de enlace y DNS vacíos.** El NAS funciona dentro de nuestra red, pero no puede salir: ni actualizaciones, ni hora por NTP, ni avisos por correo. Se pone en **Network → Global Configuration → Settings**, con gateway `192.168.30.1` y DNS `192.168.30.1` (el router).
   > Cuando lo configuréis, escribid aquí cómo y qué habéis hecho y cómo comprobasteis que el NAS sale a Internet.
2. **SMB1 activado.** Es la versión antigua del protocolo, la que aprovechó el virus WannaCry. Los clientes actuales usan SMB2/3, así que hay que desactivarlo salvo que un equipo lo necesite de verdad.
   > Cuando lo desactivéis (o si decidís dejarlo), escribid aquí cómo y por qué.
3. **Permisos 777.** Ahora cualquier usuario del NAS puede escribir en las dos carpetas. Por red sigue pidiendo usuario y contraseña, porque el acceso de invitado no está activado, pero no separa departamentos. Para la Tasca 6 hay que cambiarlo por ACL con un grupo por departamento.
4. **Un solo disco (Stripe).** Si el HDD de 2 TB falla, se pierden los datos. En la Tasca 2 vimos que ya lleva casi 5 años encendido. Lo aceptamos para el laboratorio, pero en la empresa harían falta dos discos en *Mirror*.
5. **Sin copias automáticas.** No hay instantáneas (*snapshots*) ni réplicas programadas. El apartado 9 de la guía de Iván explica las instantáneas.

> Cuando uséis el dataset `Configuraciones`, escribid aquí qué guardáis en él (por ejemplo, las copias `/export` y `/system backup` del MikroTik) y cómo lo copiáis.

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

G3-PC-01 tiene Lubuntu como sistema operativo principal y VirtualBox para las máquinas virtuales de OPNsense y Debian.

> Cuando instaléis y configuréis cada cliente, escribid aquí cómo y qué habéis hecho: usuario de administración, parámetros de red, si lo unís al dominio (y por qué) y la copia de su configuración o imagen.

## Ampliación: copias automatizadas

> Cuando automaticéis las copias, escribid aquí cómo y qué habéis hecho y, si lo conseguís, cómo funciona la recuperación automática.
