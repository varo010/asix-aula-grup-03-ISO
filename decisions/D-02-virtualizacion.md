# D-02 · Plataforma de virtualización

- **Necesidad:** ejecutar los servidores en VM, probar y recuperar sin riesgo y preparar un clúster de 3 nodos con NAS/SAN, con coste bajo y gestión sencilla.
- **Opciones consideradas:** Proxmox VE, Hyper-V, VMware, XCP-ng, VirtualBox.
- **Criterios:** funciones (VM, contenedores, clúster, alta disponibilidad, copias), licencia y precio, soporte, datos oficiales disponibles.
- **Fuentes y pruebas:** wiki oficial de Proxmox VE, Microsoft Learn, base de conocimiento de Broadcom, documentación de XCP-ng y FAQ de licencias de VirtualBox (13/10/2026 y 19/10/2026). Detalle en [03-sistemes](../docs/03-sistemes.md#virtualización).
- **Opción escogida:** Proxmox VE.
- **Limitaciones aceptadas:** el soporte oficial es de pago; la alta disponibilidad exige 3 nodos y almacenamiento compartido.
- **Qué obligaría a revisarla:** que los PC del aula no soporten virtualización por hardware (Tarea 2), o que no haya 3 nodos ni almacenamiento compartido.
- **Alternativas descartadas:** Hyper-V por licencia de Windows Server; VMware ESXi gratuito por no tener alta disponibilidad ni vCenter (la versión completa es de suscripción); XCP-ng porque para gestionar 3 nodos y hacer copias necesita Xen Orchestra desplegado aparte y no ofrece contenedores; VirtualBox por no estar pensado para servidores.
- **Uso de IA (si lo hubo):** formato y búsqueda de fuentes oficiales; el motivo de descarte de VMware y XCP-ng («faltan datos oficiales») se sustituyó por motivos con fuente.
- **Revisión por el inventario (Tasca 2, 20/10/2026):** los PC del aula (Ticnova E70 SFF, Core i3-4160) tienen VT-x con EPT, así que Proxmox VE es viable y la decisión se mantiene. Limitaciones: 8 GB de RAM y 2 núcleos por nodo (pocas VM a la vez) y sin VT-d (no se pueden pasar dispositivos PCI a las VM). Ver [02-inventari](../docs/02-inventari.md#8-impacto-en-el-diseño-y-material-que-falta).
