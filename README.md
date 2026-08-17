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
| 🏙️ **Santo Domingo** | Sede Central (Core) | `aegis.com.do/SantoDomingo` |
| 🌆 **Santiago** | Sede Sucursal | `aegis.com.do/Santiago` |
| 🏘️ **La Romana** | Sede Sucursal | `aegis.com.do/La romana` |
| 🌆 **Puerto Plata** | Sede Sucursal |  `aegis.com.do/PuertoPlata` 

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
|Starlin De La Cruz Alonzo| 2025-2247 | Equipo de Marketing | 
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

## Lista de departamentos por sucursales.


# **Sede Principal - Santo Domingo**

Funcion: Administración, SOC e Infraestructura Corporativa

| **Departamentos**        | Hosts | Vlans |
| ------------------------ | ----- | ----- |
| Dirección General<br>    | 20    | 10    |
| Recursos Humanos<br>     | 7     | 20    |
| Cumplimiento y Auditoría | 15    | 30    |
| Soporte Técnico          | 10    | 40    |
| Finanzas                 | 80    | 50    |
| Ventas                   | 110   | 60    |
| Marketing                | 51    | 70    |

<img width="900" height="609" alt="image" src="https://github.com/user-attachments/assets/1ff9580a-275d-4b43-925a-bad7137c8a79" />

# Santiago

Funcion: Centro de Datos y Servicios Empresariales

| **Departamentos**   | Hosts | Vlans |
| ------------------- | ----- | ----- |
| Centro de Datos     | 15    | 110   |
| Ventas Corporativas | 8     | 130   |
| Administración      | 5     | 140   |
| Servidores          | 1     | 199   |

<img width="843" height="567" alt="image" src="https://github.com/user-attachments/assets/3afdef0b-569c-4283-a345-e5ccf41b8956" />


# La Romana

Funcion: Consultoría e Implementación de Proyectos

| **Departamentos**             | Hosts | Vlans |
| ----------------------------- | ----- | ----- |
| Dirección Regional            | 25    | 210   |
| Consultoría TI                | 7     | 220   |
| Ingeniería de Infraestructura | 57    | 230   |

<img width="1078" height="452" alt="image" src="https://github.com/user-attachments/assets/9712795d-87f3-4472-ae9e-06e3e914c37f" />


# Puerto Plata

Funcion: Laboratorio Avanzado de Ciberseguridad

| **Departamentos**    | Hosts | Vlans |
| -------------------- | ----- | ----- |
| DFIR                 | 3     | 310   |
| Malware Research Lab | 4     | 320   |
| Threat Intelligence  | 5     | 330   |
| Red Team             | 8     | 340   |
| Ciberseguridad (SOC) | 15    | 350   |

<img width="812" height="507" alt="image" src="https://github.com/user-attachments/assets/0a87e46f-7d8c-4cf3-a47d-466edb973df7" />


# 1. Tabla VLSM - Direccionamiento Privado (Red: 10.0.0.0/9)

Formula:   ![[Pasted image 20260806194510.png]]
# Sede Principal - Santo Domingo

| **Departamento**     | **VLAN** | **Hosts (+40%)** | **Red Asignada (CIDR)** | **Rango IP Utilizable** | **Broadcast** |
| ------------------------ | ------------ | -------------------- | --------------------------- | --------------------------- | ----------------- |
| Ventas                   | 60           | 154                  | **10.0.13.0/24**            | 10.0.13.1 - 10.0.13.254     | 10.0.13.255       |
| Finanzas                 | 50           | 112                  | **10.0.15.0/25**            | 10.0.15.1 - 10.0.15.126     | 10.0.15.127       |
| Marketing                | 70           | 72                   | **10.0.16.0/25**            | 10.0.16.1 - 10.0.16.126     | 10.0.16.127       |
| Dirección General        | 10           | 28                   | **10.0.16.192/27**          | 10.0.16.193 - 10.0.16.222   | 10.0.16.223       |
| Cumplimiento y Auditoría | 30           | 21                   | **10.0.16.224/27**          | 10.0.16.225 - 10.0.16.254   | 10.0.16.255       |
| Soporte Técnico          | 40           | 14                   | **10.0.17.0/27**            | 10.0.17.1 - 10.0.17.30      | 10.0.17.31        |
| Recursos Humanos         | 20           | 10                   | **10.0.17.48/28**           | 10.0.17.49 - 10.0.17.62     | 10.0.17.63        |


# Sucursal - Santiago

|**Departamento**|**VLAN**|**Hosts (+40%)**|**Red Asignada (CIDR)**|**Rango IP Utilizable**|**Broadcast**|
|---|---|---|---|---|---|
|Servidores|1|279|**10.0.10.0/23**|10.0.10.1 - 10.0.11.254|10.0.11.255|
|Administración|5|196|**10.0.12.0/24**|10.0.12.1 - 10.0.12.254|10.0.12.255|
|Centro de Datos|15|154|**10.0.14.0/24**|10.0.14.1 - 10.0.14.254|10.0.14.255|
|Ventas Corporativas|130|12|**10.0.17.32/28**|10.0.17.33 - 10.0.17.46|10.0.17.47|

# Sucursal - La Romana

| **Departamento**              | **VLAN** | **Hosts (+40%)** | **Red Asignada (CIDR)** | **Rango IP Utilizable**   | **Broadcast** |
| ----------------------------- | -------- | ---------------- | ----------------------- | ------------------------- | ------------- |
| Consultoría TI                | 220      | 308              | **10.0.8.0/23**         | 10.0.8.1 - 10.0.9.254     | 10.0.9.255    |
| Ingeniería de Infraestructura | 230      | 80               | **10.0.15.128/25**      | 10.0.15.129 - 10.0.15.254 | 10.0.15.255   |
| Dirección Regional            | 210      | 35               | **10.0.16.128/26**      | 10.0.16.129 - 10.0.16.190 | 10.0.16.191   |

# Sucursal - Puerto Plata

|**Departamento**|**VLAN**|**Hosts (+40%)**|**Red Asignada (CIDR)**|**Rango IP Utilizable**|**Broadcast**|
|---|---|---|---|---|---|
|Ciberseguridad (SOC)|15|490|**10.0.0.0/23**|10.0.0.1 - 10.0.1.254|10.0.1.255|
|Red Team|8|476|**10.0.2.0/23**|10.0.2.1 - 10.0.3.254|10.0.3.255|
|Threat Intelligence|5|462|**10.0.4.0/23**|10.0.4.1 - 10.0.5.254|10.0.5.255|
|DFIR|3|434|**10.0.6.0/23**|10.0.6.1 - 10.0.7.254|10.0.7.255|
|Malware Research Lab|320|6|**10.0.17.64/28**|10.0.17.65 - 10.0.17.78|10.0.17.79|


# 2. Tabla VLSM - Direccionamiento Público (Red: 1.0.0.0/24)

| **Propósito del Enlace / Servicio**     | **Red Asignada (CIDR)** | **Rango IP Utilizable** | **Broadcast** |
| --------------------------------------- | ----------------------- | ----------------------- | ------------- |
| Enlace ISP ↔ Sede Santo Domingo         | **1.0.0.0/30**          | 1.0.0.1 - 1.0.0.2       | 1.0.0.3       |
| Enlace ISP ↔ Sede Santiago              | **1.0.0.4/30**          | 1.0.0.5 - 1.0.0.6       | 1.0.0.7       |
| Enlace ISP ↔ Sede La Romana             | **1.0.0.8/30**          | 1.0.0.9 - 1.0.0.10      | 1.0.0.11      |
| Enlace ISP ↔ Sede Puerto Plata          | **1.0.0.12/30**         | 1.0.0.13 - 1.0.0.14     | 1.0.0.15      |
| NAT / Servicios Web y Correo (Santiago) | **1.0.0.16/28**         | 1.0.0.17 - 1.0.0.30     | 1.0.0.31      |
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
