# D-05 · Servidor de datos

- **Necesidad:** N1–N8 de [03-sistemes](../docs/03-sistemes.md#datos): SMB y NFS, usuarios del dominio, tolerancia a fallo de disco, cuotas, copias y restauración, alertas, NFS para Proxmox y sin coste de licencias.
- **Opciones consideradas:** TrueNAS Community Edition 25.10.7, OpenMediaVault 8, Windows Server 2025, Synology DSM, QNAP QTS.
- **Criterios:** cobertura de N1–N8 sin plugins, licencia, soporte, requisitos frente al hardware disponible (16 GB de RAM supuestos en la Tasca 1; 8 GB medidos en la Tasca 2).
- **Fuentes y pruebas:** documentación oficial de TrueNAS, OpenMediaVault y Microsoft (13/10/2026). La integridad y la recuperación se probarán en la Tarea 6.
- **Opción escogida:** TrueNAS Community Edition 25.10.7.
- **Limitaciones aceptadas:** disco de arranque dedicado, soporte solo de comunidad, RAM sin ECC en el laboratorio, curva de aprendizaje de ZFS.
- **Qué obligaría a revisarla:** que el PC no tenga discos suficientes para el espejo, o que falle la unión al dominio o la prueba de disco degradado.
- **Uso de IA (si lo hubo):** solo formato.
- **Revisión por el inventario (Tasca 2, 20/10/2026):** los PC del aula tienen 8 GB de RAM, justo el mínimo oficial de TrueNAS, y un solo disco de ~500 GB. La decisión se mantiene, pero TrueNAS necesita un PC dedicado (no una VM) y discos adicionales para el espejo, o se simulará con discos virtuales. Ver [02-inventari](../docs/02-inventari.md#8-impacto-en-el-diseño-y-material-que-falta).
