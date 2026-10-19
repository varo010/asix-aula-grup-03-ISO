# D-04 · Sistemas de los clientes

- **Necesidad:** que cada departamento ejecute sus aplicaciones (Office, ERP, software de producción), con dominio, GPO, actualizaciones y cifrado de disco.
- **Opciones consideradas:** Windows 11 Pro, Ubuntu Desktop 26.04 LTS, Windows 10, Windows 11 Enterprise LTSC.
- **Criterios:** compatibilidad con las aplicaciones, integración con el dominio, ciclo de soporte, licencia y cifrado.
- **Fuentes y pruebas:** requisitos y ciclo de vida de Windows 10/11 (Microsoft Learn, microsoft.com) y de Ubuntu 26.04 LTS (ubuntu.com), consultados el 19/10/2026. Detalle en [03-sistemes](../docs/03-sistemes.md#clientes).
- **Opción escogida:** Windows 11 Pro en Dirección, Administración, Comercial y Producción; anfitrión Ubuntu Desktop 26.04 LTS con VM de Windows 11 Pro en Informática.
- **Limitaciones aceptadas:** licencia por equipo y TPM 2.0; actualizar cada 24 meses; mantener dos sistemas en Informática; gestionar las claves de BitLocker.
- **Qué obligaría a revisarla:** que las aplicaciones reales de algún departamento funcionen en Linux, o que los equipos no cumplan los requisitos de Windows 11.
- **Uso de IA (si lo hubo):** formato y búsqueda de fuentes oficiales; resolvió la contradicción entre la lista inicial y la tabla de contraste: Windows 10 se sustituye por Windows 11 Pro en Producción (sin parches desde el 14/10/2025), se quita la VM de Ubuntu en Dirección (ninguna tarea la necesita) y en Informática se deja solo la VM de Windows 11 Pro.
