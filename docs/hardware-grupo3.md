# Documentación hardware — Grupo 3 (aula ASIX)

## Router MikroTik hEX (RB750Gr3)
| Campo | Valor |
|---|---|
| Modelo | MikroTik hEX RB750Gr3, revisión r4 |
| N.º serie | HMF0B3M3EK8 |
| CPU | MIPS 1004Kc V2.15, 4 núcleos @ 880 MHz (mmips) |
| RAM / Flash | 256 MiB / 16 MiB |
| RouterOS | 7.20.8 (long-term), build 2026-01-30; factory 6.46.3; canal stable |
| Firmware | mt7621L 7.20.8 |
| Licencia | nivel 4 |
| Salud | 24,4 V · 27 °C |

### Interfaces
- 5 puertos Gigabit ether1–ether5, MTU 1500 (L2MTU 1596), MACs D0:EA:11:A0:2A:03→:07
- ether1: enlace activo 1 Gbps full-duplex (link-ok, autonegociación)
- ether2–ether5: slaves de switch1 (ether2 con enlace)
- Todos: advertise 10/100/1000 half+full, arp enabled, loop-protect default

## Switches (TP-Link, no gestionables)
| Campo | Switch grande | Switches pequeños |
|---|---|---|
| Modelo | TP-Link TL-SG116 | TP-Link LS1008G (LiteWave) |
| Cantidad | 1 | 2 |
| Puertos | 16 × RJ45 Gigabit (auto-MDI/MDIX) | 8 × RJ45 Gigabit (auto-MDI/MDIX) |
| Tipo | No gestionable (plug and play) | No gestionable (plug and play) |
| Reenvío / tabla MAC | 23,8 Mpps / 8K | 11,9 Mpps / 4K |
| Jumbo frames | 10 KB | 16 KB |
| QoS | 802.1p/DSCP, IGMP snooping | — |
| Carcasa | Metálica, sin ventilador, sobremesa | Plástico, sin ventilador, sobremesa |
| Alimentación | Adaptador 12 V DC / 1 A (máx. 10 W) | Adaptador externo (máx. 3,9 W) |

- Sin VLAN, sin gestión web/CLI/SNMP y sin SFP: la separación de redes se hace en el router (hEX) o en los propios equipos.

## PC 1 (sobremesa horizontal) — APD ALDA+ — rol: cliente Lubuntu
| Componente | Detalle |
|---|---|
| Equipo | APD ALDA+ (versión ALDA+C), dotación GVA · hostname PCG3, usuario admin |
| Placa base | Gigabyte B85M-DS3H-A (chipset Intel B85, LGA1150) |
| CPU | Intel Core i3-4160 (Haswell, 2C/4T, 3,6 GHz) |
| RAM | 8 GB DDR3-1600 (1 módulo en ChannelB-DIMM1; 3 de 4 slots libres, máx. 32 GB) |
| Disco | HDD mecánico Toshiba DT01ACA050, 500 GB (465,8 GiB), 7200 rpm, SATA 6 Gb/s |
| Unidad óptica | Regrabadora DVD ATAPI iHAS124 F |
| Gráficos | Intel HD integrada |
| SO | Lubuntu instalado, con VirtualBox (rol cliente confirmado); versión exacta pendiente (cat /etc/os-release) |
| Red | enp2s0: NIC integrada, MAC 40:8d:5c:27:fe:73 · enxb44bd6c64adc: adaptador Ethernet USB, MAC b4:4b:d6:c6:4a:dc |
| IP | _(pendiente de configurar)_ |
| Fuente de alimentación | ~300 W (estimada para este formato; consumo real del equipo ~30–65 W) |

## PC 2 (torre vertical) — Ticnova E70 SFF — rol: servidor TrueNAS
| Componente | Detalle |
|---|---|
| Equipo | Ticnova E70 SFF (Ticnova Quality Team SL, fabricado en España) |
| Orden fabricación / N.º serie | 701413 / 1720408949 |
| Placa base | ASRock H310CM-HDV (chipset Intel H310, LGA1151v2), BIOS P4.20P |
| CPU | Intel Core i3-8100 (Coffee Lake, 4C/4T, 3,6 GHz) |
| RAM | 1× Hynix 8 GB DDR4-2400 en DDR4_B1 (slot A1 libre, máx. 32 GB) |
| Disco | SSD SATA Silicon Power (SPCC) 256 GB en SATA3_0 (s/n C63807991B6A00225529); SATA3_1–3 libres |
| Gráficos | Intel UHD 630 integrada |
| SO | TrueNAS (previsto) |
| MAC | _(por rellenar)_ |
| IP | 192.168.30.20/24, fija (GW 192.168.30.1, DNS 8.8.8.8; ver [04-disseny](04-disseny.md)) |
| Conectividad | USB 3.0 frontal, Realtek Gigabit con arranque PXE |
| Fuente de alimentación | ~180–300 W (estimada para un SFF; consumo real ~30–65 W) |
| BIOS | Fecha corregida a 21/10/2026 (marcaba 2019); vigilar pila CMOS CR2032 |

## Periféricos (compartidos)
- 1 monitor para los dos PC: se usa con el PC 1; el PC 2 (TrueNAS) se administra por la interfaz web y puede ir sin monitor.
- 1 teclado y 1 ratón compartidos.

### Notas para el despliegue
- TrueNAS (PC 2): 8 GB de RAM es el mínimo justo para ZFS — el segundo módulo DDR4 es muy recomendable. TrueNAS ocupa el disco de arranque en exclusiva: el SSD de 256 GB quedará solo como boot, los datos van en discos adicionales en los 3 SATA libres.
- Lubuntu (PC 1): el i3-4160 + 8 GB lo mueve sobrado; el HDD de 7200 rpm es el cuello de botella más probable (arranque y VM de VirtualBox lentas). Un SSD mejoraría mucho el equipo.
- Pendiente PC 1: versión de Lubuntu (cat /etc/os-release) e IP. El estado de los discos y la velocidad de red salen de los diagnósticos de [02-inventari](02-inventari.md#5-diagnósticos-línea-base).
- Pendiente PC 2: MAC cuando se instale TrueNAS (la IP ya está asignada: 192.168.30.20).
