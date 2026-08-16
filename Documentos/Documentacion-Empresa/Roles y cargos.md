---
titulo: Roles y Cargos de los Integrantes — Aegis Solutions
empresa: Aegis Solutions (MSSP)
materia: TI-203 — Proyecto Final 2026-C2
equipo: 6 integrantes
estado: Organizado y consolidado
tags:
  - ciberseguridad
  - organigrama
  - proyecto-final
---

# 👥 Roles y Cargos de los Integrantes — Aegis Solutions

> **Equipo:** 6 integrantes. Todos cuentan con usuario activo en la infraestructura (RADIUS, FTP y Correo) 

---

## 📋 Tabla Maestra 

| #   | Integrante         | Cargo Corporativo                      | Nivel     | Equipo Técnico           | Rol Dual |
| --- | ------------------ | -------------------------------------- | --------- | ------------------------ | -------- |
| 1   | **Reylin Santana** | CEO & CISO                             | Liderazgo | Seguridad                | ✅ Sí     |
| 2   | **Neury**          | CTO & Arquitecto de Red                | Liderazgo | Redes y Servicios        | ✅ Sí     |
| 3   | **Starlin**        | Lead Full-Stack Developer              | Ejecución | Redes y Servicios        | ✅ Sí     |
| 4   | **Randy**          | DevOps & Release Engineer              | Ejecución | Doc. y Conf. Básica (L2) | ✅ Sí     |
| 5   | **Franchesca**     | Compliance & Procurement Manager       | Ejecución | Planificación IP         | ✅ Sí     |
| 6   | **Darling**        | Technical Writer & Document Controller | Ejecución | Doc. y Conf. Básica (L2) | ✅ Sí     |

---

## 🏛️ Organigrama Funcional

```
                    ┌─────────────────── ──┐
                    │  LIDERAZGO DIRECTIVO │
                    └──────┬───────┬───────┘
                  ┌────────┘       └────────┐
        ┌─────────┴─────────┐     ┌─────────┴─────────┐
        │ Reylin Santana    │     │ Neury             │
        │ CEO & CISO        │     │ CTO & Arq. de Red │
        │ Seguridad / SOC   │     │ LAN/WAN / DMVPN   │
        └─────────┬─────────┘     └─────────┬─────────┘
                  └──────────┬──────────────┘
                             │
              ┌──────────────┴───────────────────────────┐
              │        NIVEL DE EJECUCIÓN                │
              ├──────────────┬──────────────┬────────────┼──────────────┐
              │              │              │            │              │
    ┌─────────┴────────┐ ┌───┴─────────┐ ┌──┴───────┐ ┌──┴───────┐ ┌────┴──────┐
    │ A. Conf. Básica  │ │ B. Redes y  │ │ C. Segur.│ │ D. Doc. y │ │ (mismo    │
    │    (L2)          │ │   Servicios │ │          │ │   Planif. │ │  equipo)  │
    │ Franchesca       │ │ Neury,      │ │ Reylin   │ │ Randy,    │ │           │
    │                  │ │ Starlin     │ │ (dual)   │ │ Darling   │ │           │
    └──────────────────┘ └────────────┘ └──────────┘ └───────────┘ └───────────┘
                             │
              ┌──────────────┴──────────────┐
              │    SOPORTE OPERATIVO        │
              ├── Starlin (servidores)      │
              └── Franchesca (legal/cotiz.) │
```

---

## 🎖️ Nivel 1 — Liderazgo Directivo

Este equipo reporta a la instancia superior de la directiva empresarial.

| Rol          | Nombre         | Cargo Corporativo           | Responsabilidad                                                                                                        |
| ------------ | -------------- | --------------------------- | ---------------------------------------------------------------------------------------------------------------------- |
| **Líder**    | Reylin Santana | **CEO & CISO**              | Visión estratégica, diseño de arquitecturas de seguridad, implementación de protocolos defensivos y monitoreo del SOC. |
| **Co-Líder** | Neury          | **CTO & Arquitecto de Red** | Diseño de alto nivel de la infraestructura de red, estructuración de topologías, enrutamiento y conectividad WAN.      |

---

## 🛠️ Nivel 2 — Ejecución (Equipos Especializados)

### A. Equipo de Configuración Básica — Estructura L2

| Área             | Nombre     | Cargo                                                     | Responsabilidades                                                                                                   |
| ---------------- | ---------- | --------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------- |
| **Conf. Básica** | Franchesca | **Compliance & Procurement Manager** *(rol técnico dual)* | Configuración de VLANs y puertos de acceso, redundancia de Capa 2 (STP) y administración de los switches de acceso. |

### B. Equipo de Redes y Servicios — Conectividad L3

| Área | Nombre | Cargo | Responsabilidades |
|---|---|---|---|
| **Enrutamiento** | Neury | **CTO & Arquitecto de Red** *(rol dual)* | Protocolos de enrutamiento (OSPF multi-área), traducción de direcciones (NAT) y redundancia de primer salto (HSRP). |
| **Servidores** | Starlin | **Lead Full-Stack Developer** *(rol dual)* | Configuración, despliegue y mantenimiento de servidores empresariales (Web, Correo, FTP, DNS, RADIUS) en la topología. |

### C. Equipo de Seguridad — Protección y Protocolos

| Área | Nombre | Cargo | Responsabilidades |
|---|---|---|---|
| **Seguridad** | Reylin Santana | **CEO & CISO** *(rol dual)* | Despliegue de **DMVPN** para comunicaciones seguras inter-sucursales, Port Security para control de acceso físico y ACLs avanzadas para blindar la red. |


### D. Equipo de Documentación y Planificación IP

| Área | Nombre | Cargo | Responsabilidades |
|---|---|---|---|
| **Planificación IP** | Randy | **DevOps & Release Engineer** *(rol dual)* | Diseño del esquema de direccionamiento (VLSM), tabla de asignación de direcciones y control de versiones (GitHub). |
| **Entregables** | Darling | **Technical Writer & Document Controller** | Diagramas lógicos/físicos, registro de configuraciones, bitácoras de red y manuales de entregables. |

---

## 🔧 Nivel 3 — Soporte Operativo

| Nombre | Área de Soporte | Responsabilidad |
|---|---|---|
| **Starlin** | Servidores | Soporte técnico continuo de servidores y coordinación de proyectos web. |
| **Franchesca** | Legal y Adquisiciones | Registros operativos, normativas empresariales y costes de equipos estructurados y actualizados. |

---

## 📦 Matriz de Responsabilidades por Entregable

| Entregable del proyecto                                 | Responsable(s)       | Estado |
| ------------------------------------------------------- | -------------------- | ------ |
| Topología y diseño LAN/WAN                              | Neury                | ✅      |
| Scripts de Santo Domingo (R-SD, SWM-1/2, SW-10/11/12)   | Reylin + Neury       | ✅      |
| Scripts de La Romana (R-ROMANA, SW-3, SW-9)             | Franchesca + Starlin | ✅      |
| Scripts de Santiago (R-SANTIAGO, SWM-1/Sw-2, servicios) | Starlin + Randy      | ✅      |
| VLSM y tabla de direccionamiento                        | Randy + Darling      | ✅      |
| Documentación y diagramas                               | Darling              | ✅      |
| Servicios de red (DHCP, DNS, FTP, MAIL, RADIUS)         | Starlin              | ✅      |
| Seguridad perimetral (DMVPN, ACLs, Port Security)       | Reylin               | ✅      |

---


