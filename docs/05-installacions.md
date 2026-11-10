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
| G3-PC-01 (VM) | Debian en una máquina virtual de VirtualBox | Instalado, sin configurar |

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

##### 6. Crear el clúster (interfaz gráfica)

1. Entramos a la web del nodo `pve` (`https://192.168.30.32:8006`) como `root` → **Centro de datos → Clúster → Crear clúster** → *Nombre del clúster*: `grupo3-cluster` → *Enlace 0*: 192.168.30.32 → **Crear**. Desde ese momento el árbol de la izquierda sale como *Centro de datos (grupo3-cluster)*.
2. En el mismo sitio, **Información de unión** muestra los datos que necesitan los otros nodos para entrar: la IP del primer nodo, la huella digital de su certificado y un texto largo con todo junto. Con **Copiar información** se copia.
3. En la web de **pve2** (`https://192.168.30.33:8006`) → **Centro de datos → Clúster → Unirse al clúster** → pegamos la información → contraseña de `root` del nodo `pve` → **Unirse**. Lo mismo en **pve3** (`https://192.168.30.34:8006`).
4. El clúster quedó con los tres nodos: `pve`, `pve2` y `pve3`.

> Cuando lo reviséis, añadid aquí una captura de **Centro de datos → Clúster** con los tres nodos y de **Centro de datos → Resumen** con el quórum.

### Problemas que tuvimos y cómo los resolvimos


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

Debian instalado en una máquina virtual de VirtualBox dentro del Lubuntu de G3-PC-01. Todavía no está configurado.

> Cuando instaléis y configuréis el GNU/Linux con nftables, escribid aquí cómo y qué habéis hecho: usuario de administración, red WAN y LAN, salida a Internet de la LAN, copia de la configuración y prueba de fallo con su recuperación.

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

El 10/11 preparamos los **3 nodos** (nombre e IP de cada uno) y, después, creamos con ellos el clúster `grupo3-cluster` desde la interfaz gráfica de Proxmox.

No tenemos tres ordenadores libres para los nodos. Por eso usamos el **servidor físico de clase**, llamado `1asixb`, que tiene instalado Proxmox VE 9.2.25. El profesor ha dado a cada grupo un usuario y tres VM. Dentro de cada una de nuestras VM ya hay otro Proxmox instalado, y cada uno de esos Proxmox es uno de nuestros nodos. Es decir, tenemos **Proxmox dentro de Proxmox**.

### Cómo queda

| VM en `1asixb` | Nodo | IP | Gateway | DNS | Estado a 10/11 |
|---|---|---|---|---|---|
| 301 `g3-vm1` | `pve` | 192.168.30.32/24 | 192.168.30.1 | — | Hecho. Se quedó con el nombre de la plantilla (`pve`) |
| 302 `g3-vm2` | `pve2` | 192.168.30.33/24 | 192.168.30.1 | 192.168.30.1 | Hecho |
| 303 `g3-vm3` | `pve3` | 192.168.30.34/24 | 192.168.30.1 | — | Hecho |

- **Servidor de clase:** `https://192.168.30.31:8006`, con el usuario `grupo3@pve`. La .31 es la dirección del servidor `1asixb` dentro de nuestra red, así que los nodos van de la .32 a la .34 (en el diseño estaban en .31–.33).
- **Nuestros nodos:** `https://192.168.30.33:8006`, `https://192.168.30.34:8006` y `https://192.168.30.32:8006` (nodo `pve`), con el usuario `root` y el realm *Linux PAM*.
- El sistema de los nodos es Proxmox VE con el núcleo `7.0.14-19-pve`.
- Las contraseñas no se escriben en el repositorio.

Las tres VM tienen el mismo hardware (pestaña **Hardware** de cada VM en `1asixb`):

| Elemento | Valor | Qué significa |
|---|---|---|
| Memoria | 3,95 GiB | La RAM de cada nodo. Las VM que pongamos dentro tendrán que repartirse esto |
| Procesadores | 2 (1 socket, 2 núcleos), tipo **host** | El tipo *host* pasa la CPU real a la VM, con sus instrucciones de virtualización (VT-x). Sin esto, las VM que creemos dentro de los nodos no podrían arrancar |
| BIOS y máquina | SeaBIOS, i440fx | Los valores por defecto |
| Disco duro | `sata0`, 40 GB, `zfs-1asixb:base-9000-disk-0/vm-30X-disk-0` | Es un **clon** de la plantilla 9000 (ver abajo) |
| CD/DVD | vacío | No hay ISO porque Proxmox ya viene instalado en el disco |
| Red `net0` | `vmbr300`, con cortafuegos | Nuestra LAN 192.168.30.0/24. Dentro del nodo se llama `ens18` |
| Red `net1` | `vmbr31` | Red interna del servidor de clase. Dentro del nodo, `ens19`. Aún no la usamos |
| Red `net2` | `vmbr32` | Otra red interna. Dentro del nodo, `ens20`. Aún no la usamos |
| Cloud-Init | No tiene («Dispositivo CloudInit no encontrado») | El profesor no les ha dado nombre ni IP de forma automática. Hay que ponerlos a mano |

### Las palabras que hay que entender

- **Nodo:** cada servidor Proxmox que formará parte del clúster.
- **Virtualización anidada:** hacer funcionar un hipervisor (nuestro Proxmox) dentro de una VM de otro hipervisor (el Proxmox de clase).
- **Plantilla y clon enlazado:** el profesor instaló Proxmox una vez en una VM, la convirtió en plantilla (la 9000) y de ella sacó nuestras tres VM. Un clon enlazado no copia el disco entero: guarda solo lo que cambia respecto a la plantilla. Por eso las tres VM arrancaron **idénticas**: mismo nombre (`pve`), misma IP (`172.16.223.52`, que no es de nuestra red) y mismo identificador.
- **Bridge (`vmbr`):** es un switch virtual. En el servidor de clase, `vmbr300` conecta nuestras VM a nuestra LAN. Dentro de cada nodo, `vmbr0` conecta el propio nodo (y luego sus VM) con su tarjeta de red.
- **Nombre de la tarjeta de red:** Linux pone nombre a cada tarjeta. La plantilla la tenía guardada como `nic0`, pero en nuestros clones las tarjetas se llaman `ens18`, `ens19` y `ens20`, porque tienen MAC distintas a la de la plantilla.
- **`/etc/network/interfaces`:** el archivo donde Proxmox guarda la red: IP, gateway y a qué tarjeta va el bridge.
- **`/etc/hosts`:** una lista local de «nombre → IP». Proxmox la necesita: busca ahí su propio nombre para saber cuál es su IP. Si no lo encuentra, no arrancan ni la web ni el clúster.
- **`machine-id`:** un número que identifica la instalación de Linux. Al venir de la misma plantilla, los tres nodos tienen el mismo.

### Paso a paso

#### 1. Revisar las VM en el servidor de clase

1. Entramos a `https://192.168.30.31:8006` con `grupo3@pve`. En el árbol salen el nodo `1asixb`, nuestro pool `pool-grupo3` y las VM 301, 302 y 303. La 301 ya estaba encendida.
2. En cada VM miramos **Hardware** y **Cloud-Init** (tablas de arriba).
3. En **Centro de datos** no sale la opción *Clúster*: nuestro usuario solo tiene permiso sobre nuestras VM, no sobre el servidor de clase. El clúster se crea más adelante desde la web de nuestros propios nodos.

#### 2. Evitar que dos nodos tengan la misma IP

Si encendíamos la 302 y la 303 a la vez, las dos arrancarían con la misma IP. Primero intentamos desconectar la red de la 302 (**Hardware → net0 → Editar → Desconectar**), pero **Aceptar** salía desactivado: el campo *Puente* (`vmbr300`) sale en rojo porque nuestro usuario no tiene permiso sobre ese bridge.

Lo resolvimos así: **apagamos la 301**, y configuramos la 302 y la 303 **de una en una**, reiniciando cada una con su IP nueva antes de encender la siguiente.

#### 3. Configurar la 302 como `pve2` (.33)

Lo hicimos desde la **Consola** de la VM 302 en la web de clase, con el usuario `root`. Estos son los comandos en el orden en que los usamos:

```bash
# 1. Ver cómo venía la red de la plantilla (no cambia nada).
#    Vimos: address 172.16.223.52/24, gateway 172.16.223.1 y bridge-ports nic0.
cat /etc/network/interfaces

# 2. Ver la tabla de nombres (no cambia nada).
#    Vimos la línea: 172.16.223.52 pve.example.lan pve
cat /etc/hosts

# 3. Cambiar la IP de la plantilla por la nuestra.
#    sed -i 's#A#B#' busca el texto A en el archivo y lo cambia por B; -i guarda el cambio en el propio archivo.
#    La parte del gateway tenía un error de escritura y no cambió nada (se arregla en el paso 10).
sed -i 's#172\.16\.223\.52/24#192.168.30.33/24#; s#gateway 172.\.16\.223\.1#gateway 192.168.30.1#' /etc/network/interfaces

# 4. Hacer que el nombre del nodo apunte a la IP nueva en /etc/hosts.
#    Quedó escrito "pve2:grup3:lan" (con dos puntos en vez de puntos). Aun así funciona,
#    porque al final de la línea está "pve2", que es el nombre que busca Proxmox.
sed -i 's/^172\.16\.223\.52 .*/192.168.30.33 pve2:grup3:lan pve2/' /etc/hosts

# 5. Decir al nodo que pregunte los nombres de Internet (DNS) al MikroTik.
#    El > sobrescribe el archivo entero con esta línea.
echo "nameserver 192.168.30.1" > /etc/resolv.conf

# 6. Cambiar el nombre del equipo (se guarda en /etc/hostname).
#    Proxmox usa este nombre como nombre del nodo.
hostnamectl set-hostname pve2

# 7. Intentar crear un identificador nuevo (machine-id), distinto del de la plantilla.
#    Respondió "Initializing machine ID from D-Bus machine ID": lo copió de una copia antigua
#    guardada en /var/lib/dbus/, así que el identificador sigue siendo el de la plantilla.
rm /etc/machine-id
systemd-machine-id-setup

#    Intento de borrar las claves SSH de la plantilla para generar otras nuevas.
#    El nombre estaba mal escrito (es ssh_host_*), dio "No such file or directory" y no borró nada.
rm /etc/ssh/ssh_hosts_*

# 8. Aplicar la red nueva sin reiniciar.
#    Dio el error "vmbr0: bridge port nic0 does not exist": el bridge apuntaba a una tarjeta que no existe en el clon.
ifreload -a

# 9. Ver las tarjetas de red, su estado y su IP en formato corto.
#    Vimos que las tarjetas se llaman ens18, ens19 y ens20 (en DOWN) y que vmbr0 ya tenía 192.168.30.33/24.
ip -br a

# 10. Conectar el bridge a la tarjeta real (ens18 = net0 = nuestra LAN) y poner bien el gateway (el MikroTik).
#     La g del final cambia todas las veces que aparece nic0 en el archivo.
sed -i 's/nic0/ens18/g; s/gateway 172.16.223.1/gateway 192.168.30.1/' /etc/network/interfaces

# 11. Volver a aplicar la red. Esta vez sin errores.
ifreload -a

# 12. Comprobar que llega al MikroTik.
ping -c2 192.168.30.1

# 13. Reiniciar para que todos los servicios de Proxmox arranquen con el nombre nuevo.
reboot
```

Así queda `/etc/network/interfaces` en pve2:

```
auto lo
iface lo inet loopback

iface ens18 inet manual

auto vmbr0
iface vmbr0 inet static
        address 192.168.30.33/24
        gateway 192.168.30.1
        bridge-ports ens18
        bridge-stp off
        bridge-fd 0

source /etc/network/interfaces.d/*
```

#### 4. Configurar la 303 como `pve3` (.34)

Con lo aprendido en la 302, en la **Consola** de la VM 303 hicimos todo con menos comandos:

```bash
# 1. En el archivo de red: cambiar la IP, cambiar el gateway y conectar el bridge a ens18, todo de una vez.
sed -i 's/172.16.223.52/192.168.30.34/; s/gateway 172.16.223.1/gateway 192.168.30.1/; s/nic0/ens18/g' /etc/network/interfaces

# 2. Hacer que el nombre pve3 apunte a la IP nueva.
sed -i 's/^172.16.223.52 .*/192.168.30.34 pve3.grup3.lan pve3/' /etc/hosts

# 3. Cambiar el nombre del equipo.
hostnamectl set-hostname pve3

# 4. Reiniciar con la configuración nueva.
reboot
```

#### 5. Comprobación

Después de reiniciar las dos:

- En la web de cada nodo (`https://192.168.30.33:8006` y `https://192.168.30.34:8006`) el nodo sale como **pve2** y **pve3**.
- Desde un PC de nuestra LAN responden al `ping` las .32, .33 y .34, y la web de los nodos (puerto 8006) carga.

#### 6. Crear el clúster (interfaz gráfica)

1. Entramos a la web del nodo `pve` (`https://192.168.30.32:8006`) como `root` → **Centro de datos → Clúster → Crear clúster** → *Nombre del clúster*: `grupo3-cluster` → *Enlace 0*: 192.168.30.32 → **Crear**. Desde ese momento el árbol de la izquierda sale como *Centro de datos (grupo3-cluster)*.
2. En el mismo sitio, **Información de unión** muestra los datos que necesitan los otros nodos para entrar: la IP del primer nodo, la huella digital de su certificado y un texto largo con todo junto. Con **Copiar información** se copia.
3. En la web de **pve2** (`https://192.168.30.33:8006`) → **Centro de datos → Clúster → Unirse al clúster** → pegamos la información → contraseña de `root` del nodo `pve` → **Unirse**. Lo mismo en **pve3** (`https://192.168.30.34:8006`).
4. El clúster quedó con los tres nodos: `pve`, `pve2` y `pve3`.

> Cuando lo reviséis, añadid aquí una captura de **Centro de datos → Clúster** con los tres nodos y de **Centro de datos → Resumen** con el quórum.

### Problemas que tuvimos y cómo los resolvimos


- **No podíamos desconectar la red de una VM.** Nuestro usuario no tiene permiso sobre el bridge `vmbr300`. Lo resolvimos apagando la 301 y configurando las otras de una en una.
- **El nodo no tenía red aunque tenía la IP.** El bridge `vmbr0` apuntaba a `nic0`, que en el clon no existe. Lo vimos con el error de `ifreload -a` y con `ip -br a`, y lo cambiamos por `ens18`.
- **La «Información de unión» daba una IP de otra red (172.16.223.52).** Proxmox no saca esa IP de la configuración de red, sino de `/etc/hosts`: busca el nombre del nodo y copia la IP que tiene al lado. En la VM 301 se había cambiado la IP en `/etc/network/interfaces`, pero en `/etc/hosts` seguía la línea de la plantilla (`172.16.223.52 pve.example.lan pve`). Con esa IP, pve2 y pve3 no habrían podido unirse, porque esa dirección no existe en nuestra red.
  > Escribid aquí cómo lo resolvisteis.
- **Errores al escribir en la consola.** La consola del navegador (noVNC) no deja pegar texto, y al escribir a mano se colaron fallos: una `` ` `` en lugar de `/` en un `sed` (da *unknown option to `s'* y no hace nada), `:` en lugar de `.` en el nombre, y `ssh_hosts_*` en lugar de `ssh_host_*` (da *No such file or directory*). Para copiar y pegar se puede entrar por SSH desde un PC de la LAN (`ssh root@192.168.30.33`) o usar el botón **>_ Shell** en la web del propio nodo.

### Pendiente

1. **Nombre del nodo 1:** se quedó como `pve` en lugar de `pve1`. Ahora que está dentro del clúster ya no se puede cambiar sin sacarlo de él, así que lo dejamos así.
2. **`/etc/hosts` de pve2:** dejar la línea bien escrita: `192.168.30.33 pve2.grup3.lan pve2`.
3. **Identificador y claves SSH:** los tres nodos siguen teniendo el `machine-id` y las claves SSH de la plantilla. No impiden crear el clúster, pero lo correcto es que cada nodo tenga los suyos.
4. **Certificados de la web de `pve` y pve3:** siguen diciendo `pve.example.lan` y `pve3.example.lan`. Se renueva con `pvecm updatecerts --force` en el Shell del nodo.

> Cuando configuréis el almacenamiento compartido (NFS del TrueNAS) y creéis los servidores virtualizados de la empresa, escribid aquí cómo y qué habéis hecho, y también los usuarios de administración. Además, responded con vuestras palabras:
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
