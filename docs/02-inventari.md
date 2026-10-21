# 02 · Inventario y diagnóstico

Tasca 2 · RA principales: FDM RA1 a-h y RA4 h-i.

Inventario del material que nos ha dado el profesorado y diagnóstico del estado inicial (línea base) de cada equipo, para poder compararlo más adelante si cambian las condiciones.

- Las características de cada **modelo** salen de su ficha oficial (ver [Fuentes](#fuentes)); los datos de cada **unidad** se comprueban en el equipo con el comando indicado.
- Un dato no comprobado todavía se marca **N/C**.
- Las capturas y salidas de comandos van saneadas a [`evidencies/`](../evidencies/) con el nombre `t2-<descripcion>-<fecha>`.

## 1. Resumen del material

| ID | Tipo | Fabricante y modelo | Características principales | Cantidad | Estado |
|---|---|---|---|---|---|
| RT-01 | Router | MikroTik hEX (RB750Gr3), revisión r4 | MMIPS 880 MHz, 256 MiB de RAM, 16 MiB de flash, 5 puertos Gigabit | N/C (Tasca 3) | Funciona |
| SW-01 | Switch no gestionable | TP-Link TL-SG116 | 16 puertos Gigabit, sin VLAN ni gestión | 1 | Pendiente de probar |
| SW-02… | Switch no gestionable | TP-Link LS1008G | 8 puertos Gigabit, sin VLAN ni gestión | N/C (Tasca 3) | Pendiente de probar |
| PC-01 | Ordenador de sobremesa | APD ALDA+ | Core i3-4160, 8 GB DDR3, HDD de 500 GB | 1 | Funciona |
| PC-02 | Ordenador SFF | Ticnova E70 SFF | Core i3-8100, 8 GB DDR4, SSD de 256 GB | 1 | Funciona |
| CAB | Latiguillos de red | RJ45 Cat5e | Hasta 1 Gb/s | N/C (Tasca 3) | Pendiente de probar |
| PER | Periféricos | — | 1 monitor, 1 teclado y 1 ratón, compartidos por los dos PC | 1 de cada | Funciona |

Estado: *Funciona* · *Funciona con incidencias* · *Averiado* · *Pendiente de probar*.

## 2. Ordenadores

Disponemos de dos PC para el proyecto. Resumen en [hardware-grupo3](hardware-grupo3.md).

| Dato | PC-01: APD ALDA+ (hostname `PCG3`) | PC-02: Ticnova E70 SFF | Comprobación |
|---|---|---|---|
| Uso | Cliente Lubuntu con VirtualBox | Servidor TrueNAS (previsto) | Diseño |
| Formato | Sobremesa horizontal | SFF, en vertical | Inspección |
| Placa base | Gigabyte B85M-DS3H-A (Intel B85, LGA1150) | ASRock H310CM-HDV (Intel H310, LGA1151 v2) | `sudo dmidecode -t baseboard` |
| Número de serie | N/C | 1720408949 | `sudo dmidecode -s system-serial-number` |
| CPU | Intel Core i3-4160 (Haswell): 2 núcleos / 4 hilos, 3,6 GHz | Intel Core i3-8100 (Coffee Lake): 4 núcleos / 4 hilos, 3,6 GHz | `lscpu` y ficha de Intel |
| Virtualización | VT-x con EPT; sin VT-d | VT-x con EPT y VT-d | Ficha de Intel |
| RAM | 8 GB DDR3-1600, 1 módulo (ChannelB-DIMM1) | 8 GB DDR4-2400, 1 módulo Hynix (DDR4_B1) | `free -h`, `sudo dmidecode -t memory` |
| Ampliación de RAM | 3 de 4 ranuras libres; máximo 32 GB | 1 de 2 ranuras libre; máximo 32 GB | `sudo dmidecode -t memory` |
| Discos | HDD Toshiba DT01ACA050, 500 GB, 7200 rpm, SATA 6 Gb/s | SSD Silicon Power 256 GB (SATA3_0); SATA3_1–3 libres | `lsblk -d -o NAME,MODEL,SIZE,ROTA` |
| Estado de los discos | Ver diagnóstico (apartado 5) | Ver diagnóstico (apartado 5) | `sudo smartctl -H /dev/sdX` |
| Unidad óptica | Regrabadora DVD ATAPI iHAS124 F | No | `lsblk -d` |
| Interfaces de red | `enp2s0` integrada (MAC 40:8d:5c:27:fe:73) + adaptador USB `enxb44bd6c64adc` (MAC b4:4b:d6:c6:4a:dc) | Realtek Gigabit integrada con PXE (MAC N/C) | `ip -br link` |
| Firmware y arranque | UEFI (comprobado); arranca desde el HDD | BIOS P4.20P, UEFI; arranque por red (PXE) disponible. Fecha corregida a 21/10/2026: vigilar la pila CR2032 | Configuración de la BIOS |
| Gráficos | Intel HD Graphics 4400 integrada | Intel UHD Graphics 630 integrada | `lspci \| grep VGA` |
| Periféricos | Monitor, teclado y ratón compartidos | Ninguno: se administra por la web de TrueNAS | Inspección |
| Alimentación | Fuente de ~300 W (estimada para el formato); consumo real ~30–65 W | Fuente de ~180–300 W (estimada para un SFF); consumo real ~30–65 W | Etiqueta de la fuente |
| Sistema operativo (configuración inicial) | Lubuntu (versión N/C) con VirtualBox; usuario `admin`; IP pendiente | TrueNAS (previsto); IP pendiente | `cat /etc/os-release`, `ip -br a` |

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
| CAB-01… | Latiguillo (patch cord), gris | Cat6, 250 MHz | RJ45 | 80 m | 12 | |

Cat6 admite 1000BASE-T (1 Gb/s) hasta 100 m, suficiente para todos los puertos del material. El etiquetado y qué conecta cada cable se documentan en la Tasca 3.

## 5. Diagnósticos (línea base)

Con los sistemas operativos instalados y los equipos en reposo, se mide su **estado inicial**; la Tasca 7 lo compara con los equipos trabajando. Si aparece una avería, se anota la causa probable y una prueba que la distinga de otra causa.

| Equipo | Recurso | Prueba | Herramienta | Condiciones | Resultado | Interpretación | Evidencia |
|---|---|---|---|---|---|---|---|
| PC-01 | Memoria | Total, usada y disponible | `free -h` | En reposo, sin VM | | | |
| PC-01 | Memoria | Test de memoria | MemTest86+ | Arranque desde USB, 1 pasada | | | |
| PC-01 | Disco | Estado SMART | `sudo smartctl -H -A /dev/sda` | — | | | |
| PC-01 | Disco | Lectura y escritura | `fio` | Sin otras cargas | | | |
| PC-01 | Red | Velocidad del enlace | `ethtool enp2s0` | Conectado al switch | | | |
| PC-01 | Red | Conectividad y rendimiento | `ping` e `iperf3` con PC-02 | Mismo switch, sin otro tráfico | | | |
| PC-02 | Memoria | Total, usada y disponible | `free -h` (shell de TrueNAS) | En reposo | | | |
| PC-02 | Memoria | Test de memoria | MemTest86+ | Arranque desde USB, 1 pasada | | | |
| PC-02 | Disco | Estado SMART | `smartctl -H -A` (TrueNAS) | — | | | |
| PC-02 | Disco | Lectura y escritura | `fio` | Sin otras cargas | | | |
| PC-02 | Red | Velocidad del enlace | `ethtool` | Conectado al switch | | | |
| PC-02 | Red | Conectividad y rendimiento | `ping` e `iperf3` con PC-01 | Mismo switch, sin otro tráfico | | | |
| RT-01 | Memoria y CPU | Uso en reposo | `/system resource print` | Sin tráfico, 1 semana encendido | CPU 0 %; 199,8 MiB libres de 256 MiB; 27 °C | Sobra capacidad en reposo para RouterOS | |
| RT-01 | Red | Estado de los puertos | `/interface ethernet monitor` | Enlaces conectados | ether1 a 1 Gb/s full dúplex; ether2 con enlace | Cableado y ether1 correctos a velocidad máxima | |

## 6. Cuellos de botella previstos

A confirmar con los diagnósticos.

| Equipo | Recurso | Por qué puede limitar | Prueba que lo confirma | Mejora posible |
|---|---|---|---|---|
| PC-01 | Disco | HDD de 7200 rpm: arranque, aplicaciones y VM de VirtualBox lentos con accesos aleatorios | `fio` (aleatorio 4K) | SSD SATA |
| PC-01 | RAM | 8 GB en un solo módulo, sin doble canal: poca memoria para las VM | `free -h` con las VM arrancadas | Segundo módulo DDR3-1600 |
| PC-01 | CPU | 2 núcleos / 4 hilos: pocas vCPU para repartir entre VM | `top` con las VM arrancadas | Limitar las VM simultáneas |
| PC-02 | RAM | 8 GB es el mínimo de TrueNAS; ZFS usa la RAM libre como caché | `free -h` y ARC de TrueNAS | Segundo módulo DDR4-2400 |
| PC-02 | Disco | Un solo disco, que TrueNAS usa entero para arrancar: no queda disco para datos ni para el espejo | `lsblk` | Dos discos en los SATA libres |
| PC-02 | Red | Una sola tarjeta Gigabit para la gestión y los datos | `iperf3` y copia de ficheros al NAS | Segunda tarjeta de red |
| RT-01 | CPU | 880 MHz MIPS: con NAT, cortafuegos o VPN puede no llegar al Gigabit | `iperf3` a través del router | Filtrado en un equipo x86 |

## 7. Impacto en las decisiones

| Hallazgo | Decisión afectada | Consecuencia |
|---|---|---|
| El i3-4160 de PC-01 no está admitido por Windows 11 (PC-02, con i3-8100, sí) | [D-04 Clientes](../decisions/D-04-clientes.md) | En el aula, Windows 11 solo se podría instalar sin soporte oficial |
| El hEX es MMIPS (no x86) con 256 MiB de RAM | [D-01 Comunicaciones](../decisions/D-01-comunicaciones.md) | OPNsense y nftables no pueden ir en el hEX: van en un PC o en una VM |
| PC-01: 8 GB de RAM y 2 núcleos | [D-02 Virtualización](../decisions/D-02-virtualizacion.md) | Pocas VM a la vez |
| PC-02: 8 GB de RAM y un solo disco | [D-05 Datos](../decisions/D-05-datos.md) | TrueNAS en PC-02 dedicado; faltan discos para el espejo |
| Los switches no son gestionables | [D-01 Comunicaciones](../decisions/D-01-comunicaciones.md) | Sin VLAN en el switch: separación de redes por segmentos físicos o en el hEX |

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

## Uso de IA

La lista de material sale de una conversación con ChatGPT sobre la Tasca 2. Los datos medidos en el aula corrigieron dos errores: los PC no eran Dell OptiPlex 790 y el switch no era un SG3428XMPP gestionable. Claude (asistente de IA) completó las características con las fichas oficiales de cada fabricante, detectó el impacto en las decisiones de la Tasca 1 y redujo el documento a lo que pide el enunciado. Las potencias de las fuentes de alimentación son estimaciones.
