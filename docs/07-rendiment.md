# 07 · Rendimiento y diagnóstico

Tasca 7 «Rendiment i diagnòstic» · RA principales: ISO RA6 a-h y FDM RA1 e-g.

Medimos los equipos en reposo (línea base), los volvimos a medir trabajando y comparamos. Todas las medidas son del 19/11/2026.

## Cómo medimos

| Recurso | Herramienta | Qué nos dice |
|---|---|---|
| CPU y memoria | `vmstat 5` | Cada 5 s: % de CPU usada (`us`, `sy`), libre (`id`) y **esperando al disco (`wa`)** |
| Memoria | `free -m` | RAM usada y disponible |
| Disco | `iostat -dx 5` y `fio` | Lo que se lee y escribe por segundo, y la velocidad máxima que da el disco |
| Red | `sar -n DEV 5` e `iperf3` | Tráfico de la tarjeta y velocidad máxima entre dos equipos |
| NAS | Su propio monitor (**Reporting** de TrueNAS) | CPU, carga, disco y red del NAS |
| Router | **System → Resources** y tráfico de `ether1` | CPU, RAM y megabits que pasan |

En los nodos lo juntamos en una sola orden que mide todo a la vez durante el tiempo de la prueba, para que las cifras de CPU, disco y red sean del mismo momento. Las herramientas se instalaron con `apt install sysstat fio iperf3 stress-ng`.

## 1. Situación inicial (línea base)

La línea base de los dos PC físicos (PC-01 y PC-02) es la de la Tasca 2: [02-inventari, apartado 5](02-inventari.md#5-diagnósticos-línea-base). Aquí añadimos los equipos que entonces no existían. Evidencia: [t7-linea-base-20261119.txt](../evidencies/t7-linea-base-20261119.txt).

| Equipo | Qué tiene encendido | CPU libre | RAM usada | Disco | Red |
|---|---|---|---|---|---|
| Nodo `pve` | Windows Server (VM 100) | 93 % | **13,5 de 15,6 GiB (86 %)** | Casi parado | 0,2 Mbit/s |
| Nodo pve2 | `Debian-RAID` y `ct-test` | 99 % | 2,3 de 11,7 GiB (20 %) | Casi parado | 0,1 Mbit/s |
| Nodo pve3 | `vm-test` (sin sistema) | 86 % | 1,8 de 11,7 GiB (15 %) | Casi parado | 0,3 Mbit/s |
| TrueNAS | Carpetas SMB y NFS | 99,5 % | Casi toda (ZFS la usa de caché) | 0,2 MiB/s | 0,2 Mbit/s |
| MikroTik | Red del grupo, 115 conexiones abiertas | 99 % | 66 de 256 MiB (26 %) | — | 1 a 11 Mbit/s |

Velocidad máxima de cada pieza, sin nada más trabajando:

| Qué | Medida | Resultado |
|---|---|---|
| Red entre dos nodos | `iperf3` de `pve` a pve2, 20 s | **89,7 Gbit/s**, `ping` 0,15 ms |
| Disco local de un nodo, fichero grande | `fio` 1 GiB | Escribe a 1.232 MiB/s, lee a 4.633 MiB/s |
| Disco local de un nodo, trozos pequeños | `fio` 4 KiB al azar | 23.500 lecturas y 20.700 escrituras por segundo |
| NAS por la red (NFS), fichero grande | `fio` 1 GiB | **Escribe a 21,8 MiB/s**, lee a 104 MiB/s |
| NAS por la red (NFS), trozos pequeños | `fio` 4 KiB al azar | 4.593 lecturas y **93 escrituras por segundo** |

Lo que ya se ve en reposo:

- **El nodo `pve` tiene la memoria casi llena.** El Windows Server tiene asignados 12 GiB de los 15,6 del nodo. Solo quedan 2,4 GiB disponibles: en ese nodo no cabe otra VM.
- **pve3 gasta un 14 % de CPU sin hacer nada útil.** Es `vm-test`: una VM sin sistema operativo que se queda dando vueltas en el arranque.
- **La red entre nodos no es una red de verdad.** Los tres nodos son máquinas virtuales del mismo servidor de clase, así que los datos entre ellos no salen por ningún cable: por eso van a 89,7 Gbit/s. Entre un nodo y el NAS sí hay cable y switch (1 Gbit/s).
- **El disco local de los nodos tampoco es un disco normal.** Está sobre el almacenamiento rápido del servidor de clase, de ahí los más de 1.000 MiB/s.

## Comparación con los equipos trabajando

Cuatro cargas, cada una pensada para apretar una pieza distinta.

### Carga 1 · El NAS guardando una copia grande

Copia de seguridad del Windows Server (disco de 64 GiB, 16,6 GiB con datos) del nodo `pve` al NAS. Duró **4 min 46 s** y la copia ocupa 7,96 GB. Evidencia: [t7-carga-copia-windows-server-20261119.txt](../evidencies/t7-carga-copia-windows-server-20261119.txt).

| Equipo | Recurso | En reposo | Durante la copia |
|---|---|---|---|
| Nodo `pve` | CPU usada (`us` + `sy`) | 2 % | 16 % |
| Nodo `pve` | **CPU esperando al disco (`wa`)** | 1 % | **35 % de media, 45 % de máximo** |
| Nodo `pve` | Red recibida / enviada | 0,1 / 0,2 Mbit/s | 556 / 249 Mbit/s |
| TrueNAS | CPU | 0,5 % | 5,5 % de media, 25 % de máximo |
| TrueNAS | Carga (tiene 4 núcleos) | 0,14 | 1,62 de media, **4,46 de máximo** |
| TrueNAS | Disco de datos: lectura / escritura | 0 / 0,2 MiB/s | hasta 119 / 152 MiB/s |
| TrueNAS | Red | 0,2 Mbit/s | hasta **984 Mbit/s** |

**Interpretación.** La CPU del nodo no está ocupada: está **esperando** (35 % en `wa`). Lo que espera es al NAS, porque esta copia lee el disco de la VM del NAS y escribe la copia en el mismo NAS, y los dos ficheros están en el mismo disco duro. La red llegó a 984 Mbit/s, casi el tope del cable de 1 Gbit/s. **Recursos saturados: el disco duro del NAS y la red de 1 Gbit/s.** La CPU y la memoria van sobradas.

### Carga 2 · Escribir en el NAS desde un nodo

Las pruebas de `fio` desde pve2 contra el NAS, durante 2 min y medio. Evidencia: [t7-disco-local-y-nas-20261119.txt](../evidencies/t7-disco-local-y-nas-20261119.txt).

| Equipo | Recurso | En reposo | Durante la prueba |
|---|---|---|---|
| Nodo pve2 | CPU libre | 99 % | 97 % |
| TrueNAS | CPU | 0,5 % | 5,6 % de media, 14 % de máximo |
| TrueNAS | Carga | 0,14 | 0,77 de media, 1,53 de máximo |
| TrueNAS | Escritura en el disco de datos | 0,2 MiB/s | 11,8 MiB/s de media, 48,6 de máximo |
| TrueNAS | Red | 0,2 Mbit/s | hasta 920 Mbit/s (leyendo) |

**Interpretación.** Leyendo, el NAS da 104 MiB/s: es el tope de la red (1 Gbit/s son unos 112 MiB/s). Escribiendo se queda en 21,8 MiB/s, con la red casi vacía y la CPU del NAS al 5 %. **Recurso saturado: el disco duro del NAS al escribir.** El motivo es que Proxmox pide al NAS que confirme cada escritura cuando ya está de verdad en el disco (escritura «segura» o síncrona), y un disco duro mecánico solo puede hacer unas 90 de esas por segundo.

### Carga 3 · Un nodo con la CPU al máximo

`stress-ng --cpu 2` en pve3 durante 150 s (carga simulada). Evidencia: [t7-alerta-carga-simulada-20261119.txt](../evidencies/t7-alerta-carga-simulada-20261119.txt).

| Recurso | En reposo | Con la carga |
|---|---|---|
| CPU usada | 14 % | **100 %** (88 % de usuario, 0 % libre) |
| Procesos esperando turno de CPU (columna `r`) | 0 o 1 | 3 o 4 con solo 2 núcleos |
| Memoria, disco y red | — | Sin cambios |

**Interpretación.** **Recurso saturado: la CPU.** Con 2 núcleos, en cuanto dos procesos piden toda la CPU ya no queda nada para las VM. Los nodos tienen solo 2 núcleos cada uno: es su límite más claro para poner más máquinas.

### Carga 4 · Varios equipos descargando a través del router

Cuatro descargas a la vez por HTTPS desde un equipo de la LAN, 40 s. Evidencia: [t7-red-y-router-20261119.txt](../evidencies/t7-red-y-router-20261119.txt).

| Recurso del MikroTik | En reposo | Con las descargas |
|---|---|---|
| CPU | 0 a 1 % | **11 % de media, 29 % de máximo** |
| RAM libre | 190 MiB | 190 MiB |
| Bajada por la WAN | 1 a 11 Mbit/s | **110 Mbit/s de media, 127 de máximo** |

**Interpretación.** El router no se satura: mueve 125 Mbit/s usando un 11 % de CPU. El tope de 125 Mbit/s no lo pone él, lo pone **la salida a Internet de la red del aula**, que compartimos con los otros grupos. No hemos podido simular a todos los clientes navegando a la vez; con una sola máquina haciendo cuatro descargas, al router le sobra.

### Comparación con la línea base de la Tasca 2

| Medida | Tasca 2 (03/11), midiendo en el propio equipo | Tasca 7 (19/11), con los equipos trabajando | Qué ha cambiado |
|---|---|---|---|
| HDD del NAS, leer un fichero grande | 189 MiB/s | 104 MiB/s desde un nodo | Ahora se llega por la red: manda el cable de 1 Gbit/s, como ya predijimos (112 MiB/s) |
| HDD del NAS, accesos pequeños | 179 lecturas por segundo | 93 escrituras «seguras» por segundo | Es el mismo límite del disco mecánico: unas 100 a 180 operaciones por segundo |
| Red PC-01 ↔ NAS | 941 Mbit/s | hasta 984 Mbit/s nodo ↔ NAS | Igual: 1 Gbit/s |
| Espera de disco (`wa`) | 38,8 % en el PC-01 en reposo | 35 % en el nodo `pve` durante la copia | El mismo síntoma: la CPU parada esperando a un disco duro |

**Conclusión.** La Tasca 2 decía que el cuello de botella eran los discos duros con accesos pequeños y la red Gigabit con ficheros grandes. Con los equipos trabajando se confirma, y ahora sabemos dónde pesa más: **el disco duro del NAS es la pieza de la que depende todo el clúster**, porque en él están los discos de las VM y también sus copias.

| Equipo | Recurso que se satura primero | Con qué carga |
|---|---|---|
| Nodo `pve` | Memoria RAM (86 % en reposo) | Solo con tener encendido el Windows Server |
| Nodos pve2 y pve3 | CPU (2 núcleos) | Cualquier trabajo que use los dos núcleos |
| TrueNAS | Disco duro de datos | Escrituras desde Proxmox y copias de seguridad |
| Red nodo ↔ NAS | Cable de 1 Gbit/s | Lecturas de ficheros grandes |
| MikroTik | Ninguno (el límite es Internet) | — |

## Ampliación: alertas, carga simulada y optimización

### Alerta en los nodos

Instalamos `monit` en los tres nodos (`apt install monit`). Es un programa pequeño que mira el sistema cada 20 s y avisa cuando algo pasa de un límite. La configuración está en `/etc/monit/conf.d/g3-alertas` y es la misma en los tres:

| Qué vigila | Umbral | Duración | Por qué ese valor |
|---|---|---|---|
| Uso de CPU | Más del 80 % | 1 minuto seguido (3 comprobaciones) | En reposo los nodos están entre el 1 y el 14 %. Por encima del 80 % las VM ya van lentas |
| Carga por núcleo | Más de 2 | 1 minuto | Carga 2 por núcleo es que hay el doble de trabajo del que la CPU puede atender |
| Memoria | Más del 90 % | 1 minuto | El nodo `pve` está al 86 % en reposo: el aviso salta si alguien enciende otra VM ahí |
| Disco del sistema (`/`) | Más del 85 % | 1 minuto | Si se llena, Proxmox deja de funcionar |
| Carpeta del NAS montada | Que no exista | 1 minuto | Sin el NAS las VM pierden su disco |

**Por qué 1 minuto y no al momento:** arrancar una VM o instalar un paquete pone la CPU al 100 % unos segundos, y eso no es un problema. Esperando tres comprobaciones seguidas solo avisa cuando la carga se mantiene.

Cuando salta una alerta, `monit` ejecuta `/usr/local/sbin/g3-alerta`, que la apunta con fecha y hora en `/var/log/g3-alertas.log` y en el registro del sistema. También avisa cuando el valor vuelve a la normalidad.

> Los nodos no tienen configurado el envío de correo, así que la alerta se queda en el registro del nodo: hay que entrar a mirarla. Cuando configuréis un correo o un aviso al móvil, escribid aquí cómo.

### Carga simulada y detección

Es la carga 3 de arriba: `stress-ng --cpu 2 --timeout 150s` en pve3. Lo hicimos en pve3 y no en `pve` para no frenar el Windows Server.

| Hora | Qué pasó |
|---|---|
| 10:35:22 | Empieza la carga. La CPU pasa al 100 % |
| 10:35:52 y 10:36:12 | `monit` ve la CPU al 95,7 % y al 93,0 %, pero todavía no avisa (*warning*) |
| **10:36:32** | Tercera comprobación seguida por encima del 80 % (94,7 %): **salta la alerta** `CPU-ALTA` |
| 10:37:52 | Termina la carga |
| **10:38:52** | Tres comprobaciones seguidas bien (9,8 %): aviso `CPU-NORMAL` |

La alerta saltó **70 s después de empezar la carga**: los 60 s de duración que pusimos más lo que faltaba para la siguiente comprobación. Línea del registro:

```
19/11/2026 10:36:32 pve3 ALERTA CPU-ALTA | carga 1.87 0.88 0.58 | cpu usage of 94.7% matches resource limit [cpu usage > 80.0%]
19/11/2026 10:38:52 pve3 ALERTA CPU-NORMAL | carga 1.19 1.13 0.73 | cpu usage check succeeded [current cpu usage = 9.8%]
```

### Optimización: la escritura en el NAS

El recurso más saturado es el disco del NAS al escribir (cargas 1 y 2). La causa es la escritura «segura»: el NAS no dice «hecho» hasta que el dato está en el disco. ZFS tiene un ajuste por dataset, `sync`, que permite cambiarlo: con `sync=disabled` el NAS contesta «hecho» en cuanto tiene el dato en memoria y lo escribe en el disco unos segundos después.

Lo probamos en un **dataset de pruebas aparte** (`pruebas-rendimiento`), no en el de las VM, con las mismas pruebas antes y después. Evidencia: [t7-optimizacion-escritura-nas-20261119.txt](../evidencies/t7-optimizacion-escritura-nas-20261119.txt).

| Prueba | Antes (`sync=standard`) | Después (`sync=disabled`) | Mejora |
|---|---|---|---|
| Escribir un fichero grande (512 MiB) | 21,9 MiB/s | **101 MiB/s** | 4,6 veces más rápido |
| Escrituras pequeñas «seguras» (4 KiB) | 94 por segundo | **3.289 por segundo** | 35 veces más rápido |
| Arranque de un contenedor | 0,9 s | 0,9 s | Igual |

Con el ajuste, la escritura sube hasta el tope de la red (unos 100 MiB/s): el disco deja de ser el cuello de botella y pasa a serlo el cable.

**Por qué no lo hemos dejado puesto en el dataset de las VM.** Tiene un precio: si el NAS se apaga de golpe, lo que tenía en memoria y aún no había escrito (hasta unos 5 s) se pierde, y un disco de VM al que le faltan escrituras puede quedar estropeado. Nuestro NAS no tiene batería (SAI) y ahí está el Windows Server. La velocidad no compensa ese riesgo, así que el dataset `proxmox` sigue con `sync=standard` y el dataset de pruebas se borró al terminar.

**Cómo se conseguiría la misma mejora sin riesgo:**

1. Poner en el NAS un SSD pequeño solo para apuntar las escrituras «seguras» (en ZFS se llama SLOG). Las escrituras siguen siendo seguras, pero van a la velocidad del SSD.
2. Poner un SAI al NAS. Con eso, `sync=disabled` deja de ser peligroso ante un corte de luz.
3. Usar `sync=disabled` solo donde perder los últimos segundos no importe, como un dataset de datos temporales.

> Si decidís aplicar alguna de estas mejoras, escribid aquí cómo y repetid las dos medidas de la tabla para comparar.

### Otras mejoras que salen de las medidas

| Problema medido | Mejora | Estado |
|---|---|---|
| El nodo `pve` tiene la RAM al 86 % | Bajar la RAM del Windows Server de 12 a 8 GiB (hay que apagarlo) o moverlo a un nodo con más memoria libre | Sin hacer |
| pve3 gasta un 14 % de CPU en `vm-test`, que no tiene sistema | Apagarla cuando no se esté probando una migración | Sin hacer |
| Las copias de las VM se leen y se escriben en el mismo disco | Un segundo disco en el NAS solo para copias | Pendiente de material |
| Los nodos solo tienen 2 núcleos | Pedir más núcleos al profesorado si hay que poner más VM | Sin pedir |

> Revisad esta lista y dejad solo las mejoras que el grupo comparta.
