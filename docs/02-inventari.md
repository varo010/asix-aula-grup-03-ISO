# 02 · Inventario y diagnóstico

Tasca 2 · RA principales: FDM RA1 a-h y RA4 h-i.

Inventario del material que nos ha dado el profesorado para el entorno de pruebas y diagnóstico del estado inicial de cada equipo.

**Cómo se ha hecho y cómo se completa:**
- Las características de cada **modelo** salen de su ficha oficial (ver [Fuentes](#fuentes)).
- Lo que cambia de una **unidad** a otra (número de serie, RAM instalada, discos, versión de software, estado) se comprueba en el propio equipo con el comando indicado y se anota en la columna *Valor medido*.
- Un dato no comprobado todavía se marca **N/C** con la comprobación pendiente.
- Las capturas y salidas de comandos van saneadas a [`evidencies/`](../evidencies/) con el nombre `t2-<descripcion>-<fecha>`.

## 1. Resumen del material

| ID | Tipo | Fabricante y modelo | Características principales (ficha oficial) | Cantidad | Estado |
|---|---|---|---|---|---|
| RT-01 | Router | MikroTik hEX (RB750Gr3), revisión r4 | CPU MT7621 (MIPS 1004Kc, 880 MHz, MMIPS), 256 MiB de RAM, 16 MiB de flash, 5 puertos Gigabit, RouterOS 7.20.8 licencia nivel 4 | N/C (contar) | Funciona (datos medidos en el apartado 2) |
| SW-01 | Switch gestionable | TP-Link Omada SG3428XMPP | 24 puertos Gigabit PoE+/PoE++ (500 W) + 4 SFP+ de 10 Gb, 4K VLAN, gestión web/CLI/SNMP, rack 1U | N/C (contar) | Pendiente de probar |
| PC-01 | Ordenador de sobremesa (horizontal) | APD ALDA+ (ALDA+C), placa Gigabyte B85M-DS3H-A | Intel Core i3-4160 (2 núcleos / 4 hilos, 3,6 GHz, VT-x con EPT, sin VT-d), 8 GB DDR3-1600 (ampliable a 32 GB), disco de ~500 GB, DVD | 1 | Funciona (medido en el apartado 4) |
| PC-02 | Ordenador (torre vertical) | Ticnova E70 SFF | Por confirmar en el equipo; un ejemplar público del modelo lleva Core i3-8100 y 8 GB DDR4-2400 | 1 | Pendiente de probar |
| CAB | Latiguillos de red | Cable patch RJ45 Cat5e, 350 MHz, conector moldeado, gris | Hasta 1 Gb/s (1000BASE-T) | N/C (contar) | Pendiente de probar |
| OTR | Otros materiales | — | Adaptador de corriente del hEX (24 V, 0,38 A), cables de alimentación y kit de rack del switch | N/C (contar) | Pendiente de probar |

Estado: *Funciona* · *Funciona con incidencias* · *Averiado* · *Pendiente de probar*.

## 2. Router: MikroTik hEX (RB750Gr3)

### Ficha del modelo

| Dato | Valor (ficha oficial) |
|---|---|
| Código de producto | RB750Gr3 (hEX) |
| Procesador | MediaTek MT7621A (MIPS 1004Kc), 2 núcleos / 4 hilos, 880 MHz |
| Arquitectura | MMIPS: **no es x86**, solo ejecuta RouterOS |
| RAM | 256 MB |
| Almacenamiento | 16 MB de flash + ranura microSD |
| Puertos Ethernet | 5 × Gigabit (10/100/1000) |
| PoE | Entrada PoE pasiva (8–30 V) en ether1; sin salida PoE |
| USB | 1 × USB tipo A |
| Alimentación | Jack DC de 8–30 V; adaptador incluido de 24 V y 0,38 A |
| Consumo máximo | 10 W (5 W sin accesorios) |
| Licencia RouterOS | Nivel 4 |
| Funciones destacadas | Cifrado IPsec por hardware; paquete The Dude |
| Dimensiones | 113 × 89 × 28 mm |

### Datos de la unidad

Desde la terminal del router (Winbox → *New Terminal*, o SSH).

| Dato | Valor medido | Comando |
|---|---|---|
| Número de serie | HMF0B3M3EK8 (verificado en el equipo) | `/system routerboard print` |
| Modelo detectado | hEX, RB750Gr3, revisión r4 | `/system routerboard print` |
| Versión de RouterOS | 7.20.8 (long-term), compilada el 30/01/2026; software de fábrica 6.46.3 | `/system resource print` |
| Arquitectura y CPU | mmips; MIPS 1004Kc V2.15 a 880 MHz, RouterOS muestra 4 CPU (los 2 núcleos × 2 hilos de la ficha) | `/system resource print` |
| Canal de actualización | stable, versión instalada 7.20.8 | `/system package update print` |
| Firmware RouterBOOT (fábrica / actual / disponible) | mt7621L · 7.20.8 / 7.20.8 / 7.20.8 (al día) | `/system routerboard print` |
| RAM libre en reposo | 199,8 MiB libres de 256 MiB | `/system resource print` |
| Almacenamiento libre | 5,2 MiB libres de 16 MiB | `/system resource print` |
| Uso de CPU en reposo | 0 % | `/system resource print` |
| Tiempo encendido | 1 semana y 7 min | `/system resource print` |
| Temperatura y voltaje | 27 °C · 24,4 V | `/system health print` |
| Licencia | software-id 17UE-7K4S, nivel 4 | `/system license print` |

### Puertos

Los 5 puertos tienen MTU 1500 (L2MTU 1596), autonegociación activada anunciando 10/100/1000 en half y full dúplex, ARP activado y *loop-protect* por defecto (desactivado). Sus MAC son correlativas, de D0:EA:11:A0:2A:03 (ether1) a D0:EA:11:A0:2A:07 (ether5).

| Puerto | MAC | Velocidad máxima | Velocidad negociada | Función | Estado del enlace |
|---|---|---|---|---|---|
| ether1 (PoE-in) | D0:EA:11:A0:2A:03 | 1 Gb/s | 1 Gb/s full dúplex (autonegociación completada, control de flujo desactivado) | Independiente | Conectado (*running*), link-ok |
| ether2 | D0:EA:11:A0:2A:04 | 1 Gb/s | N/C (`monitor ether2`) | Esclavo de `switch1` | Conectado (*running*) |
| ether3 | D0:EA:11:A0:2A:05 | 1 Gb/s | — | Esclavo de `switch1` | Sin enlace |
| ether4 | D0:EA:11:A0:2A:06 | 1 Gb/s | — | Esclavo de `switch1` | Sin enlace |
| ether5 | D0:EA:11:A0:2A:07 | 1 Gb/s | — | Esclavo de `switch1` | Sin enlace |

Qué equipo hay al otro lado de ether1 y ether2: N/C (seguir el cable). Comandos: `/interface ethernet print detail`, `/interface ethernet monitor ether1`.

### Configuración de red actual

| Interfaz | Dirección IP / máscara | Origen (estática o DHCP) | Observaciones |
|---|---|---|---|
| | | | |

Comandos: `/ip address print`, `/ip dhcp-client print`, `/ip route print`. Para la evidencia: `/export file=t2-mikrotik-config-inicial`, revisando antes que no contenga contraseñas.

## 3. Switch: TP-Link Omada SG3428XMPP

### Ficha del modelo

| Dato | Valor (ficha oficial) |
|---|---|
| Tipo | Switch gestionable de capa 2+ (familia Omada) |
| Puertos RJ45 | 24 × Gigabit: 1–8 con PoE++ (hasta 90 W por puerto) y 9–24 con PoE+ (hasta 30 W por puerto) |
| Puertos SFP+ | 4 × 10 Gb/s |
| Consola | 1 × RJ45 + 1 × micro-USB |
| PoE | 802.3af/at/bt, presupuesto total de 500 W |
| Capacidad de conmutación | 128 Gb/s |
| Tasa de reenvío | 95,23 Mpps |
| Tabla MAC | 16 K entradas |
| Jumbo frames | 9 KB |
| VLAN | Hasta 4K grupos de VLAN |
| Gestión | Web, CLI (consola/Telnet), SNMP v1/v2c/v3; Omada SDN con controlador |
| Ventilación | 3 ventiladores |
| Montaje | Rack 19" (kit incluido), 1U |
| Alimentación | 100–240 V AC, 50/60 Hz |
| Consumo máximo | 605,5 W (con 500 W de PoE en uso) |
| Dimensiones | 440 × 330 × 44 mm |

### Datos de la unidad

| Dato | Valor medido | Fuente |
|---|---|---|
| Número de serie | | Etiqueta inferior |
| Versión de hardware y firmware | | Interfaz web → *System Info* |
| IP de gestión actual | | Interfaz web o consola (`show interface vlan 1`) |
| Modo de gestión (independiente u Omada) | | Interfaz web |
| VLAN configuradas | | `show vlan` |

### Puertos ocupados y libres

| Puerto | Conectado a | Velocidad del enlace | Estado | Observaciones |
|---|---|---|---|---|
| 1 | | | | |
| 2 | | | | |
| 3 | | | | |
| 4 | | | | |
| 5 | | | | |
| 6 | | | | |
| 7 | | | | |
| 8 | | | | |
| 9–24 | | | | |
| SFP+ 25–28 | — | — | Sin módulos | Sin transceptores ni cables DAC en el material |

Total: ___ puertos ocupados · ___ puertos libres. Comando: `show interface status`.

## 4. Ordenadores

Resumen de los dos equipos en [hardware-grupo3](hardware-grupo3.md).

### PC-01: APD ALDA+ (verificado con `dmidecode`)

| Dato | Valor |
|---|---|
| Equipo | APD ALDA+ (versión ALDA+C), sobremesa horizontal, dotación de la GVA con DVD |
| Placa base | Gigabyte B85M-DS3H-A (chipset Intel B85, zócalo LGA1150) |
| Procesador | Intel Core i3-4160 (4.ª generación, «Haswell», 2014): 2 núcleos / 4 hilos, 3,60 GHz sin turbo, 3 MB de caché, 54 W |
| Virtualización | Intel VT-x y VT-x con EPT (SLAT); **sin VT-d** (no permite pasar dispositivos PCI a una VM) |
| Memoria | 4 ranuras DDR3; máximo 32 GB |
| Gráficos | Intel HD Graphics 4400 integrada |

### PC-02: Ticnova E70 SFF (pendiente de verificar)

Ticnova Quality Team SL fabrica el E70 en España. Los datos marcados *por confirmar* son de otro ejemplar del mismo modelo publicado en Geekbench, no de este equipo.

| Dato | Valor |
|---|---|
| Formato | SFF, en posición de torre vertical |
| Orden de fabricación / número de serie | 701413 / 1720408949 |
| Procesador (por confirmar) | Intel Core i3-8100 (8.ª generación, «Coffee Lake»): 4 núcleos / 4 hilos, 3,6 GHz |
| Memoria (por confirmar) | 8 GB DDR4-2400 |
| Gráficos (por confirmar) | Intel UHD Graphics 630 |
| Conectividad | USB 3.0 frontal |
| Comprobación pendiente | `sudo dmidecode -t system -t baseboard -t memory`, `lscpu`, `free -h`, `lsblk -d -o NAME,MODEL,SIZE,ROTA` |

### Datos de cada unidad

Una columna por equipo; se añaden columnas si hay más. PC-01 es el APD ALDA+ `pc33-t226` y PC-02 el Ticnova E70.

| Dato | PC-01 (pc33-t226) | PC-02 (Ticnova) | PC-03 | PC-04 | PC-05 | Comando Linux | Comando Windows |
|---|---|---|---|---|---|---|---|
| Número de serie | N/C | 1720408949 | | | | `sudo dmidecode -s system-serial-number` | `Get-CimInstance Win32_BIOS` |
| Placa base | Gigabyte B85M-DS3H-A | N/C | | | | `sudo dmidecode -t baseboard` | `Get-CimInstance Win32_BaseBoard` |
| Uso previsto | N/C | | | | | Diseño (Tasca 4) | Diseño (Tasca 4) |
| CPU detectada | Intel Core i3-4160, 2C/4T, 3,6 GHz | | | | | `lscpu` | `Get-CimInstance Win32_Processor` |
| VT-x activado en BIOS | N/C | | | | | `grep -c vmx /proc/cpuinfo` | `systeminfo` |
| RAM instalada | 8 GB DDR3-1600 (7,64 GiB utilizables) | | | | | `free -h` | `Get-CimInstance Win32_PhysicalMemory` |
| Módulos y ranuras libres | 1 módulo de 8 GB DDR3-1600 en ChannelB-DIMM1; 3 de 4 ranuras libres | N/C | | | | `sudo dmidecode -t memory` | `Get-CimInstance Win32_PhysicalMemory` |
| Discos (modelo, tipo y capacidad) | ~500 GB; modelo y tipo (HDD o SSD) N/C | | | | | `lsblk -d -o NAME,MODEL,SIZE,ROTA,TRAN` | `Get-PhysicalDisk` |
| Particiones | root 57,3 GiB (53 % usado) · home 381 GiB (6 % usado) · swap 512 MiB | | | | | `lsblk`, `df -h` | `Get-Partition` |
| Estado SMART | N/C | | | | | `sudo smartctl -H -A /dev/sdX` | CrystalDiskInfo |
| Sistema operativo instalado | LliureX 21 (21.250729.1), kernel 5.15.0-113-generic, 64 bits | | | | | `cat /etc/os-release`, `uname -r` | `winver` |
| Red | Ethernet integrada: eth1, 172.16.222.19 (red del aula); velocidad N/C | | | | | `ip -br a`, `ethtool eth1` | `Get-NetAdapter` |
| Versión de BIOS | N/C | | | | | `sudo dmidecode -s bios-version` | `msinfo32` |
| TPM | N/C | | | | | `ls /dev/tpm*` | `Get-Tpm` |
| Gráficos | Intel HD integrada | | | | | `lspci \| grep VGA` | Administrador de dispositivos |
| Periféricos | N/C | | | | | `lsusb` | Administrador de dispositivos |
| Estado general | Funciona | | | | | Inspección y arranque | Inspección y arranque |

## 5. Cableado

| ID | Tipo | Categoría | Conectores | Longitud | Cantidad | Estado | Conecta |
|---|---|---|---|---|---|---|---|
| CAB-01… | Latiguillo (patch cord), gris | Cat5e, 350 MHz | RJ45 con protector moldeado | N/C (medir) | N/C (contar) | | |

- Cat5e admite **1000BASE-T (1 Gb/s) hasta 100 m**, suficiente para todos los puertos RJ45 del material.
- Los puertos SFP+ del switch necesitan módulos o cables DAC, que no están en el material.
- El estado se comprueba con un comprobador de cables o con la velocidad negociada en los puertos (apartados 2 y 3).
- Cada cable se etiqueta en sus dos extremos con su ID (Tasca 3).

## 6. Otros materiales

| ID | Material | Cantidad | Características | Estado |
|---|---|---|---|---|
| OTR-01 | Adaptador de corriente del hEX | N/C | 24 V, 0,38 A | |
| OTR-02 | Cables de alimentación de los PC y del switch | N/C | IEC C13 | |
| OTR-03 | Kit de montaje en rack del switch | N/C | Incluido con el SG3428XMPP | |
| OTR-04 | Teclados, ratones y monitores | N/C | | |
| OTR-05 | Regletas | N/C | | |

## 7. Diagnósticos (línea base)

Con los sistemas operativos instalados, se ejecutan pruebas para conocer el **estado inicial** de cada equipo (línea base) e identificar **cuellos de botella**. La Tasca 7 compara esta línea base con los equipos trabajando. Por cada prueba se registran herramienta, condiciones, resultado e interpretación. Si aparece una avería, se anota la causa probable y una prueba que la distinga de otra causa.

| ID equipo | Recurso | Prueba | Herramienta | Condiciones | Resultado | Interpretación | Evidencia |
|---|---|---|---|---|---|---|---|
| PC-xx | CPU | Modelo y uso en reposo | `lscpu`, `top` / Administrador de tareas | En reposo, sin VM | | | |
| PC-xx | RAM | Total, usada y disponible | `free -h` / Administrador de tareas | En reposo | | | |
| PC-xx | RAM | Test de memoria | MemTest86+ | Arranque desde USB, 1 pasada | | | |
| PC-xx | Disco | Capacidad y espacio libre | `df -h` / Explorador | — | | | |
| PC-xx | Disco | Estado SMART | `smartctl -H -A` / CrystalDiskInfo | — | | | |
| PC-xx | Disco | Lectura y escritura secuencial | `fio` / CrystalDiskMark | Sin otras cargas; anotar si el disco es HDD o SSD | | | |
| PC-xx | Red | Velocidad de enlace | `ethtool` / `Get-NetAdapter` | Conectado al switch | | | |
| PC-xx | Red | Conectividad | `ping` a la puerta de enlace y entre equipos | LAN del grupo | | | |
| PC-xx | Red | Rendimiento | `iperf3` entre dos PC | Mismo switch, sin otro tráfico | | | |
| RT-01 | CPU y RAM | Uso en reposo | `/system resource print` | Sin tráfico, 1 semana encendido | CPU 0 %; 199,8 MiB libres de 256 MiB; 27 °C | Sobra capacidad en reposo para RouterOS | N/C |
| RT-01 | Red | Estado y velocidad de los puertos | `/interface ethernet monitor` | Enlaces conectados | ether1: link-ok, 1 Gb/s full dúplex; ether2 con enlace; ether3–5 sin enlace | Cableado y puerto ether1 correctos a velocidad máxima | N/C |
| RT-01 | Red | Rendimiento de NAT | `iperf3` de un PC de la LAN a uno de la WAN de pruebas | Banco aislado | | | |
| SW-01 | Red | Enlaces y velocidad | `show interface status` | Todos los equipos conectados | | | |

### Cuellos de botella previstos (a confirmar con las pruebas)

| Equipo | Recurso | Por qué puede limitar (dato de la ficha) | Prueba que lo confirma |
|---|---|---|---|
| PC-01 | RAM | 8 GB (7,64 GiB utilizables) en un solo módulo, sin doble canal: Proxmox y cada VM se reparten muy poca memoria; ampliable a 32 GB | `free -h` y tabla de recursos de la Tasca 4 |
| PC-01 | CPU | 2 núcleos / 4 hilos: pocas vCPU para repartir entre las VM de un nodo | `top` o `mpstat` con las VM arrancadas (Tasca 7) |
| PC-01 | Disco | Tipo de disco N/C: si es HDD, las VM y el ZFS de TrueNAS irán lentos | `lsblk -d -o NAME,ROTA` y `fio` |
| PC-01 y PC-02 | Red | Número de tarjetas N/C: si solo hay una, gestión, clúster, almacenamiento y VM compartirían el mismo enlace | `ip -br link` e `iperf3` mientras se migra una VM (Tasca 7) |
| hEX RB750Gr3 | CPU | 880 MHz MIPS: el rendimiento con NAT, reglas de cortafuegos o VPN puede bajar del Gigabit | `iperf3` a través del router |

## 8. Impacto en el diseño y material que falta

Lo que el inventario obliga a revisar de las decisiones de la Tasca 1:

| Hallazgo | Fuente | Decisión afectada | Consecuencia |
|---|---|---|---|
| El i3-4160 de PC-01 (4.ª generación) **no está en la lista de procesadores admitidos por Windows 11** (desde la 8.ª generación); PC-02 podría llevar un i3-8100 (8.ª generación, sí admitido), por confirmar; TPM 2.0 y arranque seguro: N/C | Intel, Microsoft Learn | [D-04 Clientes](../decisions/D-04-clientes.md) | Windows 11 Pro sigue siendo la decisión para la empresa; en el aula solo se puede instalar **sin soporte oficial**, marcado como limitación del laboratorio |
| El hEX es MMIPS (no x86) con 256 MiB de RAM y 16 MiB de flash | MikroTik, medido | [D-01 Comunicaciones](../decisions/D-01-comunicaciones.md) | OPNsense y el GNU/Linux con nftables (x86-64) no pueden ir en el hEX: van en un PC del aula o en una VM de Proxmox |
| PC-01: 8 GB de RAM (ampliable a 32 GB, 3 ranuras libres) y 2 núcleos; VT-x con EPT pero sin VT-d | Intel, `dmidecode` | [D-02 Virtualización](../decisions/D-02-virtualizacion.md) | Proxmox funciona, pero cada nodo admite muy pocas VM a la vez; la Tasca 4 debe reservar memoria para el anfitrión y no puede pasar dispositivos PCI a las VM |
| 8 GB de RAM en PC-01 (y quizá en PC-02): justo el mínimo oficial de TrueNAS | Medido, TrueNAS | [D-05 Datos](../decisions/D-05-datos.md) | TrueNAS cabe solo en un PC dedicado (no en una VM de 8 GB); hay que comprobar los discos disponibles para el espejo |
| El switch tiene VLAN, gestión y PoE de sobra | TP-Link | [D-01 Comunicaciones](../decisions/D-01-comunicaciones.md) | Permite separar redes de gestión, clúster y clientes por VLAN (Tasca 4) |

| Material que falta | Por qué hace falta | Alternativa | Comunicado al profesorado |
|---|---|---|---|
| Discos para el espejo de TrueNAS (cada unidad trae un solo disco de ~500 GB, a confirmar) | Necesidad N3: tolerar la caída de un disco | Discos virtuales en una VM de TrueNAS (simulación identificada) | |
| Segunda tarjeta de red por nodo | Separar el tráfico de clúster y almacenamiento del de las VM | VLAN en la única tarjeta | |
| Módulos SFP+ o cables DAC | Usar los enlaces de 10 Gb del switch | Puertos Gigabit | |
| Segundo módulo DDR3-1600 para PC-01 (la placa admite 32 GB y tiene 3 ranuras libres) | Más VM por nodo y doble canal de memoria | Repartir las VM entre más nodos | |
| SSD SATA para PC-01 | Arranque y VM más rápidos que en el disco actual | Disco actual | |
| Equipos que cumplan los requisitos de Windows 11 | Clientes con soporte oficial | Instalación sin soporte en el aula, marcada como limitación | |

## Fuentes

| Material | URL | Fecha de consulta |
|---|---|---|
| MikroTik hEX (RB750Gr3) – ficha | https://mikrotik.com/product/RB750Gr3 | 19/10/2026 |
| TP-Link Omada SG3428XMPP – ficha | https://www.omadanetworks.com/es/business-networking/omada-switch-access-plus/sg3428xmpp/ | 19/10/2026 |
| Intel Core i3-4160 – especificaciones | https://www.intel.com/content/www/us/en/products/sku/77488/intel-core-i34160-processor-3m-cache-3-60-ghz/specifications.html | 20/10/2026 |
| MikroTik hEX y APD ALDA+ (pc33-t226) – datos medidos en el aula | Salidas de RouterOS, `dmidecode` y del sistema del PC; resumen en [hardware-grupo3](hardware-grupo3.md) | 20/10/2026 |
| Ticnova E70 SFF – ejemplar público del modelo (no es nuestro equipo) | Geekbench | 20/10/2026 |
| Windows 11 – procesadores Intel admitidos | https://learn.microsoft.com/en-us/windows-hardware/design/minimum/supported/windows-11-supported-intel-processors | 19/10/2026 |
| Cable patch Cat5e 350 MHz – referencia | https://www.infinitecables.com/products/molded-boot-custom-rj45-cat5e-350mhz-assembled-patch-cable-grey | 19/10/2026 |

## Uso de IA

La lista de material sale de una conversación con ChatGPT sobre la Tasca 2. Los datos medidos en el aula el 20/10/2026 corrigieron un error: los PC no son Dell OptiPlex 790, sino un APD ALDA+ (Core i3-4160, 8 GB) y un Ticnova E70 SFF. Claude (asistente de IA) completó las características con las fichas oficiales de cada fabricante y detectó el impacto en las decisiones de la Tasca 1 (apartado 8). Las cantidades y los datos de cada unidad se comprueban en el aula con los comandos indicados.
