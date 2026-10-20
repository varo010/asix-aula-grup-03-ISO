# D-01 · Sistema de comunicaciones

- **Necesidad:** enrutar y filtrar el tráfico entre la LAN y la WAN, con IDS/IPS, inspección de capa 7 y VPN para accesos remotos.
- **Opciones consideradas:** MikroTik RouterOS, OPNsense, pfSense, Cisco, Fortinet, Palo Alto.
- **Criterios:** funciones de routing y seguridad, licencia y coste recurrente, recursos de hardware, administración.
- **Fuentes y pruebas:** documentación oficial de MikroTik (CHR, registro de cambios), OPNsense (hardware y versiones) y Netgate (requisitos de pfSense), consultadas el 19/10/2026. Detalle en [03-sistemes](../docs/03-sistemes.md#comunicaciones).
- **Opción escogida:** topología mixta: MikroTik para routing y VPN, OPNsense para seguridad perimetral.
- **Limitaciones aceptadas:** dos plataformas que administrar; curva de aprendizaje de MikroTik; OPNsense necesita más CPU y RAM que un router dedicado.
- **Qué obligaría a revisarla:** que el inventario no tenga hardware para OPNsense, o que las pruebas de la Tarea 7 muestren un cuello de botella.
- **Alternativas descartadas:** Cisco, Fortinet y Palo Alto por licencias recurrentes. pfSense CE pide menos RAM que OPNsense, pero se descarta porque sus versiones son irregulares (de la 2.7.2 a la 2.8.0 pasaron casi 18 meses) y Netgate reserva funciones a pfSense Plus.
- **Uso de IA (si lo hubo):** formato, búsqueda de fuentes y revisión del descarte de pfSense (el motivo del borrador, «más recursos», contradecía la fuente oficial y se cambió); propuso unificar en topología mixta la elección de MikroTik y OPNsense, que el borrador presentaba por separado. Se mantuvo.
- **Revisión por el inventario (Tasca 2, 19/10/2026):** el router del aula es un MikroTik hEX (RB750Gr3), MMIPS con 256 MB de RAM. OPNsense y el GNU/Linux con nftables necesitan x86-64, así que se instalarán en un PC Ticnova E70 (x86-64) o en una VM de Proxmox. Medido el 20/10/2026: RouterOS 7.20.8 long-term, CPU al 0 % y 199,8 MiB de RAM libres en reposo. Ver [02-inventari](../docs/02-inventari.md#8-impacto-en-el-diseño-y-material-que-falta).
