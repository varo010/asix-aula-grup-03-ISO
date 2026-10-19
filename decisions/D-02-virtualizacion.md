# D-02 · Plataforma de virtualización

- **Necesidad:** ejecutar los servidores en VM, probar y recuperar sin riesgo y preparar un clúster de 3 nodos con NAS/SAN, con coste bajo y gestión sencilla.
- **Opciones consideradas:** Proxmox VE, Hyper-V, VMware, XCP-ng, VirtualBox.
- **Criterios:** funciones (VM, contenedores, clúster, alta disponibilidad, copias), licencia y precio, soporte, datos oficiales disponibles.
- **Fuentes y pruebas:** wiki oficial de Proxmox VE, Microsoft Learn, base de conocimiento de Broadcom, documentación de XCP-ng y FAQ de licencias de VirtualBox (13/10/2026 y 19/10/2026). Detalle en [03-sistemes](../docs/03-sistemes.md#virtualización).
- **Opción escogida:** Proxmox VE.
- **Limitaciones aceptadas:** el soporte oficial es de pago; la alta disponibilidad exige 3 nodos y almacenamiento compartido.
- **Qué obligaría a revisarla:** que los PC del aula no soporten virtualización por hardware (Tarea 2), o que no haya 3 nodos ni almacenamiento compartido.
- **Pendiente:** el borrador descartaba VMware y XCP-ng por falta de datos oficiales, pero ya los hay. VMware: la versión gratuita no tiene alta disponibilidad ni vCenter. XCP-ng: falta un motivo de descarte con fuente.
- **Uso de IA (si lo hubo):** formato y búsqueda de fuentes oficiales; detectó que el motivo de descarte de VMware y XCP-ng había quedado desfasado.
