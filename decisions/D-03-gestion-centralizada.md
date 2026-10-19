# D-03 · Gestión centralizada de identidades y equipos

- **Necesidad:** directorio de usuarios, equipos y grupos para clientes Windows y Linux, GPO en Windows, DNS y recursos compartidos.
- **Opciones consideradas:** Windows Server con Active Directory, Samba AD (las GPO no funcionan solo con instalarlo; nivel funcional 2008 R2), FreeIPA (pensado para Linux/UNIX).
- **Criterios:** compatibilidad con los clientes (mayoría Windows), GPO, soporte oficial, licencia y recursos.
- **Fuentes y pruebas:** Microsoft Learn y microsoft.com (13/10/2026); wiki oficial de Samba, web de FreeIPA y documentación de Ubuntu y Red Hat sobre SSSD/realmd (19/10/2026). Detalle en [03-sistemes](../docs/03-sistemes.md#gestión-centralizada).
- **Opción escogida:** Windows Server 2025 con Active Directory.
- **Limitaciones aceptadas:** licencias de servidor y CAL, mayor consumo de recursos, las GPO no se aplican a los clientes Linux.
- **Qué obligaría a revisarla:** que el reparto de clientes deje de ser mayoritariamente Windows.
- **Uso de IA (si lo hubo):** formato y búsqueda de fuentes oficiales.
