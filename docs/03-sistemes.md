# 03 · Comparativa de sistemas

Tasca 1 «Necessitats i sistemes» · RA principales: ISO RA1 a-c y FDM RA4 a-c.

Cada opción: función, arquitectura, versión, soporte, origen de la descarga, requisitos mínimos oficiales, recursos propuestos, licencia, administración, actualizaciones, compatibilidad, ventajas y limitaciones. Cada sistema con **fuente oficial, URL y fecha de consulta**.

Cada función se analiza en cuatro pasos: **necesidades**, **comparación**, **contraste** con fuentes oficiales y **justificación** de la elección. Los datos no confirmados se marcan **N/C** y tienen su propuesta de comprobación en [Datos pendientes](#datos-pendientes-nc); las hipótesis de trabajo se marcan como **supuesto**. No se afirma que una opción sea «más segura» o «más rápida» sin indicar las condiciones y la prueba que lo demuestra.

Las decisiones de cada función están registradas en [decisions/](../decisions/). Todas las fuentes, con URL y fecha, están en [Fuentes](#fuentes).

## Resumen de decisiones

| Función | Pregunta de decisión | Sistema elegido | Responsable | Decisión |
|---|---|---|---|---|
| Comunicaciones | ¿Cómo conectamos y filtramos redes? | MikroTik RouterOS (routing y VPN) + OPNsense (seguridad perimetral) | Iván | [D-01](../decisions/D-01-comunicaciones.md) |
| Virtualización | ¿Qué recursos y nodos necesita el laboratorio? | Proxmox VE 9.2 | Juan Pablo | [D-02](../decisions/D-02-virtualizacion.md) |
| Gestión centralizada | ¿Qué servicio gestiona identidades y equipos? | Windows Server 2025 con Active Directory | Alex | [D-03](../decisions/D-03-gestion-centralizada.md) |
| Clientes | ¿Qué opción admite las tareas de cada departamento? | Windows 11 Pro (puestos) y Ubuntu Desktop 26.04 LTS con VM de Windows 11 Pro (Informática) | Mauro y Adam | [D-04](../decisions/D-04-clientes.md) |
| Datos | ¿Cómo organizamos, compartimos y recuperamos ficheros? | TrueNAS Community Edition 25.10.7 | Álvaro | [D-05](../decisions/D-05-datos.md) |

**Criterio general:** Windows donde lo exigen las identidades y el software de los puestos de trabajo; Linux y software libre para cortafuegos, virtualización y datos. Solo se paga licencia donde el software de gestión de la empresa la exige.

## Comunicaciones

Responsable: Iván.

### 1. Necesidades

- Enrutamiento entre la LAN y la WAN (varias WAN o BGP: supuesto, a confirmar).
- Cortafuegos con inspección de capa 7 y prevención de intrusiones (IDS/IPS).
- VPN para los portátiles de Comercial y accesos remotos.
- Despliegue sobre hardware dedicado o virtualizado (MikroTik CHR, OPNsense en VM).

### 2. Comparación

| Criterio | MikroTik RouterOS | OPNsense | pfSense CE |
|---|---|---|---|
| Función | Router, firewall básico y VPN | Firewall perimetral, IDS/IPS y VPN | Firewall perimetral y VPN |
| Arquitectura | Hardware MikroTik o CHR virtual (x86 de 64 bits con virtualización) | x86-64 (amd64) | x86-64 (amd64) |
| Versión | 7.24.5 *stable* (29/09/2026); 7.23.8 *long-term* (09/10/2026) | Serie 26.7 «Xenial Xenops» (26.7.7) | 2.9.0 (20/08/2026) |
| Soporte | Foro oficial y soporte del fabricante; licencias perpetuas | Comunidad; edición Business de pago | Comunidad; las funciones avanzadas van en pfSense Plus |
| Origen de la descarga | mikrotik.com/download | opnsense.org/download | Instalador oficial de Netgate (netgate.com) |
| Requisitos mínimos oficiales | CHR: 256 MB de RAM (recomienda 1024 MiB) y 128 MB de disco | CPU de doble núcleo a 1 GHz, 3 GB de RAM y 4 GB de almacenamiento | CPU amd64, 1 GB de RAM y disco de 8 GB |
| Requisitos recomendados | — | CPU multinúcleo a 1,5 GHz, 8 GB de RAM y SSD de 120 GB | — |
| Recursos propuestos para el laboratorio | Routers MikroTik del aula (supuesto, según Tasca 0) | VM con 8 GB de RAM (supuesto; depende del inventario de la Tasca 2) | — |
| Licencia y precio | Pago único: en routers va incluida; CHR gratuito limitado a 1 Mbps, P1 (1 Gbps) 45 $, P10 (10 Gbps) 95 $, ilimitada 250 $ | Open source, sin coste de software | CE gratuito; varias funciones solo en pfSense Plus |
| Administración | Winbox, web y CLI; curva de aprendizaje pronunciada | Interfaz web | Interfaz web |
| Actualizaciones | Canales *stable* y *long-term* | Dos versiones mayores al año (enero y julio) y menores cada dos semanas | Irregulares: 2.7.2 (12/2023), 2.8.0 (05/2025), 2.9.0 (08/2026) |
| Compatibilidad | CHR probado en VirtualBox, VMware, QEMU, Hyper-V (solo generación 1) y Xen | Hardware x86-64 o VM | Hardware x86-64 |
| Ventajas | Protocolos de routing avanzados; bajo coste de licencia y hardware | IDS/IPS con Suricata e inspección de capa 7 con Zenarmor; sin coste de licencia | Requisitos mínimos más bajos que OPNsense |
| Limitaciones | Sin funciones de firewall de nueva generación (NGFW) | Necesita más CPU y RAM que un router dedicado | OpenVPN DCO, CARP unicast, entornos de arranque ZFS y exportación NetFlow solo en pfSense Plus |

### 3. Contraste

- **MikroTik:** la documentación oficial de CHR fija requisitos y precios de licencia de pago único, sin suscripción anual. Versiones según el registro de cambios oficial y el anuncio en el foro oficial.
- **OPNsense:** la documentación oficial de hardware separa requisitos mínimos y recomendados; las notas de versión muestran dos series al año.
- **pfSense CE:** la documentación oficial de Netgate pide 1 GB de RAM y 8 GB de disco, menos que OPNsense (3 GB de RAM). Su lista de versiones muestra publicaciones irregulares de CE (casi 18 meses entre la 2.7.2 y la 2.8.0) y su documentación reserva varias funciones a pfSense Plus.
- **OPNsense:** su documentación de actualizaciones fija dos versiones mayores al año con actualizaciones menores cada dos semanas.
- Un equipo «todo en uno» de gama alta (Fortinet, Palo Alto) que reúna routing y NGFW exige licencias anuales.

### 4. Justificación

Se elige una **topología mixta**: MikroTik se encarga del enrutamiento y las VPN, y OPNsense de la seguridad perimetral. Cada sistema cubre la carencia del otro: MikroTik no tiene NGFW y OPNsense aporta IDS/IPS e inspección de capa 7 sin coste de licencia. El rendimiento de cada equipo se medirá en la Tasca 7 antes de afirmar nada sobre él.

- **Inconveniente aceptado:** administrar dos plataformas distintas en lugar de una centralizada.
- **Descartados:**
  - Cisco, Fortinet y Palo Alto, por el coste recurrente de licencias.
  - pfSense CE: aunque pide menos recursos que OPNsense, sus versiones salen de forma irregular y Netgate reserva varias funciones a pfSense Plus. OPNsense publica parches cada dos semanas e incluye todas sus funciones en la edición libre. En un cortafuegos perimetral, la regularidad de los parches de seguridad pesa más que 2 GB de RAM.

## Virtualización

Responsable: Juan Pablo.

### 1. Necesidades

- Ejecutar en máquinas virtuales los servidores del escenario.
- Probar y recuperar sin riesgo (instantáneas y copias).
- Preparar un clúster de 3 nodos con almacenamiento compartido NAS/SAN.
- Coste bajo y gestión sencilla.

### 2. Comparación

| Criterio | Proxmox VE | Hyper-V | VMware ESXi | XCP-ng | VirtualBox |
|---|---|---|---|---|---|
| Función | VM, contenedores, clúster, migración, alta disponibilidad y copias | Hipervisor incluido en Windows Server | Hipervisor | Hipervisor basado en Xen | Virtualización de escritorio |
| Arquitectura | x86-64; arm64 desde agosto de 2026 | x64 | x86-64 | x86-64 | Anfitriones Windows, macOS, Linux y Solaris |
| Versión | 9.2 (21/05/2026), sobre Debian 13 | Windows Server 2025 | 8.0 Update 3e (versión gratuita) | 8.3 LTS | 7.2.20 |
| Soporte | Comunidad; suscripción de pago. Cada versión, al menos lo mismo que su Debian | Microsoft, según el ciclo de Windows Server | Versión gratuita sin soporte oficial | Comunidad; soporte de pago de Vates | Comunidad; soporte comercial de Oracle |
| Origen de la descarga | proxmox.com/en/downloads | Centro de evaluación de Microsoft | Portal de Broadcom | xcp-ng.org | virtualbox.org/wiki/Downloads |
| Requisitos mínimos oficiales | CPU de 64 bits con Intel VT o AMD-V, 1 GB de RAM más la de las VM, disco y una NIC | CPU de 64 bits con SLAT, VT-x/AMD-V y DEP; al menos 4 GB de RAM | 2 núcleos, 8 GB de RAM (12 GB recomendados), disco de arranque de 32 GB, NX/XD y VT-x/AMD-V | CPU x86 de 64 bits a 1,5 GHz con VT-x/AMD-V y SLAT, 2 GB de RAM y 46 GB de disco | Sin mínimo oficial: depende del anfitrión y de las VM |
| Requisitos recomendados | 2 GB de RAM para el sistema más la de las VM; almacenamiento redundante; varias NIC | — | — | CPU multinúcleo a 2 GHz, 8 GB de RAM y 70 GB de disco | — |
| Recursos propuestos para el laboratorio | 3 nodos y almacenamiento compartido NAS/SAN (diseño del enunciado; no confirmados en el aula) | — | — | — | — |
| Licencia y precio | AGPLv3, gratuito; suscripción opcional desde 120 €/socket/año | Requiere Windows Server (desde 1.176 $ más CAL) | ESXi gratuito con limitaciones; versión completa por suscripción por núcleo (Broadcom no publica tarifa) | Open source | Base GPLv3 gratuita; el Extension Pack es de pago para uso comercial |
| Administración | Consola web integrada en cada nodo, también para clúster y copias | Administrador Hyper-V y PowerShell | Host Client web; sin vCenter en la versión gratuita | XO Lite y `xe` para un solo host; Xen Orchestra, desplegado aparte, para varios hosts y copias | Aplicación de escritorio |
| Actualizaciones | Versiones mayores sobre cada Debian | Las de Windows Server | Parches de Broadcom (sin soporte en la versión gratuita) | Rama LTS (8.3) | Versiones de mantenimiento de Oracle |
| Compatibilidad | KVM y contenedores LXC | VM Windows y Linux; sin contenedores LXC | Máximo 2 CPU físicas y 8 vCPU por VM (gratuito) | VM Xen; sin contenedores LXC | VM de escritorio |
| Limitaciones | La alta disponibilidad exige 3 nodos y almacenamiento compartido | Coste de licencias | Versión gratuita sin vMotion, DRS ni alta disponibilidad | La gestión de varios hosts y las copias dependen de Xen Orchestra | No está pensado para servidores |

### 3. Contraste

- **Proxmox VE:** requisitos, versiones, licencia AGPLv3 y ciclo de soporte según la wiki oficial; precio de la suscripción en proxmox.com.
- **Hyper-V:** requisitos de hardware según Microsoft Learn.
- **VMware ESXi:** según la base de conocimiento de Broadcom, la versión gratuita 8.0U3e no se gestiona con vCenter, no tiene vMotion, DRS ni alta disponibilidad y no tiene soporte oficial.
- **VMware ESXi:** requisitos según Broadcom TechDocs.
- **XCP-ng:** requisitos, gestión y alta disponibilidad según su documentación oficial. XO Lite solo gestiona un host; para varios hosts hace falta Xen Orchestra. La alta disponibilidad pide, como Proxmox, almacenamiento compartido y se recomiendan 3 hosts.
- **VirtualBox:** licencias según su FAQ oficial.

### 4. Justificación

Se elige **Proxmox VE** por su coste nulo de licencias, por reunir todas las funciones necesarias y porque sus requisitos de alta disponibilidad (3 nodos y almacenamiento compartido) coinciden con el diseño del enunciado.

- **Inconveniente aceptado:** el soporte oficial es de pago.
- **Descartados:**
  - Hyper-V, por el coste de la licencia de Windows Server.
  - VMware ESXi: la versión gratuita no tiene alta disponibilidad ni clúster con vCenter, y la completa es de suscripción.
  - XCP-ng: es libre y pide un clúster parecido, pero gestionar 3 nodos y hacer copias exige desplegar y mantener Xen Orchestra aparte, y no ofrece contenedores. Proxmox integra clúster, copias y contenedores LXC en la misma consola web, con menos piezas que montar en el laboratorio.
  - VirtualBox: no está pensado para servidores y el Extension Pack es de pago para uso comercial.

## Gestión centralizada

Responsable: Alex.

### 1. Necesidades

- Directorio centralizado de usuarios, equipos y grupos para un entorno mixto (clientes Windows y Linux).
- Políticas de seguridad (GPO) en los equipos Windows.
- Servicios internos: DNS y recursos compartidos.
- Despliegue en hardware dedicado o máquina virtual.

La implantación completa del dominio y las políticas corresponde a fases posteriores (RA4 y RA5 de ISO); aquí solo se justifica el sistema.

### 2. Comparación

| Criterio | Windows Server 2025 (AD DS) | Samba AD | FreeIPA |
|---|---|---|---|
| Función | Directorio, GPO, DNS y recursos compartidos | Controlador de dominio compatible con Active Directory | Identidad y autenticación para entornos Linux/UNIX (389 Directory Server, Kerberos MIT, Dogtag, BIND y SSSD) |
| Arquitectura | x64 | Linux | Linux |
| Versión | 2025 (disponible desde el 01/11/2024) | 4.25 (septiembre de 2026) | 4.13.4 |
| Soporte | Estándar hasta el 14/11/2029 y extendido hasta el 15/11/2034 | Cada serie unos 18 meses; la 4.25 hasta marzo de 2028 | Comunidad |
| Origen de la descarga | Centro de evaluación de Microsoft | samba.org o paquetes de la distribución | Paquetes de Fedora, RHEL o Debian, o contenedor |
| Requisitos mínimos oficiales | CPU de 1,4 GHz y 64 bits, 2 GB de RAM (4 GB con escritorio) y 32 GB de disco | IP estática, nombre DNS propio (no `.local`) y nombre de equipo de menos de 15 caracteres | Servidor Linux (Fedora, RHEL o Debian) con IP estática y DNS (los de la distribución) |
| Recursos propuestos para el laboratorio | VM en Proxmox con 4 GB de RAM o más (supuesto) | — | — |
| Licencia y precio | Standard: 1.176 $ (16 núcleos) más CAL | Libre (GNU GPL) | Libre (GPLv3) |
| Administración | Interfaz gráfica y PowerShell | `samba-tool` y herramientas RSAT desde un Windows | Web, CLI y API |
| Actualizaciones | Según el ciclo de vida de Windows Server | Una serie nueva cada seis meses | Las de la distribución |
| Compatibilidad | Nativa con clientes Windows; Linux con SSSD/realmd | Clientes Windows 10/11; nivel funcional de Windows Server 2008 R2 (los superiores son experimentales) | Clientes Linux/UNIX; relaciones de confianza con Active Directory |
| Ventajas | Integración nativa con clientes Windows; gestión centralizada | Sin coste de licencia | Sin coste de licencia; integración nativa con Linux |
| Limitaciones | Coste de licencias; mayor consumo de recursos; las GPO no se aplican a Linux sin herramientas adicionales | Las GPO no funcionan solo con instalar el controlador; nivel funcional limitado | No está pensado para clientes Windows |

### 3. Contraste

- **Windows Server:** integración nativa de AD y GPO en clientes Windows; requisitos, precio y ciclo de vida según Microsoft Learn y microsoft.com.
- **Clientes Linux en AD:** Ubuntu y Red Hat documentan la unión al dominio con `realm join` y SSSD. Ubuntu remite a ADSys como cliente de GPO.
- **Samba AD:** limitaciones de GPO y de nivel funcional según la wiki oficial; ciclo de soporte según su planificación de versiones.
- **FreeIPA:** su web oficial lo define como solución para entornos Linux/UNIX.

### 4. Justificación

Se elige **Windows Server 2025 con Active Directory** por su integración con los clientes Windows, que son mayoría, y por la facilidad de gestión centralizada. Si el reparto final de clientes cambia, esta decisión debe revisarse.

- **Inconvenientes aceptados:** coste de licencias, mayor consumo de recursos y gestión adicional de los equipos Linux, a los que las GPO no llegan sin ADSys.
- **Descartados:** Samba AD (las GPO no funcionan solo con instalarlo y el nivel funcional está limitado a 2008 R2) y FreeIPA (pensado para entornos Linux/UNIX).

## Clientes

Responsables: Mauro y Adam.

### 1. Necesidades

Las necesidades salen de lo que pide cada departamento:

- Compatibilidad con las aplicaciones de cada puesto (Office, ERP, software de producción). Las aplicaciones concretas de cada departamento son un supuesto (ver [01-requisits](01-requisits.md#necesidades-por-departamento)).
- Unión al dominio y directivas de grupo.
- Actualizaciones automáticas y soporte rápido ante incidencias.
- Cifrado de disco.

### 2. Comparación

| Criterio | Windows 11 Pro | Ubuntu Desktop 26.04 LTS | Windows 10 |
|---|---|---|---|
| Función | Puesto de trabajo unido al dominio | Puesto técnico (SSH, scripting, redes) | Puesto de trabajo |
| Arquitectura | x64 y ARM64 | amd64 y arm64 | x64 |
| Versión y soporte | 26H2 (29/09/2026), soporte hasta el 10/10/2028; 25H2 hasta el 13/10/2027 | 26.04.1 LTS; soporte estándar hasta mayo de 2031, hasta mayo de 2036 con Ubuntu Pro | 22H2, sin soporte desde el 14/10/2025 |
| Origen de la descarga | microsoft.com/software-download/windows11 | ubuntu.com/download/desktop | — |
| Requisitos mínimos oficiales | CPU de 64 bits compatible a 1 GHz con 2 núcleos, 4 GB de RAM, 64 GB de almacenamiento, UEFI con arranque seguro y TPM 2.0 | CPU de doble núcleo a 2 GHz, 6 GB de RAM y 25 GB de disco | — |
| Recursos propuestos para el laboratorio | VM con 4 GB de RAM, 64 GB de disco y TPM virtual (supuesto) | VM con 6 GB de RAM y 25 GB de disco (supuesto) | — |
| Licencia y precio | Por equipo (OEM o volumen); 259 € en la Microsoft Store de España | Libre; Ubuntu Pro opcional | — |
| Administración | Unión al dominio y GPO | Unión al dominio con SSSD/realmd; GPO con ADSys | — |
| Actualizaciones | Cada versión tiene 24 meses de soporte | LTS con 5 años de parches | Sin actualizaciones de seguridad |
| Compatibilidad | Office y ERP | No ejecuta el software de gestión de Windows; solo sesión Wayland (sin X11) | — |
| Ventajas | Dominio, GPO y BitLocker | Herramientas Linux nativas; sin licencia | Ninguna frente a Windows 11 |
| Limitaciones | Requisito de TPM 2.0; licencia por equipo | Incompatible con parte del software de los puestos | Sin soporte |

### 3. Contraste por departamento

| Departamento | Sistema | Necesidad que cubre | Inconveniente aceptado | Alternativa descartada |
|---|---|---|---|---|
| Dirección | Windows 11 Pro | Office y consulta de informes del ERP sin incompatibilidades; disco cifrado con BitLocker | Licencia por equipo y requisito de TPM 2.0 | Ubuntu Desktop: no garantiza compatibilidad total con Office |
| Informática | Anfitrión Ubuntu Desktop 26.04 LTS con una VM de Windows 11 Pro | Linux nativo para SSH, scripting y redes; la VM aporta las herramientas RSAT para administrar el dominio y permite reproducir incidencias de los puestos | Mantener dos sistemas y reservar RAM para la VM (16 GB en el anfitrión: 6 GB para Ubuntu y 4 GB para la VM, supuesto) | Todo Ubuntu: no administra el dominio con las herramientas de Windows Server. Todo Windows: pierde la línea de comandos Linux |
| Producción | Windows 11 Pro, unidos al dominio y bloqueados con GPO en los terminales de planta | Software de gestión de producción y de etiquetas que solo funciona en Windows (supuesto) | Una licencia por equipo | Ubuntu Desktop: no ejecuta ese software. Windows 11 Enterprise LTSC: solo por licencia por volumen y Microsoft lo reserva a dispositivos de propósito específico |
| Administración | Windows 11 Pro | ERP, Excel con macros, certificados digitales y facturación electrónica | Cada versión de Windows 11 dura 24 meses y hay que actualizar | Ubuntu Desktop: Excel con macros y certificados no garantizados |
| Comercial | Windows 11 Pro con BitLocker | Portátiles fuera de la oficina: disco cifrado y VPN contra el cortafuegos | Gestionar las claves de recuperación de BitLocker | — |

Ciclo de vida y requisitos contrastados con Microsoft Learn, microsoft.com y ubuntu.com (ver [Fuentes](#fuentes)).

### 4. Justificación

Windows 11 Pro cubre los puestos de trabajo porque el software de gestión de la empresa lo exige y se integra con el dominio. Ubuntu Desktop se reserva para Informática, donde aporta herramientas nativas de red y scripting. **Windows 10 se descarta** en todos los departamentos porque ya no recibe actualizaciones de seguridad.

**Asignación final y cambios respecto al borrador inicial:**

| Departamento | Borrador inicial | Decisión final | Motivo del cambio |
|---|---|---|---|
| Producción | Windows 10 | Windows 11 Pro | Windows 10 no recibe parches desde el 14/10/2025; un terminal de planta sin parches es un riesgo para toda la LAN |
| Comercial | Windows 11 | Windows 11 Pro | Sin cambio; se concreta la edición Pro (dominio y BitLocker) |
| Administración | Windows 11 | Windows 11 Pro | Sin cambio; se concreta la edición Pro |
| Dirección | Anfitrión Windows 11 con VM de Ubuntu | Windows 11 Pro sin VM | Ninguna tarea de Dirección necesita Linux; la VM solo añadiría un sistema más que parchear y RAM consumida |
| Informática | Anfitrión Ubuntu con VM de Windows 10 y 11 | Anfitrión Ubuntu 26.04 LTS con VM de Windows 11 Pro | Se elimina la VM de Windows 10 por falta de soporte; la de Windows 11 cubre las herramientas RSAT y las pruebas |

## Datos

Responsable: Álvaro.

### 1. Necesidades

| Id | Necesidad | Origen |
|---|---|---|
| N1 | Compartir carpetas por departamento con clientes Windows (SMB) y Linux (NFS) | Supuesto: depende del SO de los clientes |
| N2 | Usar los mismos usuarios que el Domain Controller con Windows Server | — |
| N3 | Tolerar la caída de un disco, simular un disco degradado y reconstruirlo | Tasca 6 |
| N4 | Aplicar cuotas de espacio por departamento y por usuario | — |
| N5 | Hacer copias automáticas y restaurarlas en un destino alternativo | Tasca 6 |
| N6 | Avisar del estado de los discos y del volumen | Tasca 7 |
| N7 | Ofrecer almacenamiento NFS a Proxmox para copias de VM e ISOs | Supuesto |
| N8 | Sin coste de licencias | Supuesto: presupuesto N/C |

Hardware disponible: PC dedicado con 16 GB de RAM (supuesto de la Tasca 1). **Medido en la Tasca 2:** TrueNAS irá en PC-02 (Ticnova E70 SFF): Core i3-8100, 8 GB de RAM (ampliable a 32 GB) y un SSD de 256 GB que queda solo para el arranque; los datos necesitan discos adicionales en los 3 SATA libres. Ver [02-inventari](02-inventari.md#2-ordenadores).

### 2. Comparación

| Criterio | TrueNAS Community Edition 25.10.7 | OpenMediaVault 8 | Windows Server 2025 |
|---|---|---|---|
| Funcionalidades | SMB, NFS e iSCSI; ZFS (espejo y RAIDZ); instantáneas, replicación y rsync; cuotas por dataset, usuario y grupo; unión a Active Directory | SMB (Samba standalone), NFS y rsync; RAID mdadm mediante plugin; cuotas en ext4/XFS; instantáneas solo en Btrfs | SMB, NFS, cuotas FSRM, instantáneas VSS, Storage Spaces y Active Directory nativo |
| Versión y arquitectura | 25.10.7 «Goldeye», x86_64; versión recomendada por el fabricante para uso general | 8 «Synchrony», Debian 13, AMD64/ARM64; estable desde diciembre de 2025 | 2025, x64; disponible desde el 01/11/2024 |
| Origen de la descarga | truenas.com/download-truenas-community-edition | openmediavault.org/download.html | Centro de evaluación de Microsoft |
| Requisitos mínimos oficiales | CPU x86_64, 8 GB de RAM, SSD de arranque de 20 GB y 2 discos iguales | 1 GiB de RAM (recomienda 8 GiB o más), 64 bits y disco de sistema aparte | CPU de 1,4 GHz y 64 bits, 2 GB de RAM (4 GB con escritorio), 32 GB de disco y ECC o similar en máquina física |
| Recursos propuestos (supuesto) | 16 GB de RAM, SSD de arranque de 20 GB o más y 2 discos en espejo | 16 GB de RAM, disco de sistema y 2 discos en RAID 1 | 16 GB de RAM, disco de sistema de 32 GB o más y 2 discos en espejo |
| Licencia y precio | Open source, sin coste | GPLv3, gratuito | Standard: 1.176 $ (16 núcleos) más CAL; evaluación de 180 días |
| Soporte y actualizaciones | Comunidad; soporte de pago solo en Enterprise. La versión 27 está en fase RC | Solo comunidad (foro, bugtracker y Discord) | Estándar hasta el 14/11/2029 y extendido hasta el 15/11/2034 |
| Administración | Web: pools, discos, cuotas, instantáneas y replicación | Web; el RAID necesita plugin | Administrador del servidor y PowerShell |
| Compatibilidad | Clientes Windows (SMB), Linux y Proxmox (NFS); dominio AD | Clientes Windows (SMB) y Linux (NFS); unión a AD no documentada | Clientes Windows y Linux; AD nativo |
| Limitaciones | Disco de arranque dedicado; desaconseja RAID por hardware y arranque desde USB; recomienda RAM ECC | Unión a Active Directory no documentada; instantáneas solo en Btrfs | Licencias por núcleo y CAL; la evaluación caduca |

### 3. Contraste

Fuentes oficiales consultadas el 13/10/2026 (detalle en [Fuentes](#fuentes)).

No se afirma que ninguna opción sea «más segura» o «más rápida». La integridad y la recuperación de TrueNAS se comprobarán con evidencias en la Tasca 6 (prueba de disco degradado y restauración).

### 4. Justificación

Se elige **TrueNAS Community Edition 25.10.7** porque es la única opción que cubre las ocho necesidades sin coste de licencias ni plugins:

| Necesidad | Cómo la cubre TrueNAS |
|---|---|
| N1, N7 | SMB y NFS para clientes y Proxmox |
| N2 | Unión al dominio de Windows Server |
| N3 | Espejo ZFS y reconstrucción de disco degradado |
| N4 | Cuotas por dataset, usuario y grupo |
| N5 | Instantáneas, replicación y rsync |
| N6 | Alertas de discos y pools |
| N8 | Open source, sin coste |

Con el supuesto de 16 GB, el PC tendría el doble del mínimo oficial; el PC verificado tiene 8 GB, justo el mínimo (ver [D-05](../decisions/D-05-datos.md)). Se usa la 25.10.7 porque es la que el fabricante recomienda para uso general; la 27 sigue en fase de pruebas.

- **Inconvenientes aceptados:** disco de arranque dedicado que no guarda datos, soporte solo de comunidad, RAM sin ECC en el laboratorio y curva de aprendizaje de ZFS.
- **Descartados:**
  - OpenMediaVault 8: la unión al dominio no aparece en su documentación oficial (N2) y las instantáneas dependen de Btrfs (N5).
  - Windows Server 2025: exige licencias y CAL (incumple N8) y en el laboratorio solo habría 180 días de evaluación.
  - Synology DSM y QNAP QTS: solo funcionan en su propio hardware.

**Integración:** se une al dominio del Domain Controller para la autenticación y ofrece almacenamiento NFS a Proxmox VE para copias de VM e ISOs, coherente con [D-02](../decisions/D-02-virtualizacion.md).

## Datos pendientes (N/C)

Todos los datos de la Tasca 1 están contrastados. Los siguientes solo se pueden obtener sobre el material del aula y se completan en las tareas indicadas:

| Dato | Función | Tasca | Propuesta de comprobación |
|---|---|---|---|
| Versión de RouterOS instalada en los routers del aula | Comunicaciones | Tasca 2 | Ejecutar `/system resource print` en cada router |
| Soporte de VT-x/AMD-V y SLAT en los PC del aula | Virtualización | Tasca 2 | Comprobar en la BIOS/UEFI y con `lscpu` (Linux) o `systeminfo` (Windows) |
| TPM 2.0 y arranque seguro en los equipos cliente | Clientes | Tasca 2 | `tpm.msc` y `msinfo32` en Windows; `ls /dev/tpm*` y `mokutil --sb-state` en Linux |
| CPU, número y tamaño de discos del PC de datos | Datos | Tasca 2 | Inventario con `lsblk`, `smartctl -a` y la ficha del fabricante |
| Hash de las ISO descargadas | Todas | Tasca 5 (instalación) | Comparar el SHA-256 publicado con el calculado con `sha256sum` |

## Fuentes

| Sistema | Versión | URL | Fecha de consulta |
|---|---|---|---|
| MikroTik CHR – requisitos y licencias | RouterOS 7 | https://help.mikrotik.com/docs/spaces/ROS/pages/18350234/Cloud+Hosted+Router+CHR | 19/10/2026 |
| MikroTik – descargas y registro de cambios | 7.23.8 | https://mikrotik.com/download · https://mikrotik.com/download/changelogs | 19/10/2026 |
| MikroTik – anuncio 7.24.5 *stable* (foro oficial) | 7.24.5 | https://forum.mikrotik.com/t/7-24-5-stable-is-released/273494 | 19/10/2026 |
| OPNsense – requisitos de hardware | 26.7 | https://docs.opnsense.org/manual/hardware.html | 19/10/2026 |
| OPNsense – versiones Community Edition | 26.7.7 | https://docs.opnsense.org/CE_releases.html | 19/10/2026 |
| OPNsense – descarga | 26.7 | https://opnsense.org/download/ | 19/10/2026 |
| pfSense CE – requisitos mínimos | 2.9.0 | https://docs.netgate.com/pfsense/en/latest/hardware/minimum-requirements.html | 19/10/2026 |
| pfSense – versiones CE y Plus | 2.9.0 | https://docs.netgate.com/pfsense/en/latest/releases/versions.html | 19/10/2026 |
| pfSense Plus – funciones exclusivas | — | https://docs.netgate.com/pfsense/en/latest/general/plus.html | 19/10/2026 |
| OPNsense – calendario de actualizaciones | 26.7 | https://docs.opnsense.org/manual/updates.html | 19/10/2026 |
| Proxmox VE – requisitos | 9.x | https://pve.proxmox.com/wiki/System_Requirements | 19/10/2026 |
| Proxmox VE – versiones, ciclo de soporte y licencia | 9.2 | https://pve.proxmox.com/wiki/FAQ · https://pve.proxmox.com/wiki/Roadmap | 19/10/2026 |
| Proxmox VE – descarga y suscripción | 9.2 | https://www.proxmox.com/en/downloads · https://proxmox.com | 19/10/2026 · 13/10/2026 |
| Hyper-V – requisitos | Windows Server 2025 | https://learn.microsoft.com/en-us/windows-server/virtualization/hyper-v/host-hardware-requirements | 19/10/2026 |
| VMware ESXi gratuito (Broadcom KB) | 8.0 U3e | https://knowledge.broadcom.com/external/article/399823 | 19/10/2026 |
| VMware ESXi – requisitos de hardware | 8.0 | https://techdocs.broadcom.com/us/en/vmware-cis/vsphere/vsphere/8-0/esx-installation-and-setup/installing-and-setting-up-esxi-install/esxi-requirements-install/esxi-hardware-requirements-install.html | 19/10/2026 |
| XCP-ng – requisitos | 8.3 LTS | https://docs.xcp-ng.org/installation/requirements/ | 19/10/2026 |
| XCP-ng – gestión | 8.3 LTS | https://docs.xcp-ng.org/management/ | 19/10/2026 |
| XCP-ng – alta disponibilidad | 8.3 LTS | https://docs.xcp-ng.org/management/ha/ | 19/10/2026 |
| VirtualBox – licencias y descarga | 7.2.20 | https://www.virtualbox.org/wiki/Licensing_FAQ · https://www.virtualbox.org/wiki/Downloads | 19/10/2026 |
| Windows Server (AD DS) | 2025 | https://learn.microsoft.com · https://microsoft.com | 13/10/2026 |
| Windows Server – evaluación y descarga | 2025 | https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2025 | 19/10/2026 |
| Samba AD – controlador de dominio | 4.25 | https://wiki.samba.org/index.php/Setting_up_Samba_as_an_Active_Directory_Domain_Controller | 19/10/2026 |
| Samba – planificación de versiones | 4.25 | https://wiki.samba.org/index.php/Samba_Release_Planning | 19/10/2026 |
| FreeIPA – descripción, versión y descarga | 4.13.4 | https://www.freeipa.org · https://www.freeipa.org/page/About · https://www.freeipa.org/page/Downloads | 19/10/2026 |
| Ubuntu – SSSD con Active Directory | 26.04 LTS | https://ubuntu.com/server/docs/how-to/sssd/with-active-directory/ | 19/10/2026 |
| Red Hat – conectar RHEL a AD con SSSD | RHEL 9 | https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/9/html/integrating_rhel_systems_directly_with_windows_active_directory/connecting-rhel-systems-directly-to-ad-using-sssd_integrating-rhel-systems-directly-with-active-directory | 19/10/2026 |
| Windows 11 – requisitos | 26H2 | https://www.microsoft.com/en-us/windows/windows-11-specifications | 19/10/2026 |
| Windows 11 Home y Pro – ciclo de vida | 25H2 / 26H2 | https://learn.microsoft.com/en-us/lifecycle/products/windows-11-home-and-pro | 19/10/2026 |
| Windows 11 – descarga | 26H2 | https://www.microsoft.com/en-us/software-download/windows11 | 19/10/2026 |
| Windows 11 Pro – precio (Microsoft Store España) | 26H2 | https://www.microsoft.com/es-es/d/windows-11-pro/dg7gmgf0d8h4 | 19/10/2026 |
| Windows Enterprise LTSC – descripción | LTSC 2024 | https://learn.microsoft.com/en-us/windows/whats-new/ltsc/overview | 19/10/2026 |
| Windows 10 Home y Pro – ciclo de vida | 22H2 | https://learn.microsoft.com/en-us/lifecycle/products/windows-10-home-and-pro | 19/10/2026 |
| Ubuntu – ciclo de versiones | 26.04 LTS | https://ubuntu.com/about/release-cycle | 19/10/2026 |
| Ubuntu Desktop – descarga y requisitos | 26.04.1 LTS | https://ubuntu.com/download/desktop | 19/10/2026 |
| TrueNAS CE – Hardware Guide | 25.10.7 | https://www.truenas.com/docs/scale/gettingstarted/scalehardwareguide/ | 13/10/2026 |
| TrueNAS CE – Software Status | 25.10.7 | https://www.truenas.com/docs/softwarestatus/ | 13/10/2026 |
| TrueNAS CE – Licencia (blog oficial, 12/03/2026) | 25.10.7 | https://www.truenas.com/blog/building-a-bridge-between-community-enterprise | 13/10/2026 |
| TrueNAS CE – descarga | 25.10.7 | https://www.truenas.com/download-truenas-community-edition/ | 19/10/2026 |
| OpenMediaVault – Requisitos | 8 | https://docs.openmediavault.org/en/stable/prerequisites.html | 13/10/2026 |
| OpenMediaVault – Versiones | 8 | https://docs.openmediavault.org/en/stable/releases.html | 13/10/2026 |
| OpenMediaVault – Funcionalidades | 8 | https://docs.openmediavault.org/en/stable/features.html | 13/10/2026 |
| OpenMediaVault – descarga | 8 | https://www.openmediavault.org/download.html | 19/10/2026 |
| Windows Server – Requisitos de hardware | 2025 | https://learn.microsoft.com/en-us/windows-server/get-started/hardware-requirements | 13/10/2026 |
| Windows Server – Precios | 2025 | https://www.microsoft.com/en-us/windows-server/pricing | 13/10/2026 |
| Windows Server – Ciclo de vida | 2025 | https://learn.microsoft.com/en-us/lifecycle/products/windows-server-2025 | 13/10/2026 |

## Uso de IA

Se usó un asistente de IA (Claude) para tres cosas:

1. **Formato:** pasar el estudio del grupo a Markdown, ordenar cada función en los cuatro pasos y detectar huecos frente al enunciado.
2. **Búsqueda de fuentes oficiales** (19/10/2026) para los datos que faltaban: versiones, requisitos, licencias, ciclos de soporte, precios y URL de descarga. Cada dato procede de la página oficial citada en [Fuentes](#fuentes).
3. **Revisión de los motivos de descarte.** Dos motivos del borrador no se sostenían con los datos oficiales y se sustituyeron por otros con fuente:
   - pfSense: el borrador decía «requiere más recursos», pero pide menos RAM que OPNsense. Se cambia por la irregularidad de sus versiones y las funciones reservadas a pfSense Plus.
   - VMware y XCP-ng: el borrador decía «faltan datos oficiales», pero sí los hay. Se cambia por la falta de alta disponibilidad en ESXi gratuito y la dependencia de Xen Orchestra en XCP-ng.

   En Clientes se resolvió la contradicción entre la lista inicial y la tabla de contraste (ver la asignación final).

Datos que aporta la experiencia técnica y no una fuente concreta:
- la administración de MikroTik con Winbox;
- que Hyper-V y XCP-ng no ofrecen contenedores LXC;
- el criterio de priorizar la regularidad de los parches en un cortafuegos;
- quitar la VM de Ubuntu en Dirección.

Cada responsable revisa su sección y la defiende en el debate de clase.
