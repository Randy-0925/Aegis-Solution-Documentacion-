## Aegis Solutions— Infraestructura de Red Empresarial

> **Proyecto Final — Conmutación y Enrutamiento**  
> Profesor: Onel Luis Pelegrino | ITLA 2026

[![Cisco](https://img.shields.io/badge/Cisco-IOS-1BA0D7?style=flat-square&logo=cisco&logoColor=white)](https://www.cisco.com/)
[![PNetLab](https://img.shields.io/badge/Simulación-PNetLab-orange?style=flat-square)](https://pnetlab.com/)
[![OSPF](https://img.shields.io/badge/Enrutamiento-OSPF-green?style=flat-square)]()
[![VPN](https://img.shields.io/badge/Seguridad-IPsec%20VPN-red?style=flat-square)]()
[![VLAN](https://img.shields.io/badge/Segmentación-VLANs-blue?style=flat-square)]()

---

## 📋 Descripción del Proyecto

Diseño e implementación de la infraestructura de red para **Élite Tecnologico /Aegis-Solution**, una empresa del sector financiero y crediticio con presencia en tres sedes geográficas:

| Sede | Rol | Dominio |
|------|-----|---------|
| 🏙️ **Santo Domingo** | Sede Central (Core) | `santodomingo.Solutions` |
| 🌆 **Santiago** | Sede Sucursal | `santiago.Solutions` |
| 🏘️ **La Romana** | Sede Sucursal | `romana.Solutions` |

La arquitectura implementa un modelo **jerárquico de 3 capas** (Núcleo → Distribución → Acceso), con segmentación por VLANs, enrutamiento dinámico OSPF multi-área, VPN IPsec inter-sedes, redundancia HSRP y monitoreo con SNMP/Zabbix.

---

## 👥 Equipo de Trabajo — TI-203

| Nombre | Matrícula | Rol |
|--------|-----------|-----|
| Reylin Santana Ortega | 2025-2253 | Líder / Arquitecto de Red & Seguridad |
| Neury Jorge Montero Mejia | 2025-0829 | Co-Líder / Ingeniería WAN & Seguridad |
|Franchesca Soto Abreu |  2025-1625 | Equipo de Redes / Conectividad L3 |
| Darling Nathali Fortuna García  | 2025-0722 | Equipo Documentación |
|Randy Gabriel Troncoso Tejeda | 2024-2319 | Equipo de Documentacion |


---

## 🏗️ Arquitectura de Red

### Modelo Jerárquico de 3 Capas

```
                        ┌─────────────────┐
                        │   INTERNET/WAN  │
                        │    (Nodo Net)   │
                        └────────┬────────┘
              ┌──────────────────┼──────────────────┐
              │                  │                  │
       ┌──────┴──────┐    ┌──────┴──────┐    ┌──────┴──────┐
       │   R1-Core   │    │  RO-STGO    │    │     R2      │
       │ Santo Dom.  │    │  Santiago   │    │  La Romana  │
       └──────┬──────┘    └──────┬──────┘    └──────┬──────┘
     ┌────────┴────────┐         │                   │
  ┌──┴──┐          ┌───┴──┐  ┌───┴──────┐      ┌────┴────┐
  │SWM1 │          │SWM2  │  │SW-STGO   │      │  SWA13  │
  │(L3) │          │(L3)  │  │  CORE    │      │  (L3)   │
  └──┬──┘          └───┬──┘  └───┬──────┘      └────┬────┘
     │                 │         │                   │
 ┌───┴──┐          ┌───┴──┐  ┌───┴──────┐    ┌──────┴──────┐
 │SWAC1 │          │SWAC3 │  │SW-STGO   │    │SW-ROMANA    │
 │SWAC2 │          │      │  │  AC1     │    │    AC1      │
 └──────┘          └──────┘  └──────────┘    └─────────────┘
```

### Tecnologías Implementadas

| Tecnología | Descripción |
|------------|-------------|
| **OSPF Multi-área** | Enrutamiento dinámico (Área 0 Core, Área 1 SD, Área 2 STGO, Área 3 Romana) |
| **VLANs + 802.1Q** | Segmentación de tráfico por departamento con trunks |
| **VLSM** | Direccionamiento eficiente del espacio 192.168.0.0/16 |
| **VTP** | Propagación centralizada de VLANs por sede |
| **HSRP** | Redundancia de primer salto (Gateway HA) |
| **EtherChannel (LACP)** | Agregación de enlaces y redundancia L2 |
| **STP / PortFast / BPDU Guard** | Prevención de bucles en capa 2 |
| **NAT Overload** | Salida a Internet desde IPs privadas |
| **VPN IPsec** | Túneles cifrados inter-sedes |
| **AAA + RADIUS** | Autenticación centralizada de administradores |
| **SNMP v3 + LLDP** | Monitoreo con Zabbix |
| **NTP** | Sincronización de tiempo en todos los equipos |
| **DHCP Snooping** | Seguridad en asignación dinámica de IPs |
| **Port Security** | Control de acceso físico a switches |
| **ACLs Extendidas** | Filtrado de tráfico inter-VLAN |
| **SSH v2** | Administración remota segura |

---


## 🌐 Esquema de Direccionamiento IP (Resumen)

> Espacio base: `192.168.0.0/16` | Técnica: **VLSM**

### Sede Santo Domingo (Área OSPF 1)

| VLAN | Departamento | Red | Máscara | Gateway |
|------|-------------|-----|---------|---------|
| 10 | RR.HH | 192.168.0.0 | /27 (255.255.255.224) | 192.168.0.1 |
| 11 | Marketing | 192.168.0.32 | /28 | 192.168.0.33 |
| 12 | Soporte TI | 192.168.0.48 | /28 | 192.168.0.49 |
| 13 | Desarrollo | 192.168.0.64 | /26 | 192.168.0.65 |

### Sede Santiago (Área OSPF 2)

| VLAN | Departamento | Red | Máscara |
|------|-------------|-----|---------|
| 14 | Ventas | 192.168.3.0 | /27 |
| 15 | Centro de Datos | 192.168.3.32 | /28 |
| 16 | Administración | 192.168.3.48 | /28 |

### Sede La Romana (Área OSPF 3)

| VLAN | Departamento | Red | Máscara |
|------|-------------|-----|---------|
| 19 | Dpto. 2 | 192.168.2.96 | /26 |
| 20 | Dpto. 1 (Call Center) | 192.168.2.160 | /27 |
| 21 | Dpto. 3 | 192.168.2.192 | /28 |
| 500 | Management | 192.168.2.208 | /28 |

---

## 🔐 Políticas de Seguridad

- **Credenciales de administración:** `admin` / `cisco` (cambiar en producción)
- **SSH v2** habilitado en todos los dispositivos — Telnet deshabilitado
- **Port Security:** Máximo 3 MACs por puerto, violación `restrict` con MAC sticky
- **VPN IPsec:** Túneles cifrados entre las 3 sedes
- **ACLs:** Filtrado inter-VLAN para proteger Centro de Datos y datos financieros
- **BPDU Guard + PortFast:** Habilitado en todos los puertos de acceso de usuario
- **DHCP Snooping:** Activo en VLANs de usuario
- **AAA + RADIUS:** Autenticación centralizada, usuario de emergencia local `soporte`
- **Banner MOTD:** "UNAUTHORIZED ACCESS IS PROHIBITED!" en todos los equipos

---

## 🖥️ Plataforma de Simulación

Este proyecto fue diseñado y validado en **PNetLab** usando:
- **Cisco 7200** (emulado) para routers
- **Cisco Catalyst 3560** (emulado) para switches L3 de distribución/core
- **Cisco Catalyst 2960** (emulado) para switches L2 de acceso
- **Ubuntu/CentOS Server Linux** para servidores DHCP, DNS y RADIUS

---

## 📖 Documentación Completa

Consulta el directorio [`docs/`](./docs/) para:
- Tabla completa de direccionamiento VLSM
- Descripción técnica de todos los equipos
- Estructura organizativa del equipo

---

## 📜 Licencia

Proyecto académico — ITLA 2026 | Conmutación y Enrutamiento  
Prof. Onel Luis Pelegrino/ Empresa Aegis-Solutions 
