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

## PC 1 (horizontal) — APD ALDA+ (pc33-t226) ✔ verificado por dmidecode
| Componente | Detalle |
|---|---|
| Equipo | APD ALDA+ (versión ALDA+C), formato sobremesa horizontal, dotación GVA con DVD |
| Placa base | Gigabyte B85M-DS3H-A (chipset Intel B85, LGA1150) |
| CPU | Intel Core i3-4160 (Haswell, 2C/4T, 3,6 GHz) |
| RAM | 8 GB DDR3-1600 (1 módulo en ChannelB-DIMM1; 3 de 4 slots libres, máx. 32 GB) |
| Disco | ~500 GB (root 57,3 GiB + home 381 GiB + swap 512 MiB); modelo pendiente (lsblk) |
| Gráficos | Intel HD integrada |
| SO | LliureX 21 (21.250729.1), kernel 5.15.0-113-generic, 64 bits |
| Red | Ethernet integrada — eth1, 172.16.222.19 (red del aula) |

## PC 2 (torre vertical) — Ticnova E70 SFF ⚠ pendiente de verificar por comando
| Componente | Detalle |
|---|---|
| Equipo | Ticnova E70 SFF (Ticnova Quality Team SL, fabricado en España) |
| Orden fabricación / N.º serie | 701413 / 1720408949 |
| CPU (por confirmar) | referencia pública del modelo: Intel Core i3-8100 (Coffee Lake, 4C/4T, 3,6 GHz) |
| RAM (por confirmar) | referencia pública: 8 GB DDR4-2400 |
| Gráficos (por confirmar) | Intel UHD 630 |
| Conectividad | USB 3.0 frontal |
| Pendiente | ejecutar en este equipo: sudo dmidecode -t system -t baseboard -t memory; lscpu; free -h; lsblk -d -o NAME,MODEL,SIZE,ROTA |

### Notas
- Mejoras recomendadas PC 1: SSD SATA y segundo módulo DDR3 (activaría dual channel).
- Las specs del PC 2 marcadas "por confirmar" provienen de un ejemplar del mismo modelo documentado públicamente (Geekbench), no de este equipo.
