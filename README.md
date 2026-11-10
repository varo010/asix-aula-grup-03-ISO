# ASIX ThinkLab · Grupo 3 · «Naix un sistema: instal·la, configura i vigila'l»

| Dato | Valor |
|---|---|
| Encargo | Infraestructura de pruebas del sistema informático de la empresa |
| Grupo | 3 — Álvaro, Alex, Mauro, Adam, Iván, Juan Pablo |
| LAN | 192.168.30.0/24 |
| WAN del firewall | Pendiente de asignación (172.16.222.0/24) |
| Periodo | 5 oct – 27 nov 2026 |
| Versión | En desarrollo (sin etiqueta de entrega) |

## Alcance

Servidor de comunicaciones, virtualización (Proxmox VE), servidores empresariales, clientes, servidor de datos e infraestructura física (rack, switch, router y cableado).

## Estado

| Tarea del enunciado | Documento | Estado | Responsable |
|---|---|---|---|
| Tasca 0 «Muntatge de l'entorn de proves a l'aula» | [Planificación](lliuraments/Planificacion_Grupo3.pdf) | En curso | Todos |
| Tasca 1 «Necessitats i sistemes» | [01-requisits](docs/01-requisits.md), [03-sistemes](docs/03-sistemes.md), [decisions/](decisions/) | Ejecutado | Todos |
| Tasca 2 «Equips i perifèrics» | [02-inventari](docs/02-inventari.md) | En curso | Mauro |
| Tasca 3 «Rack i interconnexió» | [xarxa-aula](docs/xarxa-aula.md), [diagrames/](diagrames/) | En curso | |
| Tasca 4 «Disseny físic i lògic» | [04-disseny](docs/04-disseny.md), [diagrames/](diagrames/) | Ejecutado | Mauro |
| Tasca 5 «Instal·lació i comunicacions» | [05-installacions](docs/05-installacions.md) | En curso | MikroTik configurado; TrueNAS, Lubuntu y las VM de OPNsense y Debian instalados; nodos Proxmox pve2 y pve3 preparados |
| Tasca 6 «Emmagatzematge i recuperació» | [06-recuperacio](docs/06-recuperacio.md) | Pendiente | |
| Tasca 7 «Rendiment i diagnòstic» | [07-rendiment](docs/07-rendiment.md) | Pendiente | |
| Tasca 8 (Final) | — | Pendiente | |

Estados posibles: Pendiente · En curso · Ejecutado · Simulado · Pendiente de material.

## Índice

- [docs/](docs/) — documentación por tareas
- [decisions/](decisions/) — decisiones justificadas y alternativas
- [incidencies/](incidencies/) — incidencias: síntoma, hipótesis, prueba, solución y retorno
- [diagrames/](diagrames/) — esquemas editables y exportaciones
- [evidencies/](evidencies/) — capturas saneadas y registros
- [lliuraments/](lliuraments/) — actas de revisión y versiones entregadas

## Cómo trabajamos

1. Cada tarea es un *issue* con responsable, resultado esperado y comprobación.
2. Nadie trabaja sobre `main`: rama con nombre descriptivo (`t2-inventario-pcs`, `t3-esquema-fisico`).
3. Commits pequeños y coherentes, con la cuenta propia de cada persona.
4. *Pull request* hacia `main` revisada por un compañero antes de integrar.
5. Al cerrar la tarea se actualiza la tabla de estado de este README.

Nunca se suben contraseñas, tokens, claves privadas, datos personales, ISOs ni discos virtuales. Las ISOs y copias se guardan en el almacenamiento docente y aquí solo se anota su ubicación y su hash.
