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
| RT-01 | Router | MikroTik hEX (RB750Gr3) | CPU MT7621A (2 núcleos, 880 MHz, MMIPS), 256 MB de RAM, 5 puertos Gigabit, RouterOS licencia nivel 4 | N/C (contar) | Pendiente de probar |
| SW-01 | Switch gestionable | TP-Link Omada SG3428XMPP | 24 puertos Gigabit PoE+/PoE++ (500 W) + 4 SFP+ de 10 Gb, 4K VLAN, gestión web/CLI/SNMP, rack 1U | N/C (contar) | Pendiente de probar |
| PC-01… | Ordenador de sobremesa | Dell OptiPlex 790 (minitorre) | Intel Core i5-2400 (4 núcleos, 3,1–3,4 GHz, VT-x con EPT), DDR3 hasta 16 GB, red Intel 82579LM Gigabit, TPM 1.2, fuente de 265 W | N/C (contar) | Pendiente de probar |
| CAB | Latiguillos de red | Cable patch RJ45 Cat5e, 350 MHz, conector moldeado, gris | Hasta 1 Gb/s (1000BASE-T) | N/C (contar) | Pendiente de probar |
| OTR | Otros materiales | — | Adaptador de corriente del hEX (24 V, 0,38 A), cables de alimentación y kit de rack del switch | N/C (contar) | Pendiente de probar |

Estado: *Funciona* · *Funciona con incidencias* · *Averiado* · *Pendiente de probar*.

## 2. Router: MikroTik hEX (RB750Gr3)

### Ficha del modelo

| Dato | Valor (ficha oficial) |
|---|---|
| Código de producto | RB750Gr3 (hEX) |
| Procesador | MediaTek MT7621A, 2 núcleos / 4 hilos, 880 MHz |
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
| Número de serie | | `/system routerboard print` |
| Modelo detectado | | `/system routerboard print` |
| Versión de RouterOS | | `/system resource print` |
| Canal de actualización | | `/system package update print` |
| Firmware RouterBOOT (actual / disponible) | | `/system routerboard print` |
| RAM libre en reposo | | `/system resource print` |
| Uso de CPU en reposo | | `/system resource print` |
| Temperatura y voltaje | | `/system health print` |
| Licencia | | `/system license print` |

### Puertos

| Puerto | Velocidad máxima | Velocidad negociada | Conectado a | Estado del enlace |
|---|---|---|---|---|
| ether1 (PoE-in) | 1 Gb/s | | | |
| ether2 | 1 Gb/s | | | |
| ether3 | 1 Gb/s | | | |
| ether4 | 1 Gb/s | | | |
| ether5 | 1 Gb/s | | | |

Comandos: `/interface ethernet print detail`, `/interface ethernet monitor ether1`.

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

## 4. Ordenadores: Dell OptiPlex 790 (minitorre)

### Ficha del modelo

| Dato | Valor (ficha oficial) |
|---|---|
| Formato | Minitorre |
| Procesador | Intel Core i5-2400 (2.ª generación, «Sandy Bridge», 2011): 4 núcleos / 4 hilos, 3,10 GHz (turbo 3,40 GHz), 6 MB de caché, 95 W |
| Virtualización | Intel VT-x, VT-x con EPT (SLAT) y VT-d |
| Chipset | Intel Q65 (serie 6 Express) |
| Memoria | 4 ranuras DDR3 1333 MHz sin ECC; **máximo 16 GB** según Dell (4 × 4 GB) |
| Gráficos | Intel HD Graphics 2000 integrada (VGA y DisplayPort) |
| Almacenamiento | 3 puertos SATA 2.0 (3 Gb/s) + 1 SATA 3.0 (6 Gb/s); 2 bahías de 3,5" y 2 bahías de 5,25" |
| Red | Intel 82579LM Gigabit Ethernet (10/100/1000) |
| Seguridad | TPM 1.2 integrado en placa (no admite TPM 2.0) |
| USB | USB 2.0 (sin USB 3.0) |
| Ranuras de expansión | 4 |
| Fuente de alimentación | 265 W, 100–240 V |
| Configuración de referencia | SSD de 256 GB, Windows 10 Pro, grabadora DVD; RAM de 8 o 16 GB según la unidad |

### Datos de cada unidad

Una columna por equipo; se añaden columnas si hay más.

| Dato | PC-01 | PC-02 | PC-03 | PC-04 | PC-05 | Comando Linux | Comando Windows |
|---|---|---|---|---|---|---|---|
| Etiqueta de servicio (Service Tag) | | | | | | `sudo dmidecode -s system-serial-number` | `Get-CimInstance Win32_BIOS` |
| Uso previsto | | | | | | Diseño (Tasca 4) | Diseño (Tasca 4) |
| CPU detectada | | | | | | `lscpu` | `Get-CimInstance Win32_Processor` |
| VT-x activado en BIOS | | | | | | `grep -c vmx /proc/cpuinfo` | `systeminfo` |
| RAM instalada | | | | | | `free -h` | `Get-CimInstance Win32_PhysicalMemory` |
| Módulos y ranuras libres | | | | | | `sudo dmidecode -t memory` | `Get-CimInstance Win32_PhysicalMemory` |
| Discos (modelo, tipo y capacidad) | | | | | | `lsblk -d -o NAME,MODEL,SIZE,ROTA,TRAN` | `Get-PhysicalDisk` |
| Estado SMART | | | | | | `sudo smartctl -H -A /dev/sdX` | CrystalDiskInfo |
| Sistema operativo instalado | | | | | | `cat /etc/os-release` | `winver` |
| Velocidad de la tarjeta de red | | | | | | `ethtool <interfaz>` | `Get-NetAdapter` |
| Versión de BIOS | | | | | | `sudo dmidecode -s bios-version` | `msinfo32` |
| TPM activado (1.2) | | | | | | `ls /dev/tpm*` | `Get-Tpm` |
| Unidad óptica | | | | | | `lsblk` | Explorador |
| Periféricos | | | | | | `lsusb` | Administrador de dispositivos |
| Estado general | | | | | | Inspección y arranque | Inspección y arranque |

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
| PC-xx | Disco | Lectura y escritura secuencial | `fio` / CrystalDiskMark | Sin otras cargas; anotar si el disco va a SATA 2.0 o 3.0 | | | |
| PC-xx | Red | Velocidad de enlace | `ethtool` / `Get-NetAdapter` | Conectado al switch | | | |
| PC-xx | Red | Conectividad | `ping` a la puerta de enlace y entre equipos | LAN del grupo | | | |
| PC-xx | Red | Rendimiento | `iperf3` entre dos PC | Mismo switch, sin otro tráfico | | | |
| RT-01 | CPU y RAM | Uso en reposo | `/system resource print` | Sin tráfico | | | |
| RT-01 | Red | Estado y velocidad de los puertos | `/interface ethernet monitor` | Enlaces conectados | | | |
| RT-01 | Red | Rendimiento de NAT | `iperf3` de un PC de la LAN a uno de la WAN de pruebas | Banco aislado | | | |
| SW-01 | Red | Enlaces y velocidad | `show interface status` | Todos los equipos conectados | | | |

### Cuellos de botella previstos (a confirmar con las pruebas)

| Equipo | Recurso | Por qué puede limitar (dato de la ficha) | Prueba que lo confirma |
|---|---|---|---|
| OptiPlex 790 | RAM | Máximo 16 GB DDR3 por equipo: limita cuántas VM caben en cada nodo de Proxmox | `free -h` y tabla de recursos de la Tasca 4 |
| OptiPlex 790 | Disco | 3 de los 4 puertos SATA son 2.0 (3 Gb/s): un SSD conectado a ellos rinde por debajo de su capacidad | `fio` en el puerto SATA 3.0 y en uno SATA 2.0 |
| OptiPlex 790 | USB | Solo USB 2.0: copias y arranques desde USB lentos | Copia de un fichero grande a USB |
| OptiPlex 790 | Red | Una sola tarjeta Gigabit: gestión, clúster, almacenamiento y VM compartirían el mismo enlace | `iperf3` mientras se migra una VM (Tasca 7) |
| hEX RB750Gr3 | CPU | 880 MHz MIPS: el rendimiento con NAT, reglas de cortafuegos o VPN puede bajar del Gigabit | `iperf3` a través del router |

## 8. Impacto en el diseño y material que falta

Lo que el inventario obliga a revisar de las decisiones de la Tasca 1:

| Hallazgo | Fuente | Decisión afectada | Consecuencia |
|---|---|---|---|
| El i5-2400 (2.ª generación) **no está en la lista de procesadores admitidos por Windows 11** (desde la 8.ª generación) y el OptiPlex 790 solo tiene **TPM 1.2** y no admite arranque seguro | Microsoft Learn, Dell | [D-04 Clientes](../decisions/D-04-clientes.md) | Windows 11 Pro sigue siendo la decisión para la empresa; en el aula solo se puede instalar **sin soporte oficial**, marcado como limitación del laboratorio |
| El hEX es MMIPS (no x86) con 256 MB de RAM | MikroTik | [D-01 Comunicaciones](../decisions/D-01-comunicaciones.md) | OPNsense y el GNU/Linux con nftables (x86-64) no pueden ir en el hEX: van en un OptiPlex o en una VM de Proxmox |
| Máximo 16 GB de RAM y Gigabit único por PC | Dell | [D-02 Virtualización](../decisions/D-02-virtualizacion.md) | Cada nodo admite pocas VM a la vez; la tabla de recursos de la Tasca 4 debe reservar memoria para Proxmox |
| 16 GB de RAM sin ECC, 2 bahías de 3,5" y 4 puertos SATA (uno ocupado por el DVD) | Dell | [D-05 Datos](../decisions/D-05-datos.md) | TrueNAS cabe (mínimo 8 GB) con 1 disco de arranque + 2 de datos en espejo; hay que comprobar que haya discos para ello |
| El switch tiene VLAN, gestión y PoE de sobra | TP-Link | [D-01 Comunicaciones](../decisions/D-01-comunicaciones.md) | Permite separar redes de gestión, clúster y clientes por VLAN (Tasca 4) |

| Material que falta | Por qué hace falta | Alternativa | Comunicado al profesorado |
|---|---|---|---|
| Discos para el espejo de TrueNAS (si las unidades solo traen el SSD de 256 GB) | Necesidad N3: tolerar la caída de un disco | Discos virtuales en una VM de TrueNAS (simulación identificada) | |
| Segunda tarjeta de red por nodo | Separar el tráfico de clúster y almacenamiento del de las VM | VLAN en la única tarjeta | |
| Módulos SFP+ o cables DAC | Usar los enlaces de 10 Gb del switch | Puertos Gigabit | |
| Equipos que cumplan los requisitos de Windows 11 | Clientes con soporte oficial | Instalación sin soporte en el aula, marcada como limitación | |

## Fuentes

| Material | URL | Fecha de consulta |
|---|---|---|
| MikroTik hEX (RB750Gr3) – ficha | https://mikrotik.com/product/RB750Gr3 | 19/10/2026 |
| TP-Link Omada SG3428XMPP – ficha | https://www.omadanetworks.com/es/business-networking/omada-switch-access-plus/sg3428xmpp/ | 19/10/2026 |
| Intel Core i5-2400 – especificaciones | https://www.intel.com/content/www/us/en/products/sku/52207/intel-core-i52400-processor-6m-cache-up-to-3-40-ghz/specifications.html | 19/10/2026 |
| Dell OptiPlex 790 – guía técnica | https://i.dell.com/sites/doccontent/shared-content/data-sheets/en/Documents/optiplex-790-tech-guide.pdf | 19/10/2026 |
| Dell OptiPlex 790 – guía de instalación | https://dl.dell.com/manuals/all-products/esuprt_desktop/esuprt_optiplex_desktop/optiplex-790_setup%20guide_en-us.pdf | 19/10/2026 |
| Dell OptiPlex 790 – configuración de referencia | https://refurbishedpc.com/products/renewed-tower-computer-package-dell-optiplex-790-intel-quad-core-i5-2400-up-to-3-40-ghz-win-10-pro-dvd-rw-wifi-bluetooth-customize | 19/10/2026 |
| OptiPlex 790 y Windows 11 (TPM 1.2, sin arranque seguro) – comunidad de Dell | https://www.dell.com/community/en/conversations/optiplex-desktops/optiplex-and-windows-11/647f95ccf4ccf8a8de8676c6 | 19/10/2026 |
| Windows 11 – procesadores Intel admitidos | https://learn.microsoft.com/en-us/windows-hardware/design/minimum/supported/windows-11-supported-intel-processors | 19/10/2026 |
| Cable patch Cat5e 350 MHz – referencia | https://www.infinitecables.com/products/molded-boot-custom-rj45-cat5e-350mhz-assembled-patch-cable-grey | 19/10/2026 |

## Uso de IA

La lista de material sale de una conversación con ChatGPT sobre la Tasca 2: el grupo confirmó que los productos que identificó son el material real del aula. Claude (asistente de IA) completó las características con las fichas oficiales de cada fabricante y detectó el impacto en las decisiones de la Tasca 1 (apartado 8). Las cantidades y los datos de cada unidad se comprueban en el aula con los comandos indicados.
