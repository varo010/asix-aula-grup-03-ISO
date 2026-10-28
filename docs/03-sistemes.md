# 03 · Comparativa de sistemas

Tasca 1 «Necessitats i sistemes» · RA principales: ISO RA1 a-c y FDM RA4 a-c.

Cada función se analiza en cuatro pasos: necesidades, comparación, contraste y justificación. Las decisiones están en [decisions/](../decisions/).

| Función | Sistema elegido | Responsable |
|---|---|---|
| Comunicaciones | MikroTik y OPNsense combinados (topología mixta) | Iván |
| Virtualización | Proxmox VE | Juan Pablo |
| Gestión centralizada | Windows Server | Alex |
| Clientes | Windows 11 y Ubuntu | Mauro y Adam |
| Datos | TrueNAS Community Edition 25.10.7 | Álvaro |

## Comunicaciones

Responsable: Iván.

### MikroTik

1. **Necesidades:** proveer enrutamiento, firewall básico y gestión de VPN, ya sea con hardware dedicado o virtualizado (CHR).
2. **Comparación:** destaca por su alto rendimiento, protocolos avanzados y licencias de bajo coste sin suscripciones anuales. Sus principales desventajas son una curva de aprendizaje pronunciada y la falta de firewall de nueva generación (NGFW).
3. **Contraste:** según mikrotik.com, su modelo de pago único es más económico que las licencias recurrentes de otras marcas.
4. **Justificación:** se elige por su relación calidad-precio para routing y VPN. Se acepta el inconveniente de su complejidad técnica.

**Frente a Cisco.** Cisco es más conocido y más grande, y tiene soporte técnico. El problema es que es muy caro, y nuestra empresa es pequeña: no tenemos tanto capital para gastar ese dinero. Por eso es más rentable MikroTik: con un MikroTik barato nos podemos apañar. Además, el soporte de MikroTik es gratuito: se abre un ticket sin coste, aunque no asegura una respuesta inmediata. También tiene formaciones, pero son de pago.

### OPNsense

1. **Necesidades:** proveer seguridad perimetral avanzada, firewall de inspección profunda, prevención de intrusiones (IDS/IPS) y VPN, desplegable sobre hardware genérico x86 o entornos virtuales.
2. **Comparación:** destaca por ser software de código abierto y gratuito, con interfaz web muy intuitiva y funciones de seguridad superiores (Suricata, Sensei/Zenarmor). Su principal desventaja es un mayor consumo de CPU y RAM frente a routers dedicados.
3. **Contraste:** según opnsense.org, no tiene coste de licencias de software, lo que reduce mucho el coste total frente a soluciones propietarias con licencias anuales obligatorias.
4. **Justificación:** se elige por su alta seguridad, facilidad de gestión y coste cero en software. Se acepta el inconveniente de necesitar mejor hardware y se descartan alternativas por ser más complejas de administrar (MikroTik) o de pago recurrente elevado (Fortinet, Cisco).

### MikroTik y OPNsense combinados (topología mixta)

1. **Necesidades:** proveer a la vez enrutamiento de alto rendimiento (por ejemplo, varias conexiones WAN o BGP) y seguridad perimetral avanzada (NGFW, IDS/IPS) en la misma infraestructura.
2. **Comparación:** al combinarlos se anulan las debilidades de cada sistema. MikroTik aporta eficiencia en el tráfico de red pesado y bajo coste de hardware, y OPNsense cubre la falta de seguridad de MikroTik con inspección profunda (capa 7) y prevención de intrusos.
3. **Contraste:** separar las funciones (routing y firewall) da un rendimiento corporativo que, en un único equipo «todo en uno» de gama alta (como Fortinet o Palo Alto), tendría un coste prohibitivo en licencias anuales.
4. **Justificación:** se elige dejar el «músculo» de la red a MikroTik y el «cerebro» de la seguridad a OPNsense, para obtener lo mejor de los dos sin comprometer el presupuesto. Se acepta el inconveniente de administrar dos plataformas distintas en lugar de una centralizada.

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
| Requisitos recomendados | 2 GB de RAM para el sistema más la de las VM; almacenamiento redundante; varias NIC | 2GB de RAM, Windows Server, Procesador 64 bits |Al menos 2 núcleos, Procesador x86 64 bits, 4GB de RAM | CPU multinúcleo a 2 GHz, 8 GB de RAM y 70 GB de disco | — |
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

Se elige **Proxmox VE** por su coste nulo de licencias, por reunir todas las funciones necesarias en una sola plataforma sin depender de software aparte. Sus planes de contigencia son apropiados para nuestros requisitos.

- **Inconveniente aceptado:** el soporte oficial es de pago.
- **Descartados:**
  - Hyper-V, por el coste de la licencia de Windows Server.
  - VMware ESXi: la versión gratuita no tiene alta disponibilidad ni clúster con vCenter, y la completa es de suscripción.
  - XCP-ng: es libre y pide un clúster parecido, pero gestionar 3 nodos y hacer copias exige desplegar y mantener Xen Orchestra aparte, y no ofrece contenedores. Proxmox integra clúster, copias y contenedores LXC en la misma consola web, con menos piezas que montar en el laboratorio.
  - VirtualBox: no está pensado para servidores y el Extension Pack es de pago para uso comercial.

### Fuentes

| Sistema | Versión | URL | Fecha de consulta |
|---|---|---|---|
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

## Gestión centralizada

Responsable: Alex. Sistema: Windows Server (Domain Controller).

1. **Necesidades:** proveer un directorio centralizado de usuarios, equipos y grupos para un entorno mixto (clientes Windows y Linux), aplicar políticas de seguridad en los equipos Windows (GPO) y ofrecer servicios internos como DNS y recursos compartidos, ya sea con hardware dedicado o virtualizado (máquina virtual).
2. **Comparación:** destaca por su máxima compatibilidad con clientes Windows, su administración sencilla (interfaz gráfica y PowerShell) y el soporte oficial de Microsoft. Permite integrar equipos Linux mediante SSSD/realmd. Sus principales desventajas son el coste de licencias (servidor más CAL), un mayor consumo de recursos y que las GPO no se aplican a los clientes Linux.
3. **Contraste:** según learn.microsoft.com y microsoft.com, Windows Server ofrece integración nativa con Active Directory y GPO en clientes Windows. La integración de Linux en AD está en la documentación oficial de Red Hat y Ubuntu (SSSD/realmd). Fecha de consulta: 13/10/2026.
4. **Justificación:** se elige por su integración con los clientes Windows, que son mayoría (si el reparto real es otro, hay que ajustarlo), y por su facilidad de gestión centralizada. Se acepta el coste de licencias, el mayor consumo de recursos y la gestión adicional de los equipos Linux. Se descartan Samba AD (GPO y soporte oficial más limitados) y FreeIPA (pensado para entornos Linux).

## Clientes

Responsables: Mauro y Adam.

1. Producción → Windows 10
2. Comercial → Windows 11
3. Administración → Windows 11
4. Dirección → anfitrión Windows 11 con VM de Ubuntu
5. Informática → anfitrión Ubuntu con VM de Windows 10 y 11

### 1. Necesidades

Las necesidades de los clientes salen de lo que pide cada departamento:

- Compatibilidad con las aplicaciones.
- Dominio y directivas de grupo.
- Actualizaciones automáticas y soporte rápido ante incidencias.
- Cifrado de disco.

### 2. Comparación

| Opción | Versión y soporte | Licencia y precio | Ventajas | Limitaciones |
|---|---|---|---|---|
| Windows 11 Pro | 25H2 hasta el 13/10/2027; 26H2, publicada el 29/09/2026, hasta el 10/10/2028 | Licencia por equipo (OEM o volumen); precio N/C | Compatible con Office y con el ERP, se une al dominio, recibe directivas de grupo, BitLocker | Pide TPM 2.0 y arranque seguro; cada versión dura 24 meses |
| Ubuntu Desktop 26.04 LTS | Cinco años, hasta abril de 2031; con Ubuntu Pro, diez años de parches | Libre; Ubuntu Pro opcional | Nativo para scripting, SSH y redes; sin licencia | Solo sesión Wayland (sin X11); no ejecuta el software de gestión de Windows |
| Windows 10 | Retirada fijada por Microsoft el 14/10/2025 | N/C | Ninguna frente a Windows 11 | Sin actualizaciones de seguridad |

### 3. Contraste: sistema de los puestos de trabajo por departamento

| Departamento | Equipos y SO | Necesidad que cubre | Inconveniente aceptado | Alternativa descartada |
|---|---|---|---|---|
| Dirección | Windows 11 Pro | Office y consulta de informes del ERP sin incompatibilidades; disco cifrado con BitLocker | Licencia por equipo y requisito de TPM 2.0 | Ubuntu Desktop: no garantiza compatibilidad total con Office |
| Informática | Ubuntu Desktop 26.04 LTS y 2 con Windows 11 Pro | Linux nativo para SSH, scripting y redes; Windows para las herramientas de administración del dominio | Mantener dos sistemas de cliente | Todo Ubuntu: no administra el dominio de Windows Server con sus herramientas; todo Windows: pierde la línea de comandos Linux |
| Producción | Windows 11 Pro, unidos al dominio y bloqueados con directivas en los terminales de planta | Software de gestión de producción y de etiquetas que solo corre en Windows (supuesto) | Una licencia por equipo | Ubuntu Desktop: no ejecuta ese software; Windows 11 Enterprise LTSC: precio y disponibilidad N/C |
| Administración | Windows 11 Pro | ERP, Excel con macros, certificados digitales y facturación electrónica | Cada versión de Windows 11 dura 24 meses y hay que actualizar | Ubuntu Desktop: Excel con macros y certificados no quedan garantizados |
| Comercial | Windows 11 Pro y BitLocker | Portátiles fuera de la oficina: disco cifrado y VPN contra el cortafuegos | Hay que gestionar las claves de BitLocker | — |

### 4. Justificación

Elegimos una mezcla: Windows para identidades y puestos de trabajo, y Linux o software libre para cortafuegos, virtualización y datos. Así pagamos licencia solo donde el software de gestión de la empresa la exige. Combinamos sistemas porque cada uno cubre una necesidad que el otro no cubre bien.

## Datos

Responsable: Álvaro.

### 1. Necesidades

El servidor de datos debe:

- **N1.** Compartir carpetas por departamento con clientes Windows (SMB) y Linux (NFS). (Supuesto: depende de los SO de los clientes.)
- **N2.** Usar los mismos usuarios que el Domain Controller con Windows Server.
- **N3.** Aguantar la caída de un disco, simular un disco degradado y reconstruirlo. (Tasca 6)
- **N4.** Aplicar cuotas de espacio por departamento y por usuario. (Tasca 6)
- **N5.** Hacer copias automáticas y restaurarlas en un destino alternativo. (Tasca 6)
- **N6.** Avisar del estado de los discos y del volumen. (Tasca 7)
- **N7.** Ofrecer almacenamiento NFS a Proxmox para copias de VM e ISOs. (Supuesto.)
- **N8.** No tener coste de licencias. (Supuesto: presupuesto N/C.)

Hardware disponible: PC dedicado con 16 GB de RAM. CPU, número y tamaño de discos: N/C.

### 2. Comparación

| Criterio | TrueNAS Community Edition 25.10.7 | OpenMediaVault 8 | Windows Server 2025 |
|---|---|---|---|
| Funcionalidades | SMB, NFS e iSCSI; ZFS (espejo y RAIDZ); instantáneas, replicación y rsync; cuotas por dataset, usuario y grupo; unión a Active Directory | SMB (Samba en modo standalone), NFS y rsync; RAID mdadm mediante plugin; cuotas en ext4/XFS; instantáneas solo en Btrfs | SMB, NFS, cuotas FSRM, instantáneas VSS, Storage Spaces y Active Directory nativo |
| Versión y arquitectura | 25.10.7 «Goldeye», x86_64; la recomendada por el fabricante para uso general | 8 «Synchrony», Debian 13, AMD64/ARM64; estable desde diciembre de 2025 | 2025, x64; disponible desde el 1/11/2024 |
| Requisitos mínimos oficiales | CPU x86_64, 8 GB de RAM, SSD de arranque de 20 GB y 2 discos iguales | 1 GiB de RAM (recomienda 8 GiB o más), 64 bits y disco de sistema aparte | CPU de 1,4 GHz y 64 bits, 2 GB de RAM (4 GB con escritorio), 32 GB de disco y ECC o similar en máquina física |
| Recursos propuestos para el laboratorio | 16 GB de RAM, SSD de arranque de 20 GB o más y 2 discos en espejo (supuesto) | 16 GB de RAM, disco de sistema y 2 discos en RAID 1 (supuesto) | 16 GB de RAM, disco de sistema de 32 GB o más y 2 discos en espejo (supuesto) |
| Licencia y precio | Open source, sin coste | GPLv3, gratuito | Standard: 1.176 $ (16 núcleos) más CAL; evaluación de 180 días |
| Soporte y actualizaciones | Comunidad; el soporte de pago solo existe en Enterprise. La versión 27 está en fase RC | Solo comunidad (foro, bugtracker y Discord) | Estándar hasta el 14/11/2029 y extendido hasta el 15/11/2034 |
| Administración | Web: pools, discos, cuotas, instantáneas y replicación | Web; el RAID necesita plugin | Administrador del servidor y PowerShell |
| Limitaciones | Disco de arranque dedicado; desaconseja RAID por hardware y arranque desde USB; recomienda RAM ECC | Unión a Active Directory no documentada; instantáneas solo en Btrfs | Licencias por núcleo y CAL; la evaluación caduca |

### 3. Contraste

Fuentes oficiales, consultadas el 13/10/2026:

- TrueNAS – Hardware Guide: https://www.truenas.com/docs/scale/gettingstarted/scalehardwareguide/
- TrueNAS – Software Status: https://www.truenas.com/docs/softwarestatus/
- TrueNAS – Blog oficial (12/03/2026), licencia de Community Edition: https://www.truenas.com/blog/building-a-bridge-between-community-enterprise
- OpenMediaVault – Requisitos: https://docs.openmediavault.org/en/stable/prerequisites.html
- OpenMediaVault – Versiones: https://docs.openmediavault.org/en/stable/releases.html
- OpenMediaVault – Funcionalidades: https://docs.openmediavault.org/en/stable/features.html
- Microsoft Learn – Requisitos de hardware: https://learn.microsoft.com/en-us/windows-server/get-started/hardware-requirements
- Microsoft – Precios de Windows Server 2025: https://www.microsoft.com/en-us/windows-server/pricing
- Microsoft Learn – Ciclo de vida de Windows Server 2025: https://learn.microsoft.com/en-us/lifecycle/products/windows-server-2025

No afirmamos que ninguna opción sea «más segura» o «más rápida». La integridad y la recuperación de TrueNAS se comprobarán con evidencias en la Tasca 6, con la prueba de disco degradado y la restauración.

### 4. Justificación

Se elige **TrueNAS Community Edition 25.10.7** porque es la única opción que cubre las ocho necesidades sin coste de licencias y sin plugins:

- espejo de discos y disco degradado (N3);
- cuotas (N4);
- instantáneas, replicación y rsync (N5);
- alertas (N6);
- SMB y NFS para clientes y Proxmox (N1 y N7);
- unión al dominio de Windows Server (N2).

El PC tiene 16 GB de RAM, el doble del mínimo oficial. Se usa la 25.10.7 porque es la que el fabricante recomienda para uso general; la 27 sigue en fase de pruebas.

**Inconvenientes aceptados:** necesita un disco de arranque dedicado que no guarda datos, no tiene soporte oficial (solo comunidad), en el laboratorio se usará RAM sin ECC y ZFS tiene curva de aprendizaje.

**Alternativas descartadas:**

- OpenMediaVault 8: la unión al dominio no aparece en su documentación oficial (N2) y las instantáneas dependen de Btrfs (N5).
- Windows Server 2025: exige licencias y CAL (incumple N8) y en el laboratorio solo tendríamos 180 días de evaluación.
- Synology DSM y QNAP QTS: solo funcionan en su propio hardware.

**Combinación:** se une al dominio del Domain Controller para la autenticación y da almacenamiento NFS a Proxmox (supuesto, pendiente de quien lleve Virtualización).
