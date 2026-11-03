# 02 · Inventario y diagnóstico

Tasca 2 «Equips i perifèrics» · RA principales: FDM RA1 a-h y RA4 h-i.

Inventario del material que nos ha dado el profesorado y diagnóstico del estado inicial (línea base) de cada equipo, para poder compararlo más adelante si cambian las condiciones.

- Las características de cada **modelo** salen de su ficha oficial (ver [Fuentes](#fuentes)); los datos de cada **unidad** se comprueban en el equipo con el comando indicado.
- Un dato no comprobado todavía se marca **N/C**.
- Las capturas y salidas de comandos van saneadas a [`evidencies/`](../evidencies/) con el nombre `t2-<descripcion>-<fecha>`.

## 1. Resumen del material

| ID | Tipo | Fabricante y modelo | Características principales | Cantidad | Estado |
|---|---|---|---|---|---|
| RT-01 | Router | MikroTik hEX (RB750Gr3), revisión r4 | MMIPS 880 MHz, 256 MiB de RAM, 16 MiB de flash, 5 puertos Gigabit | 1 | Funciona |
| SW-01 | Switch no gestionable | TP-Link TL-SG116 | 16 puertos Gigabit, sin VLAN ni gestión | 1 | Funciona |
| SW-02… | Switch no gestionable | TP-Link LS1008G | 8 puertos Gigabit, sin VLAN ni gestión | 2 | Funciona |
| PC-01 | Ordenador de sobremesa | APD ALDA+ | Core i3-4160, 8 GB DDR3, HDD de 500 GB | 1 | Funciona |
| PC-02 | Ordenador SFF | Ticnova E70 SFF | Core i3-8100, 8 GB DDR4, SSD de 256 GB | 1 | Funciona |
| CAB | Latiguillos de red | RJ45 Cat6 | Hasta 1 Gb/s | 12 | Funciona |
| PER | Periféricos | — | 1 monitor, 1 teclado y 1 ratón, compartidos por los dos PC | 1 de cada | Funciona |

Estado: *Funciona* · *Funciona con incidencias* · *Averiado* · *Pendiente de probar*.

## 2. Ordenadores

Disponemos de dos PCs extra para el proyecto. Resumen en [hardware-grupo3](hardware-grupo3.md).

| Dato | PC-01: APD ALDA+ (hostname `PCG3`) | PC-02: Ticnova E70 SFF | Comprobación |
|---|---|---|---|
| Uso | Cliente Lubuntu con VirtualBox | Servidor TrueNAS | Diseño |
| Formato | Sobremesa horizontal | SFF, en vertical | Inspección |
| Placa base | Gigabyte B85M-DS3H-A (Intel B85, LGA1150) | ASRock H310CM-HDV (Intel H310, LGA1151 v2) | `sudo dmidecode -t baseboard` |
| Número de serie | N/C | 1720408949 | `sudo dmidecode -s system-serial-number` |
| CPU | Intel Core i3-4160 (Haswell): 2 núcleos / 4 hilos, 3,6 GHz | Intel Core i3-8100 (Coffee Lake): 4 núcleos / 4 hilos, 3,6 GHz | `lscpu` y ficha de Intel |
| Virtualización | VT-x con EPT; sin VT-d | VT-x con EPT y VT-d | Ficha de Intel |
| RAM | 8 GB DDR3-1600, 1 módulo (ChannelB-DIMM1) | 8 GB DDR4-2400, 1 módulo Hynix (DDR4_B1) | `free -h`, `sudo dmidecode -t memory` |
| Ampliación de RAM | 3 de 4 ranuras libres; máximo 32 GB | 1 de 2 ranuras libre; máximo 32 GB | `sudo dmidecode -t memory` |
| Discos | HDD Toshiba DT01ACA050, 500 GB, 7200 rpm, SATA 6 Gb/s | `sda`: SSD Silicon Power (SPCC) 256 GB (SATA3_0), disco de arranque de TrueNAS (`boot-pool`). `sdb`: HDD Seagate IronWolf Pro ST2000NE0025, 2 TB, 7200 rpm, SATA 6 Gb/s, n.º de serie ZDS16MKR; trae un sistema de ficheros ext4 de antes y aún no está en ningún pool. Quedan 2 puertos SATA libres | `lsblk -o NAME,SIZE,MODEL,ROTA,FSTYPE`, `smartctl -i` |
| Estado de los discos | Ver diagnóstico (apartado 5) | Ver diagnóstico (apartado 5) | `sudo smartctl -H /dev/sdX` |
| Unidad óptica | Regrabadora DVD ATAPI iHAS124 F | No | `lsblk -d` |
| Interfaces de red | `enp2s0` integrada (MAC 40:8d:5c:27:fe:73) + adaptador USB `enxb44bd6c64adc` (MAC b4:4b:d6:c6:4a:dc) | Realtek Gigabit integrada con PXE, `enp1s0`, a 1000 Mb/s full dúplex (MAC a8:a1:59:02:b6:95, deducida de su IPv6 de enlace local) | `ip -br a`, `ethtool enp1s0` |
| Firmware y arranque | UEFI (comprobado); arranca desde el HDD | BIOS P4.20P, UEFI; arranque por red (PXE) disponible. Fecha corregida a 21/10/2026: vigilar la pila CR2032 | Configuración de la BIOS |
| Gráficos | Intel HD Graphics 4400 integrada | Intel UHD Graphics 630 integrada | `lspci \| grep VGA` |
| Periféricos | Monitor, teclado y ratón compartidos | Ninguno: se administra por la web de TrueNAS | Inspección |
| Alimentación | Fuente de ~300 W (estimada para el formato); consumo real ~30–65 W | Fuente de ~180–300 W (estimada para un SFF); consumo real ~30–65 W | Etiqueta de la fuente |
| Sistema operativo (configuración inicial) | Lubuntu (versión N/C) con VirtualBox; usuario `admin`; IP fija 192.168.30.15/24 (ver [04-disseny](04-disseny.md)) | TrueNAS 25.10.7 «Goldeye»; IP fija 192.168.30.20/24 (ver [04-disseny](04-disseny.md)) | `cat /etc/os-release`, `ip -br a`, web de TrueNAS |

## 3. Router y switches

Los puertos, las conexiones y la configuración de red se documentan en la Tasca 3 (rack e interconexión).

**Router MikroTik hEX (RB750Gr3), n.º de serie HMF0B3M3EK8**

| Dato | Valor |
|---|---|
| CPU | MediaTek MT7621 (MIPS 1004Kc), 880 MHz; arquitectura MMIPS (no x86) |
| RAM y almacenamiento | 256 MiB · 16 MiB de flash |
| Interfaces de red | 5 × Gigabit (ether1–ether5), MAC D0:EA:11:A0:2A:03 a :07 |
| Firmware | RouterOS 7.20.8 long-term, RouterBOOT 7.20.8 (al día); licencia nivel 4 |
| Alimentación | Adaptador de 24 V y 0,38 A, o PoE pasivo en ether1; máximo 10 W |
| Formato | Sobremesa, 113 × 89 × 28 mm |

**Switches TP-Link** (no gestionables: sin VLAN, sin IP de gestión y sin firmware configurable)

| Dato | TL-SG116 | LS1008G |
|---|---|---|
| Puertos | 16 × Gigabit RJ45 | 8 × Gigabit RJ45 |
| Alimentación | Adaptador 12 V DC / 1 A; máximo 10 W | Adaptador externo; máximo 3,9 W |
| Formato | Sobremesa, carcasa metálica, sin ventilador | Sobremesa, carcasa de plástico, sin ventilador |

## 4. Cableado

| ID | Tipo | Categoría | Conectores | Longitud | Cantidad | Estado |
|---|---|---|---|---|---|---|
| CAB-01… | Latiguillo (patch cord), gris | Cat6, 250 MHz | RJ45 | 80 m | 12 | Funciona |

Cat6 admite 1000BASE-T (1 Gb/s) hasta 100 m, suficiente para todos los puertos del material. El etiquetado y qué conecta cada cable se documentan en la Tasca 3.

## 5. Diagnósticos (línea base)

Con los sistemas operativos instalados y los equipos en reposo, se mide su **estado inicial**; la Tasca 7 lo compara con los equipos trabajando.

> Cuando hagáis los diagnósticos de memoria, disco y red de los PC, escribid aquí cómo y qué habéis hecho: herramienta, condiciones, resultado e interpretación. Si sale una avería, la causa probable y una prueba que la distinga de otra causa.

| Equipo | Recurso | Prueba | Herramienta | Condiciones | Resultado | Interpretación | Evidencia |
|---|---|---|---|---|---|---|---|
| RT-01 | Memoria y CPU | Uso en reposo | `/system resource print` | Sin tráfico, 1 semana encendido | CPU 0 %; 199,8 MiB libres de 256 MiB; 27 °C | Sobra capacidad en reposo para RouterOS | |
| RT-01 | Red | Estado de los puertos | `/interface ethernet monitor` | Enlaces conectados | ether1 a 1 Gb/s full dúplex; ether2 con enlace | Cableado y ether1 correctos a velocidad máxima | |
| PC-02 | Sistema | Versión y tiempo encendido | `cat /etc/version`, `uname -r`, `uptime` | 03/11/2026, Shell de TrueNAS; recién arrancado (3 min), sin clientes ni pools de datos | TrueNAS 25.10.7, kernel 6.12.105; carga 0,12 / 0,21 / 0,09 | En reposo: una carga de 0,12 con 4 núcleos es un ~3 % de uso | [salida](../evidencies/t2-diagnostico-pc02-truenas-20261103.txt) |
| PC-02 | CPU | Rendimiento con los 4 núcleos | `openssl speed -multi 4 -seconds 3 sha256` | Mismas condiciones | ~1,86 GB/s (≈ 1770 MiB/s) con bloques de 16 KB | La CPU calcula sumas de verificación (lo que hace ZFS con cada bloque) mucho más rápido de lo que llega por la red: no limita | ídem |
| PC-02 | Memoria | Total, usada y disponible; caché ZFS (ARC) | `free -h`, `arc_summary` | Mismas condiciones | 7,4 GiB visibles (de 8 GB), 1,4 GiB usados, 6,0 GiB disponibles, sin swap. ARC: 282 MiB ahora, puede crecer hasta 6,4 GiB | En reposo sobra memoria. El ARC es pequeño porque aún no hay datos; cuando los haya, TrueNAS usará casi toda la RAM libre como caché de lectura | ídem |
| PC-02 | Disco `sda` (SSD) | Estado SMART | `sudo smartctl -H -A /dev/sda` | Mismas condiciones | PASSED; 99 % de vida; 3610 h encendido; 1354 GiB escritos; 0 errores CRC; 33 °C; **109 apagados bruscos** | SSD sano. Los apagados bruscos indican que el equipo se ha apagado sin cerrar el sistema: hay que apagar TrueNAS desde la web | ídem |
| PC-02 | Disco `sda` (SSD) | Lectura secuencial y aleatoria | `fio --readonly`: bloques de 1 MB y de 4 KB, 20 s cada una | Mismas condiciones | Secuencial **466 MiB/s** (489 MB/s). Aleatoria 4K: 63.800 IOPS (249 MiB/s) | Normal para un SSD SATA III (el máximo del SATA es de unos 550 MB/s) | ídem |
| PC-02 | Disco `sdb` (HDD) | Estado SMART | `sudo smartctl -i -H -A /dev/sdb` | Mismas condiciones | PASSED; 0 sectores reasignados, pendientes e incorregibles; 0 errores CRC; 29 °C; enlace SATA a 6 Gb/s; **41.623 h encendido (unos 4,7 años)** | Disco sano, pero con muchas horas de uso. `Raw_Read_Error_Rate` y `Seek_Error_Rate` tienen números en bruto grandes porque Seagate los codifica así; lo que cuenta es VALUE (64 y 81), por encima del umbral THRESH (44 y 45) | ídem |
| PC-02 | Disco `sdb` (HDD) | Lectura secuencial y aleatoria | `fio --readonly`: bloques de 1 MB y de 4 KB, 20 s cada una | Mismas condiciones | Secuencial **189 MiB/s** (198 MB/s). Aleatoria 4K: **179 IOPS (0,7 MiB/s)** | Normal para un HDD de 7200 rpm: va bien leyendo seguido, pero en cada lectura aleatoria el cabezal tiene que moverse (unos 5 ms), y por eso solo hace unas 180 por segundo | ídem |
| PC-02 | Red | Velocidad del enlace | `sudo ethtool enp1s0` | Mismas condiciones | 1000 Mb/s, full dúplex, enlace detectado | La tarjeta ha negociado el máximo con el switch | ídem |
| PC-01 → PC-02 | Red | Latencia y pérdida | `ping -c 10 192.168.30.20` desde PC-01 | 27/10/2026; los dos PC en el mismo switch, sin otro tráfico; PC-01 aún con IP .200 por DHCP | 10 enviados, 10 recibidos (0 % de pérdida); media 0,150 ms (mín. 0,138, máx. 0,169, mdev 0,011) | Conexión sin pérdidas y casi instantánea (en el mismo switch, por debajo de 1 ms es bueno); casi no varía, así que el switch no se satura | [ping](../evidencies/t2-ping-pc01-truenas-20261027.txt) |
| PC-01 ↔ PC-02 | Red | Velocidad real de transferencia | `iperf3 -s` en TrueNAS (System → Shell); `iperf3 -c 192.168.30.20` y `iperf3 -c 192.168.30.20 -R` en PC-01 | Mismas condiciones; 10 s por sentido. La primera prueba se cortó a los 3 s y se descarta | PC-01 → NAS: 941 Mbit/s. NAS → PC-01: 942 Mbit/s, 0 retransmisiones. Estable entre 926 y 955 Mbit/s | Es el máximo real de una red Gigabit: el ~6 % que falta hasta 1000 son las cabeceras de Ethernet, IP y TCP. 941 Mbit/s ÷ 8 ≈ 117 MB/s, que son los **112 MiB/s** («112 MBytes» por segundo) que muestra iperf3. Igual en los dos sentidos y 0 retransmisiones: cables, puertos y switch en buen estado | [iperf3](../evidencies/t2-iperf3-pc01-truenas-20261027.txt) |

## 6. Cuellos de botella

| Equipo | Recurso | Por qué limita | Prueba que lo confirma | Mejora posible |
|---|---|---|---|---|
| PC-02 (TrueNAS) | Red | Con ficheros grandes (copias de seguridad, imágenes ISO, vídeos) es la pieza más lenta: no puede pasar de **112 MiB/s**, aunque el HDD lee a 189 y el SSD a 466. Además, esos 112 MiB/s los **comparten** todos los equipos que usan el NAS a la vez: con 6 portátiles copiando a la vez tocaría a unos 19 MiB/s cada uno | `iperf3` (112 MiB/s) frente a `fio` secuencial de los discos (189 y 466 MiB/s) y CPU (~1770 MiB/s) | Tarjeta de 2,5 o 10 Gb/s, que también exige un switch de esa velocidad (los TP-Link son de 1 Gb/s). Una segunda tarjeta Gigabit solo ayuda si separa tráfico (por ejemplo, una red solo para las copias de Proxmox): con switches no gestionables no se pueden unir dos enlaces (LACP) |
| PC-02 (TrueNAS) | Disco `sdb` (HDD) | Con muchos ficheros pequeños o accesos aleatorios (varias personas a la vez, carpetas con miles de documentos, discos de VM) solo hace **179 lecturas por segundo (0,7 MiB/s)**, muy por debajo de la red. En ese caso el disco es el cuello de botella, no la red | `fio` aleatorio 4K: 179 IOPS en el HDD frente a 63.800 en el SSD | Más RAM, para que el ARC guarde lo más usado y no haya que ir al disco; un segundo disco en espejo, que reparte las lecturas entre los dos; o un SSD para los datos que más se usen |

**Cómo lo hemos buscado.** Cuando un cliente lee un fichero del NAS, los datos hacen el camino **disco → memoria/CPU → red → cliente**. El cuello de botella es la pieza más lenta de ese camino: frena a las demás aunque vayan sobradas. Por eso hemos medido cada pieza por separado con una herramienta adecuada y lo hemos pasado todo a la misma unidad (MiB/s) para compararlo:

| Pieza | Herramienta | Ficheros grandes (secuencial) | Ficheros pequeños (aleatorio 4K) |
|---|---|---|---|
| CPU | `openssl speed` | ~1770 MiB/s | — |
| SSD `sda` (sistema) | `fio` | 466 MiB/s | 249 MiB/s (63.800 IOPS) |
| HDD `sdb` (datos) | `fio` | 189 MiB/s | **0,7 MiB/s (179 IOPS)** |
| Red | `iperf3` | **112 MiB/s** | — |

- **Ficheros grandes:** la red (112) es la más lenta, así que manda la red. Cambiar el disco por otro más rápido no se notaría en los clientes.
- **Ficheros pequeños:** el HDD baja a 0,7 MiB/s, así que manda el disco, y una red más rápida no serviría de nada.
- **El ping no sirve para esto:** mide cuánto **tarda** un paquete, no cuántos **datos** pasan por segundo. Ha servido para descartar pérdidas y retardos.
- **La RAM no limita en reposo** (6,0 GiB disponibles), pero de ella depende el ARC. Con datos reales, la Tasca 7 comprobará si los 8 GB se quedan cortos.
- **No hemos encontrado averías**: los dos discos dan PASSED, sin sectores dañados ni errores de cable. Sí hay dos riesgos: el HDD lleva unos 4,7 años encendido y es un único disco sin espejo, y el SSD acumula 109 apagados bruscos.

> Cuando midáis el PC-01 (`free -h`, `fio`, `ethtool`), escribid aquí cómo y qué habéis hecho y si encontráis otro cuello de botella.

## Fuentes

| Material | URL o fuente | Fecha de consulta |
|---|---|---|
| MikroTik hEX (RB750Gr3) – ficha | https://mikrotik.com/product/RB750Gr3 | 19/10/2026 |
| TP-Link TL-SG116 – ficha | https://www.tp-link.com/es/business-networking/unmanaged-switch/tl-sg116/ | 21/10/2026 |
| TP-Link LS1008G – ficha | https://www.tp-link.com/es/home-networking/soho-switch/ls1008g/ | 21/10/2026 |
| Intel Core i3-4160 – especificaciones | https://www.intel.com/content/www/us/en/products/sku/77488/intel-core-i34160-processor-3m-cache-3-60-ghz/specifications.html | 20/10/2026 |
| Intel Core i3-8100 – especificaciones | https://www.intel.com/content/www/us/en/products/sku/126688/intel-core-i38100-processor-6m-cache-3-60-ghz/specifications.html | 21/10/2026 |
| Windows 11 – procesadores Intel admitidos | https://learn.microsoft.com/en-us/windows-hardware/design/minimum/supported/windows-11-supported-intel-processors | 19/10/2026 |
| Datos medidos en el aula | Salidas de RouterOS, `dmidecode`, `lsblk`, `ip` y BIOS de los equipos | 20–21/10/2026 |
| Diagnóstico de red entre PC-01 y PC-02 | Salidas de `ping` e `iperf3` (ver [`evidencies/`](../evidencies/)) | 27/10/2026 |
| Línea base del PC-02 | Salidas de `lscpu`, `openssl`, `free`, `arc_summary`, `smartctl`, `fio` y `ethtool` en TrueNAS (ver [`evidencies/`](../evidencies/)) | 03/11/2026 |

## Uso de IA

La lista de material sale de una conversación con ChatGPT sobre la Tasca 2. Los datos medidos en el aula corrigieron dos errores: los PC no eran Dell OptiPlex 790 y el switch no era un SG3428XMPP gestionable. Claude (asistente de IA) completó las características con las fichas oficiales de cada fabricante y redujo el documento a lo que pide el enunciado. Las potencias de las fuentes de alimentación son estimaciones.

En los diagnósticos (27/10 y 03/11), Claude nos indicó las órdenes y nos ayudó a interpretar las salidas. Las pruebas las hicimos nosotros en el aula. La primera prueba de `iperf3` la descartamos porque se cortó a los 3 segundos.
