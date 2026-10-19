# D-01 · Sistema de comunicaciones

- **Necesidad:** enrutar y filtrar el tráfico entre la LAN y la WAN, con IDS/IPS, inspección de capa 7 y VPN para accesos remotos.
- **Opciones consideradas:** MikroTik RouterOS, OPNsense, pfSense, Cisco, Fortinet, Palo Alto.
- **Criterios:** funciones de routing y seguridad, licencia y coste recurrente, recursos de hardware, administración.
- **Fuentes y pruebas:** documentación oficial de MikroTik (CHR, registro de cambios), OPNsense (hardware y versiones) y Netgate (requisitos de pfSense), consultadas el 19/10/2026. Detalle en [03-sistemes](../docs/03-sistemes.md#comunicaciones).
- **Opción escogida:** topología mixta: MikroTik para routing y VPN, OPNsense para seguridad perimetral.
- **Limitaciones aceptadas:** dos plataformas que administrar; curva de aprendizaje de MikroTik; OPNsense necesita más CPU y RAM que un router dedicado.
- **Qué obligaría a revisarla:** que el inventario no tenga hardware para OPNsense, o que las pruebas de la Tarea 7 muestren un cuello de botella.
- **Pendiente:** el descarte de pfSense «por requerir más recursos» no se sostiene: según Netgate pide 1 GB de RAM, frente a 3 GB de OPNsense. Hay que buscar otro motivo con fuente o reconsiderarlo.
- **Uso de IA (si lo hubo):** formato y búsqueda de fuentes oficiales; propuso unificar en topología mixta la elección de MikroTik y OPNsense, que el borrador presentaba por separado. Se mantuvo.
