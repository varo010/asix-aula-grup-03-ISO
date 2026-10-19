# 02 · Inventario y diagnóstico

Tasca 2 · RA principales: FDM RA1 a-h y RA4 h-i.

Inventario de **todo el material que nos ha dado el profesorado** para el entorno de pruebas, comprobado con herramientas y manuales, y diagnóstico del estado inicial de cada equipo. Solo se inventaría el material que tenemos: lo que falte va al último apartado.

**Cómo rellenarlo:**
- Los datos se sacan del equipo (comando, BIOS/UEFI o etiqueta del fabricante), nunca de memoria ni de lo que «debería» ser. En la columna *Fuente* se indica de dónde sale cada dato.
- Un dato que no se puede comprobar se marca **N/C** con la comprobación pendiente.
- Las capturas y salidas de comandos van saneadas a [`evidencies/`](../evidencies/) con el nombre `t2-<descripcion>-<fecha>`.
- Si un equipo tiene más elementos de los que caben en la tabla, se añaden filas (`PC-06`, `CAB-07`…).

## 1. Resumen del material

| ID | Tipo | Fabricante y modelo | Características principales | Estado | Responsable del inventario |
|---|---|---|---|---|---|
| RT-01 | Router MikroTik | | RouterOS, puertos, RAM | | |
| SW-01 | Switch | | Puertos y velocidades | | |
| PC-01 | Ordenador de sobremesa | | CPU, RAM, discos | | |
| PC-02 | Ordenador de sobremesa | | CPU, RAM, discos | | |
| PC-03 | Ordenador de sobremesa | | CPU, RAM, discos | | |
| PC-04 | Ordenador de sobremesa | | CPU, RAM, discos | | |
| PC-05 | Ordenador de sobremesa | | CPU, RAM, discos | | |
| CAB | Cableado | — | Tipo y cantidad (ver apartado 5) | | |
| OTR | Otros materiales | — | Fuentes, accesorios, portátiles, discos (ver apartado 6) | | |

Estado: *Funciona* · *Funciona con incidencias* · *Averiado* · *Pendiente de probar*.

## 2. Router MikroTik

Los datos se obtienen desde la terminal del router (Winbox → *New Terminal*, o SSH).

| Dato | Valor | Fuente / comando |
|---|---|---|
| Fabricante | MikroTik | Etiqueta del equipo |
| Modelo exacto | | `/system routerboard print` (campo `model`) y etiqueta inferior |
| Número de serie | | `/system routerboard print` (campo `serial-number`) |
| Versión de RouterOS | | `/system resource print` (campo `version`) |
| Canal de actualización | | `/system package update print` |
| Versión del firmware (RouterBOOT) | | `/system routerboard print` (`current-firmware` y `upgrade-firmware`) |
| Arquitectura y procesador | | `/system resource print` (`architecture-name`, `cpu`, `cpu-count`, `cpu-frequency`) |
| Memoria RAM | | `/system resource print` (`total-memory`) |
| Almacenamiento | | `/system resource print` (`total-hdd-space`) |
| Licencia (nivel) | | `/system license print` |
| Alimentación | | Etiqueta del adaptador (voltaje y amperios) |

**Puertos Ethernet y velocidades**

| Puerto | Velocidad máxima | Velocidad negociada | PoE | Conectado a | Estado del enlace |
|---|---|---|---|---|---|
| ether1 | | | | | |
| ether2 | | | | | |
| ether3 | | | | | |
| ether4 | | | | | |
| ether5 | | | | | |

Comandos: `/interface ethernet print detail` (velocidades), `/interface ethernet monitor ether1` (velocidad negociada y estado), `/interface print` (estado de cada interfaz).

**Configuración de red actual**

| Interfaz | Dirección IP / máscara | Origen (estática o DHCP) | Observaciones |
|---|---|---|---|
| | | | |

Comandos: `/ip address print`, `/ip dhcp-client print`, `/ip route print`.

Exportar la configuración completa como evidencia, revisando antes que no contenga contraseñas: `/export file=t2-mikrotik-config-inicial`.

## 3. Switch

| Dato | Valor | Fuente / comando |
|---|---|---|
| Fabricante | | Etiqueta del equipo |
| Modelo exacto | | Etiqueta inferior o trasera; interfaz web si es gestionable |
| Número de serie | | Etiqueta |
| Gestionable (sí/no) | | ¿Tiene IP o interfaz web? |
| Versión de firmware | | Interfaz de gestión (si la tiene) |
| Número total de puertos | | Contar en el equipo y comprobar en la ficha del fabricante |
| Velocidades de los puertos | | Ficha del fabricante (10/100/1000, SFP…) |
| PoE (sí/no, potencia) | | Ficha del fabricante |
| VLAN (sí/no) | | Ficha del fabricante o interfaz de gestión |
| Alimentación | | Interna o adaptador externo |

**Puertos ocupados y libres**

| Puerto | Conectado a | Velocidad del enlace (LED o gestión) | Estado del enlace | Observaciones |
|---|---|---|---|---|
| 1 | | | | |
| 2 | | | | |
| 3 | | | | |
| 4 | | | | |
| 5 | | | | |
| 6 | | | | |
| 7 | | | | |
| 8 | | | | |

Total: ___ puertos ocupados · ___ puertos libres. Si el switch tiene más puertos, se añaden filas.

## 4. Ordenadores de sobremesa

Una tabla por equipo. Copiar el bloque de `PC-01` para el resto.

### PC-01

| Dato | Valor | Fuente / comando (Linux) | Fuente / comando (Windows) |
|---|---|---|---|
| Fabricante y modelo | | `sudo dmidecode -s system-manufacturer` y `-s system-product-name` | `Get-CimInstance Win32_ComputerSystem` |
| Número de serie / etiqueta de servicio | | `sudo dmidecode -s system-serial-number` | `Get-CimInstance Win32_BIOS` |
| Uso previsto en el laboratorio | | Diseño del grupo | Diseño del grupo |
| Procesador (modelo, núcleos e hilos, frecuencia) | | `lscpu` | `Get-CimInstance Win32_Processor` |
| Virtualización por hardware (VT-x/AMD-V) y SLAT | | `lscpu \| grep -i virt`; `grep -E 'vmx\|svm' /proc/cpuinfo` | `systeminfo` (apartado Hyper-V) |
| RAM instalada | | `free -h` | `Get-CimInstance Win32_PhysicalMemory` |
| Módulos de RAM, ranuras libres y máximo admitido | | `sudo dmidecode -t memory` | `Get-CimInstance Win32_PhysicalMemory`; ficha del fabricante |
| Discos (tipo, modelo y capacidad) | | `lsblk -d -o NAME,MODEL,SIZE,ROTA,TRAN` | `Get-PhysicalDisk` |
| Estado SMART de los discos | | `sudo smartctl -H -A /dev/sdX` | CrystalDiskInfo o `Get-StorageReliabilityCounter` |
| Sistema operativo instalado | | `cat /etc/os-release` | `winver` o `Get-ComputerInfo` |
| Tarjeta de red (modelo) | | `lspci \| grep -i ethernet` | `Get-NetAdapter` |
| Velocidad de conexión | | `ethtool <interfaz>` | `Get-NetAdapter` (LinkSpeed) |
| Firmware: BIOS o UEFI, versión | | `sudo dmidecode -s bios-version`; `ls /sys/firmware/efi` | `msinfo32` |
| Arranque seguro | | `mokutil --sb-state` | `Confirm-SecureBootUEFI` |
| TPM (versión) | | `ls /dev/tpm*`; `cat /sys/class/tpm/tpm0/tpm_version_major` | `Get-Tpm`; `tpm.msc` |
| Periféricos conectados | | `lsusb` | Administrador de dispositivos |
| Alimentación (potencia de la fuente) | | Etiqueta de la fuente | Etiqueta de la fuente |
| Formato (torre, SFF, mini) | | Inspección física | Inspección física |
| Estado general | | Inspección física y arranque | Inspección física y arranque |

### PC-02 … PC-05

Copiar la tabla de `PC-01`.

### Resumen de los ordenadores

| ID | Uso previsto | CPU | Virtualización | RAM | Discos | Red | Firmware / TPM | Observaciones |
|---|---|---|---|---|---|---|---|---|
| PC-01 | | | | | | | | |
| PC-02 | | | | | | | | |
| PC-03 | | | | | | | | |
| PC-04 | | | | | | | | |
| PC-05 | | | | | | | | |

## 5. Cableado

| ID | Cantidad | Tipo y categoría (Cat5e, Cat6…) | Conectores | Longitud aproximada | Estado | Conecta | Observaciones |
|---|---|---|---|---|---|---|---|
| CAB-01 | | | RJ45 | | | | |
| CAB-02 | | | | | | | |
| CAB-03 | | | | | | | |

- La categoría viene impresa en la funda del cable.
- El estado se comprueba con un comprobador de cables o con el LED del puerto y la velocidad negociada (apartados 2 y 3).
- Cada cable se etiqueta en sus dos extremos con su ID (Tasca 3).

## 6. Otros materiales

| ID | Material | Cantidad | Modelo / características | Estado | Observaciones |
|---|---|---|---|---|---|
| OTR-01 | Fuentes de alimentación / adaptadores | | | | |
| OTR-02 | Regletas | | | | |
| OTR-03 | Portátiles | | | | |
| OTR-04 | Discos adicionales | | | | |
| OTR-05 | Monitores, teclados y ratones | | | | |
| OTR-06 | Otros accesorios | | | | |

## 7. Diagnósticos (línea base)

Una vez instalados los sistemas operativos, se ejecutan pruebas para conocer el **estado inicial** de cada equipo (la línea base) e identificar los **cuellos de botella**. Esta línea base se comparará en la Tasca 7 con los equipos trabajando.

Por cada prueba se registran la herramienta, las condiciones, el resultado y la interpretación. Si aparece una avería, se anota la causa probable y una prueba que la distinga de otra causa.

| ID equipo | Recurso | Prueba | Herramienta | Condiciones | Resultado | Interpretación | Evidencia |
|---|---|---|---|---|---|---|---|
| | CPU | Modelo y uso en reposo | `lscpu`, `top` / Administrador de tareas | Equipo en reposo, sin VM | | | |
| | RAM | Memoria total, usada y disponible | `free -h` / Administrador de tareas | En reposo | | | |
| | RAM | Test de memoria | MemTest86+ | Arranque desde USB, 1 pasada completa | | | |
| | Disco | Capacidad y espacio libre | `df -h` / Explorador | — | | | |
| | Disco | Estado SMART | `smartctl -H -A` / CrystalDiskInfo | — | | | |
| | Disco | Rendimiento de lectura y escritura | `fio` / CrystalDiskMark | Sin otras cargas | | | |
| | Red | Interfaces y velocidad de enlace | `ip -br link`, `ethtool` / `Get-NetAdapter` | Cable conectado al switch | | | |
| | Red | Conectividad | `ping` a la puerta de enlace y entre equipos | LAN del grupo | | | |
| | Red | Rendimiento | `iperf3` entre dos equipos | Mismo switch, sin otro tráfico | | | |
| RT-01 | CPU y RAM | Uso en reposo | `/system resource print` | Sin tráfico | | | |
| RT-01 | Red | Estado y velocidad de los puertos | `/interface ethernet monitor` | Enlaces conectados | | | |
| SW-01 | Red | Enlaces y velocidad | LED o interfaz de gestión | Todos los equipos conectados | | | |

### Cuellos de botella detectados

| ID equipo | Recurso que limita | Dato que lo demuestra | Efecto en el laboratorio | Posible solución |
|---|---|---|---|---|
| | | | | |

## 8. Material que falta o limita el diseño

| Material | Por qué hace falta o qué limita | Alternativa (simular, virtualizar…) | Comunicado al profesorado (fecha) |
|---|---|---|---|
| | | | |
