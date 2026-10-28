# D-03 · Gestión centralizada de identidades y equipos

- **Necesidad:** directorio de usuarios, equipos y grupos para clientes Windows y Linux, GPO en Windows, DNS y recursos compartidos.
- **Opciones consideradas:** Windows Server, Samba AD (GPO y soporte oficial más limitados), FreeIPA (pensado para entornos Linux).
- **Criterios:** compatibilidad con los clientes (mayoría Windows), GPO, soporte oficial, licencia y recursos.
- **Fuentes y pruebas:** learn.microsoft.com y microsoft.com; documentación oficial de Red Hat y Ubuntu (SSSD/realmd), consultadas el 13/10/2026. Detalle en [03-sistemes](../docs/03-sistemes.md#gestión-centralizada).
- **Opción escogida:** Windows Server 2025 con Active Directory.
- **Limitaciones aceptadas:** licencias de servidor y CAL, mayor consumo de recursos, las GPO no se aplican a los clientes Linux.
- **Qué obligaría a revisarla:** que el reparto de clientes deje de ser mayoritariamente Windows.
- **Uso de IA (si lo hubo):**
