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

## PC 1 (sobremesa horizontal) — APD ALDA+ — rol: cliente Lubuntu
| Componente | Detalle |
|---|---|
| Equipo | APD ALDA+ (versión ALDA+C), dotación GVA, con DVD |
| Placa base | Gigabyte B85M-DS3H-A (chipset Intel B85, LGA1150) |
| CPU | Intel Core i3-4160 (Haswell, 2C/4T, 3,6 GHz) |
| RAM | 8 GB DDR3-1600 (1 módulo en ChannelB-DIMM1; 3 de 4 slots libres, máx. 32 GB) |
| Disco | ~500 GB (modelo y HDD/SSD por confirmar con lsblk) |
| Gráficos | Intel HD integrada |
| SO | Lubuntu (previsto; actualmente LliureX 21, kernel 5.15) |
| MAC | _(por rellenar)_ |
| IP | _(por rellenar; actual de aula: 172.16.222.19)_ |

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
| IP | _(por rellenar)_ |
| Conectividad | USB 3.0 frontal, Realtek Gigabit con arranque PXE |
| BIOS | Fecha corregida a 21/10/2026 (marcaba 2019); vigilar pila CMOS CR2032 |

### Notas para el despliegue
- TrueNAS (PC 2): 8 GB de RAM es el mínimo justo para ZFS — el segundo módulo DDR4 es muy recomendable. TrueNAS ocupa el disco de arranque en exclusiva: el SSD de 256 GB quedará solo como boot, los datos van en discos adicionales en los 3 SATA libres.
- Lubuntu (PC 1): instalar en SSD si se añade uno; el i3-4160 + 8 GB lo mueve sobrado.
- Pendiente: modelo del disco del PC 1 (lsblk -d -o NAME,MODEL,SIZE,ROTA) y MAC/IP de ambos cuando se configuren.
