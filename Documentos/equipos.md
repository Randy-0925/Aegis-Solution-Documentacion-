
---
titulo: Descripción de Equipos — Aegis Solutions
empresa: Aegis Solutions (MSSP)
materia: TI-203 — Proyecto Final 2026-C2
plataforma: PNETLab
estado: Fase 1 completa; Fases 2-3 con scripts creados
tags:
  - ciberseguridad
  - redes
  - proyecto-final
---

# 🛡️ Descripción de Equipos — Aegis Solutions

> **Contexto:** Todos los equipos se emulan en **PNETLab**. La arquitectura sigue un modelo jerárquico adaptado para un proveedor de servicios de seguridad (MSSP), integrando una topología **Hub-and-Spoke mediante DMVPN** para la comunicación cifrada de los Centros de Operaciones (SOC).

---

## 📋 Inventario Consolidado

### Fase 1 — Implementada ✅

| # | Equipo | Sede | Rol | Modelo | Estado |
|---|--------|------|-----|--------|--------|
| 1 | R-SD | Santo Domingo | Core + Hub DMVPN | Cisco 7200 | ✅ Implementado |
| 2 | SWM-1 | Santo Domingo | Distribución L3 (principal) | Catalyst 3560 | ✅ Implementado |
| 3 | SWM-2 | Santo Domingo | Distribución L3 (redundante) | Catalyst 3560 | ✅ Implementado |
| 4 | SW-10 | Santo Domingo | Acceso L2 | Catalyst 2960 | ✅ Implementado |
| 5 | SW-11 | Santo Domingo | Acceso L2 | Catalyst 2960 | ✅ Implementado |
| 6 | SW-12 | Santo Domingo | Acceso L2 | Catalyst 2960 | ✅ Implementado |
| 7 | R-ROMANA | La Romana | Spoke DMVPN | Cisco 7200 | ✅ Implementado |
| 8 | SW-3 | La Romana | Distribución | Catalyst 3560 | ✅ Implementado |
| 9 | SW-9 | La Romana | Acceso L2 | Catalyst 2960 | ✅ Implementado |
| 10 | ISP-NUBE | Nube | ISP (NAT de salida) | Router emulado | ✅ Implementado |

### Fase 2 — Scripts Creados 

| #   | Equipo          | Sede                | Rol                       | Modelo        |
| --- | --------------- | ------------------- | ------------------------- | ------------- |
| 11  | R-SANTIAGO      | Santiago            | Spoke DMVPN               | Cisco 7200    |
| 12  | SW-1 (Santiago) | Santiago            | Distribución L3           | Catalyst 3560 |
| 13  | SW-2 (Santiago) | Santiago            | Acceso L3 / Core local    | Catalyst 3560 |
| 14  | SRV-* (Linux)   | Santiago (VLAN 199) | RADIUS / DNS / NTP / DHCP | Ubuntu/Mate   |

### Fase 3 — Scripts Creados 

| #   | Equipo         | Sede         | Rol                           | Modelo        |
| --- | -------------- | ------------ | ----------------------------- | ------------- |
| 15  | R-PUERTO-PLATA | Puerto Plata | Spoke DMVPN                   | Cisco 7200    |
| 16  | SW-5           | Puerto Plata | Distribución L3               | Catalyst 3560 |
| 17  | sw-30          | Puerto Plata | Acceso L2 (DFIR, Malware)     | Catalyst 2960 |
| 18  | sw-31          | Puerto Plata | Acceso L2 (TH, Red Team, SOC) | Catalyst 2960 |


---

## 🏙️ Sede Santo Domingo (Sede Central / Hub)

**Función:** Administración, SOC e Infraestructura Corporativa.

### 1. Router R-SD — Core & Hub DMVPN

| Campo | Detalle |
|---|---|
| **Tipo** | Router de núcleo (Core Router) |
| **Modelo** | Cisco 7200 (emulado en PNETLab) |
| **Cantidad** | 1 |
| **Función** | Punto central de enrutamiento y **Hub DMVPN (NHRP)**. Conecta el SOC con Internet y los spokes (Santiago, La Romana, Puerto Plata). Termina los túneles cifrados y distribuye tráfico a SWM-1/SWM-2. |
| **Interfaces** | `e0/2` → SWM-1 (10.255.255.1/30) \| `e0/1` → SWM-2 (10.255.255.5/30) \| `e0/0` → Internet/ISP (1.0.0.2/30) \| `Tunnel1` → 10.1.100.1/24 (Hub DMVPN) |
| **OSPF** | Área 0 (Backbone/Túnel) + Área 10 (Santo Domingo) |
| **DHCP** | 7 pools por VLAN (10-70) + exclusiones de gateways HSRP + `ip helper-address` desde SWM-1/SWM-2 |
| **Seguridad** | SSHv2 (RSA 2048), NAT Overload (PAT), ACLs `VPN-TRAFFIC` y `NAT-INTERNAS`, banner MOTD |
| **Credenciales** | `enable secret AEGIS-2026` / `username admin privilege 15 secret AEGIS-2026` |
| **IPsec Key** | `AEGIS-2026-VPN` (pre-shared key para DMVPN) |
| **NHRP Auth** | `AEGIS` |
| **Justificación** | Alta capacidad para múltiples protocolos. Hub central que requiere alta disponibilidad (HA), redundancia de rutas y terminación de túneles seguros para proteger los datos de los clientes. |

### 2. Switch SWM-1 — Distribución L3 (Principal)

| Campo | Detalle |
|---|---|
| **Tipo** | Switch multicapa de distribución (Layer 3) |
| **Modelo** | Cisco Catalyst 3560 (emulado) |
| **Cantidad** | 1 |
| **Función** | Distribución en SD. Interconecta R-SD con la capa de acceso mediante trunks 802.1Q. Realiza **inter-VLAN routing** para los departamentos operativos del SOC. |
| **STP** | Root Bridge (prioridad 24576) — Rapid PVST |
| **HSRP** | **Activo** (prioridad 120 + preempt) para cada VLAN operativa (10-70) |
| **EtherChannel** | Po1 (LACP `mode active`) hacia SWM-2 por e0/1–e0/3 |
| **Uplinks** | `e0/0` → R-SD (10.255.255.2/30) \| `e1/0`–`e1/2` → SW-10 / SW-11 / SW-12 |
| **SVIs (gateways)** | VLAN 10 → .194 \| VLAN 20 → .50 \| VLAN 30 → .226 \| VLAN 40 → .2 \| VLAN 50 → .2 \| VLAN 60 → .2 \| VLAN 70 → .2 |
| **OSPF** | Área 10 (router-id 2.2.2.2) |
| **Credenciales** | `enable secret AEGIS-2026` / `username admin privilege 15 secret AEGIS-2026` |

### 3. Switch SWM-2 — Distribución L3 (Redundante)

| Campo | Detalle |
|---|---|
| **Tipo** | Switch multicapa de distribución (Layer 3) |
| **Modelo** | Cisco Catalyst 3560 (emulado) |
| **Cantidad** | 1 |
| **Función** | Distribución redundante en SD. Backup de SWM-1 para garantizar que el centro de monitoreo no pierda conexión. |
| **STP** | Root Bridge Secundario (prioridad 28672) — Rapid PVST |
| **HSRP** | **Standby** (prioridad 110) de SWM-1 para VLAN 10-70 |
| **EtherChannel** | Po1 (LACP `mode active`) hacia SWM-1 por e0/1–e0/3 |
| **Uplinks** | `e0/0` → R-SD (10.255.255.6/30) \| `e1/0`–`e1/2` → SW-10 / SW-11 / SW-12 |
| **SVIs (gateways)** | VLAN 10 → .195 \| VLAN 20 → .51 \| VLAN 30 → .227 \| VLAN 40 → .3 \| VLAN 50 → .3 \| VLAN 60 → .3 \| VLAN 70 → .3 |
| **OSPF** | Área 10 (router-id 3.3.3.3) |
| **Credenciales** | `enable secret AEGIS-2026` / `username admin privilege 15 secret AEGIS-2026` |

### 4. Switches de Acceso — Capa L2 (SW-10, SW-11, SW-12)

| Campo | Detalle |
|---|---|
| **Tipo / Modelo** | Switch de acceso (Layer 2) / Cisco Catalyst 2960 (emulado) |
| **Cantidad** | 3 |
| **Uplinks** | Doble trunk (`e0/0`–`e0/1`) hacia SWM-1 y SWM-2 (802.1Q, native VLAN 99) |
| **Seguridad** | Port Security (MAC sticky, máx. 2, `restrict`), BPDU Guard, PortFast |
| **Credenciales** | `enable secret AEGIS-2026` / `username admin privilege 15 secret AEGIS-2026` |

**Distribución de VLANs por switch de acceso:**

| Switch | VLANs servidas | Departamentos |
|---|---|---|
| **SW-10** | 10, 20 | Dirección General, RRHH |
| **SW-11** | 30, 40 | Cumplimiento y Auditoría, Soporte Técnico |
| **SW-12** | 50, 60, 70 | Finanzas, Ventas, Marketing |

---

## 🌄 Sede Santiago (Sucursal / Nodo Spoke)

**Función:** Centro de Datos y Servicios Empresariales.

> **Estado:** Scripts creados para R-SANTIAGO, SW-1 y SW-2. Pendientes de integración y prueba completa. Los servidores Linux (VLAN 199) están pendientes.

### 5. Router R-SANTIAGO

| Campo             | Detalle                                                                                                                     |
| ----------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **Tipo / Modelo** | Router de sucursal (Branch Router) / Cisco 7200 (emulado)                                                                   |
| **Cantidad**      | 1                                                                                                                           |
| **Función**       | Punto de salida WAN para Santiago. Conecta con Internet y alimenta al core local. Gestiona NAT, NTP y el túnel Spoke DMVPN. |
| **Interfaces**    | `e0/0` → ISP (1.0.0.6/30) \| `e0/1.99` → SW-1 (10.0.19.1/30, VLAN 99 tránsito) \| `Tunnel1` → 10.1.100.2/24 (Spoke DMVPN)   |
| **OSPF**          | Área 20 (Santiago) + Área 0 (Túnel DMVPN)                                                                                   |
| **DHCP**          | Relay hacia servidor Linux en VLAN 199                                                                                      |
| **Seguridad**     | SSHv2, NAT Overload, ACLs `VPN-TRAFFIC` / `NAT-INTERNAS`, banner MOTD                                                       |
| **Credenciales**  | `enable secret AEGIS-2026` / `username admin privilege 15 secret AEGIS-2026`                                                |
| **IPsec Key**     | `AEGIS-2026-VPN`                                                                                                            |
| **NHRP Auth**     | `AEGIS`                                                                                                                     |
| **Estado**        | Implementado                                                      |
|                   |                                                                                                                             |

### 6. Switch SW-1 (Santiago) — Distribución L3

| Campo | Detalle |
|---|---|
| **Tipo / Modelo** | Switch multicapa / Catalyst 3560 |
| **Función** | Distribución local. Interconecta R-SANTIAGO con SW-2 mediante EtherChannel. |
| **VLANs** | 99 (tránsito WAN), 110 (Centro de Datos), 130 (Ventas Corporativas), 140 (Administración), 199 (Servidores) |
| **EtherChannel** | Po1 (LACP `mode active`) hacia SW-2 |
| **Uplinks** | `e0/0` → R-SANTIAGO (trunk VLAN 99) |
| **STP** | Root Bridge (prioridad 24576) — Rapid PVST |
| **Credenciales** | `enable secret AEGIS-2026` / `username admin privilege 15 secret AEGIS-2026` |
| **Estado** |                                                                                    |

### 7. Switch SW-2 (Santiago) — Acceso L3 / Core Local

| Campo | Detalle |
|---|---|
| **Tipo / Modelo** | Switch multicapa / Catalyst 3560 |
| **Función** | Core local y acceso. Conecta servidores y PCs de las departamentos de Santiago. |
| **VLANs** | 99 (tránsito), 110 (Centro de Datos), 130 (Ventas Corporativas), 140 (Administración), 199 (Servidores) |
| **EtherChannel** | Po1 (LACP `mode active`) hacia SW-1 |
| **STP** | Root Bridge Secundario (prioridad 28672) — Rapid PVST |
| **Puerto Servidor** | `e0/2` → Servidor Linux (VLAN 199, Port Security máx. 5) |
| **Credenciales** | `enable secret AEGIS-2026` / `username admin privilege 15 secret AEGIS-2026` |

### 8. Servidores Linux (Pendientes)

| Campo          | Detalle                                                            |
| -------------- | ------------------------------------------------------------------ |
| **Plataforma** | Ubuntu Server / CentOS (emulado en PNETLab)                        |
| **Ubicación**  | Santiago — VLAN 199 Servidores (10.0.10.0/23)                      |
| **Servicios**  | RADIUS, DNS interno (`aegis.com.do`, 10.0.10.10), NTP, DHCP, FTP,  |
| **Estado**     | ⏳ Pendiente de crear scripts                                       |

---

## 🏖️ Sede La Romana (Sucursal / Nodo Spoke)

**Función:** Consultoría e Implementación de Proyectos.

### 9. Router R-ROMANA

| Campo | Detalle |
|---|---|
| **Tipo / Modelo** | Router de sucursal (Branch Router) / Cisco 7200 (emulado) |
| **Cantidad** | 1 |
| **Función** | Conecta La Romana con Internet. Enruta el tráfico de las VLANs de Consultoría TI, Dirección Regional e Ingeniería de Infraestructura mediante **Router-on-a-Stick** (subinterfaces 802.1Q). |
| **Interfaces** | `e0/1.210` → Dir. Regional (10.0.16.129/26) \| `e0/1.220` → Consultoría TI (10.0.8.1/23) \| `e0/1.230` → Infraestructura (10.0.15.129/25) \| `e0/0` → ISP (1.0.0.10/30) \| `Tunnel1` → 10.1.100.3/24 (Spoke DMVPN) |
| **OSPF** | Área 30 (La Romana) + Área 0 (Túnel DMVPN) |
| **DHCP** | 3 pools por VLAN (210, 220, 230) |
| **Seguridad** | SSHv2, NAT Overload, ACLs `VPN-TRAFFIC` / `NAT-INTERNAS`, banner MOTD |
| **Credenciales** | `enable secret AEGIS-2026` / `username admin privilege 15 secret AEGIS-2026` |
| **IPsec Key** | `AEGIS-2026-VPN` |
| **NHRP Auth** | `AEGIS` |

### 10. Switch SW-3 (La Romana) — Distribución

| Campo | Detalle |
|---|---|
| **Tipo / Modelo** | Switch multicapa / Catalyst 3560 |
| **Función** | Distribución en La Romana. Interconecta R-ROMANA con SW-9. |
| **VLANs** | 210 (Dir. Regional), 220 (Consultoría TI), 230 (Ing. Infraestructura), 99 (nativa) |
| **EtherChannel** | Po1 (LACP `mode active`) hacia SW-9 |
| **Uplinks** | `e0/0` → R-ROMANA (trunk) |
| **STP** | Root Bridge (prioridad 24576) |
| **Credenciales** | `enable secret AEGIS-2026` / `username admin privilege 15 secret AEGIS-2026` |

### 11. Switch SW-9 (La Romana) — Acceso L2

| Campo | Detalle |
|---|---|
| **Tipo / Modelo** | Switch de acceso (Layer 2) / Cisco Catalyst 2960 (emulado) |
| **Función** | Acceso para departamentos de consultoría. |
| **VLANs** | 210 (Dir. Regional), 220 (Consultoría TI), 230 (Ing. Infraestructura) |
| **Seguridad** | Port Security (MAC sticky, máx. 2, `restrict`), BPDU Guard, PortFast |
| **Credenciales** | `enable secret AEGIS-2026` / `username admin privilege 15 secret AEGIS-2026` |

---

## 🚢 Sede Puerto Plata (Nodo Spoke — Laboratorio de Ciberseguridad)

**Función:** Laboratorio Avanzado de Ciberseguridad (DFIR, Malware Research, Threat Intelligence, Red Team, SOC).

> **Estado:** Scripts creados para todos los equipos. Pendientes de integración y prueba completa.

### 12. Router R-PUERTO-PLATA

| Campo | Detalle |
|---|---|
| **Tipo / Modelo** | Router de sucursal (Branch Router) / Cisco 7200 (emulado) |
| **Cantidad** | 1 |
| **Función** | Spoke DMVPN. Enruta tráfico de las 5 VLANs del laboratorio de ciberseguridad hacia la sede central. |
| **Interfaces** | `e0/0` → ISP (1.0.0.14/30) \| `e0/1` → SW-5 (trunk, subinterfaces 802.1Q) \| `Tunnel1` → 10.1.100.4/24 (Spoke DMVPN) |
| **Subinterfaces** | `e0/1.310` → DFIR (10.0.6.1/23) \| `e0/1.320` → Malware (10.0.17.65/28) \| `e0/1.330` → Threat Intel (10.0.4.1/23) \| `e0/1.340` → Red Team (10.0.2.1/23) \| `e0/1.350` → SOC (10.0.0.1/23) |
| **OSPF** | Área 40 (Puerto Plata) + Área 0 (Túnel DMVPN) |
| **DHCP** | 5 pools por VLAN (310-350) |
| **Seguridad** | SSHv2, NAT Overload, ACLs `VPN-TRAFFIC` / `NAT-INTERNAS`, banner MOTD |
| **Credenciales** | `enable secret AEGIS-2026` / `username admin privilege 15 secret AEGIS-2026` |
| **IPsec Key** | `AEGIS-2026-VPN` |
| **NHRP Auth** | `AEGIS` |
| **Estado** |            |

### 13. Switch SW-5 (Puerto Plata) — Distribución L3

| Campo | Detalle |
|---|---|
| **Tipo / Modelo** | Switch multicapa / Catalyst 3560 |
| **Función** | Distribución central de Puerto Plata. Conecta R-PUERTO-PLATA con sw-30 y sw-31. |
| **VLANs** | 310 (DFIR), 320 (Malware), 330 (Threat Intel), 340 (Red Team), 350 (SOC) |
| **Trunks** | `e0/0` → R-PUERTO-PLATA \| `e0/1` → sw-31 \| `e0/2` → sw-30 |
| **STP** | Root Bridge (prioridad 24576) — Rapid PVST |
| **Credenciales** | `enable secret AEGIS-2026` / `username admin privilege 15 secret AEGIS-2026` |
| **Estado** |                                                 |

### 14. Switch sw-30 (Puerto Plata) — Acceso L2 (DFIR, Malware)

| Campo | Detalle |
|---|---|
| **Tipo / Modelo** | Switch de acceso (Layer 2) / Cisco Catalyst 2960 |
| **Función** | Acceso para departamentos DFIR y Malware Research. |
| **VLANs** | 310 (DFIR), 320 (Malware) |
| **Uplink** | `e0/0` → SW-5 (trunk) |
| **Puertos Acceso** | `e0/1` → PC DFIR (VLAN 310) \| `e0/2` → PC Malware (VLAN 320) |
| **Credenciales** | `enable secret AEGIS-2026` / `username admin privilege 15 secret AEGIS-2026` |

### 15. Switch sw-31 (Puerto Plata) — Acceso L2 (TH, Red Team, SOC)

| Campo | Detalle |
|---|---|
| **Tipo / Modelo** | Switch de acceso (Layer 2) / Cisco Catalyst 2960 |
| **Función** | Acceso para Threat Intelligence, Red Team y SOC. |
| **VLANs** | 330 (Threat Intel), 340 (Red Team), 350 (SOC) |
| **Uplink** | `e0/0` → SW-5 (trunk) |
| **Puertos Acceso** | `e0/1` → PC Threat Intel (VLAN 330) \| `e0/2` → PC Red Team (VLAN 340) \| `e0/3` → PC SOC (VLAN 350) |
| **Credenciales** | `enable secret AEGIS-2026` / `username admin privilege 15 secret AEGIS-2026` |

---

## 🌐 ISP-NUBE (Nube Pública)

| Campo | Detalle |
|---|---|
| **Tipo** | Router emulado del ISP / nube pública |
| **Función** | Provee conectividad WAN a las 4 sedes y salida a Internet (NAT Overload hacia el cloud) |
| **Interfaces** | `e0/3` → Santo Domingo (1.0.0.1/30) \| `e0/1` → Santiago (1.0.0.5/30) \| `e0/2` → La Romana (1.0.0.9/30) \| `e1/1` → Puerto Plata (1.0.0.13/30) |
| **Rutas Estáticas** | Rutas hacia subredes de Puerto Plata vía 1.0.0.14 |
| **NAT** | PAT desde `e1/0` (Internet/CLARO) hacia todas las sucursales |

---

## 🗺️ Roadmap de Implementación (Actualizado)

| Fase | Alcance | Estado |
|---|---|---|
| **1** | Santo Domingo (hub + SOC) + La Romana (spoke) + ISP-NUBE | ✅ Implementada |
| **2** | Santiago: R-SANTIAGO (DMVPN, OSPF Área 20) + SW-1 + SW-2 + Servidores Linux | 
| **3** | Puerto Plata: R-PUERTO-PLATA (spoke, OSPF Área 40) + SW-5 + sw-30 + sw-31 | 

---



### Credenciales y claves

| Dispositivo | Credencial Anterior | Credencial Actual (Corregida) |
|---|---|---|
| R-SD | `enable secret cisco123` | `enable secret AEGIS-2026` ✅ |
| NHRP Auth (todos) | `AEGIS` (R-SD) / `Empresa-1` (VPN docs) | `AEGIS` unificado ✅ |
| IPsec PSK (todos) | Variaba por documento | `AEGIS-2026-VPN` unificado ✅ |

### Configuración

| Elemento | Estado Anterior | Estado Actual |
|---|---|---|
| **VTP** | Mencionado pero no configurado | Eliminado de la documentación (VLANs manuales) ✅ |
| **EtherChannel** | "mode on" / "estático" en docs | LACP `mode active` unificado en scripts ✅ |
| **Puerto Plata OSPF** | No definido | Área 40 ✅ |
| **Banner MOTD** | "Falta agregar" | Implementado en todos los routers y switches ✅ |

### Equipos Nuevos (no documentados anteriormente)

| Equipo | Sede | Función | Estado |
|---|---|---|---|
| **SW-5** | Puerto Plata | Distribución L3 | Script creado |
| **sw-30** | Puerto Plata | Acceso L2 (DFIR/Malware) | Script creado |
| **sw-31** | Puerto Plata | Acceso L2 (TH/RedTeam/SOC) | Script creado |
| **SW-1** | Santiago | Distribución L3 | Script creado |
| **SW-2** | Santiago | Acceso L3 / Core local | Script creado |

---

## 📁 Referencias a Scripts

| Sede | Carpeta | Archivos |
|---|---|---|
| Santo Domingo | `Scrip Santo Domingo/` | R-Santo Domingo, SWM-1, SWM-2, SW-10, SW-11, SW-12, VPN en R-Santo Domingo, nota tecnica |
| Santiago | `Scrip Santiago/` | R-Santiago, SWM-1 (SW-1), Sw-2, DHCP, DNS, FTP, MAIL, RADIUS |
| La Romana | `Scrip Romana/` | R-Romana, SW-3, SW-9 |
| Puerto Plata | `Script Puerto Plata/` | R-PUERTOPLATA, SW-5, sw-30, sw-31 |
| Seguridad | `Proyecto Final - Empresa 1/` | Medidas de seguridad para los equipos.md |
