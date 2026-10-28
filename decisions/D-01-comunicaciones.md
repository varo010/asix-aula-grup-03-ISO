# D-01 · Sistema de comunicaciones

- **Necesidad:** enrutamiento, firewall y VPN; seguridad perimetral avanzada (IDS/IPS, inspección de capa 7).
- **Opciones consideradas:** MikroTik, OPNsense, Cisco, Fortinet, Palo Alto.
- **Criterios:** coste (licencias y hardware), funciones de routing y seguridad, soporte y administración.
- **Fuentes y pruebas:** mikrotik.com y opnsense.org. Detalle en [03-sistemes](../docs/03-sistemes.md#comunicaciones).
- **Opción escogida:** topología mixta: MikroTik para el enrutamiento y las VPN, OPNsense para la seguridad perimetral.
- **Limitaciones aceptadas:** administrar dos plataformas distintas; la complejidad técnica de MikroTik; OPNsense necesita mejor hardware.
- **Qué obligaría a revisarla:**
- **Alternativas descartadas:** Cisco: es más conocido, más grande y tiene soporte técnico, pero es muy caro para una empresa pequeña como la nuestra; MikroTik es más rentable y su soporte por ticket es gratuito (sin respuesta inmediata asegurada; las formaciones son de pago). Fortinet y Palo Alto, por el coste de las licencias anuales.
- **Uso de IA (si lo hubo):**
