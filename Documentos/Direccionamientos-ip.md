
10
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


# Santiago

Funcion: Centro de Datos y Servicios Empresariales

| **Departamentos**   | Hosts | Vlans |
| ------------------- | ----- | ----- |
| Centro de Datos     | 15    | 110   |
| Ventas Corporativas | 8     | 130   |
| Administración      | 5     | 140   |
| Servidores          | 1     | 199   |


# La Romana

Funcion: Consultoría e Implementación de Proyectos

| **Departamentos**             | Hosts | Vlans |
| ----------------------------- | ----- | ----- |
| Dirección Regional            | 25    | 210   |
| Consultoría TI                | 7     | 220   |
| Ingeniería de Infraestructura | 57    | 230   |


# Puerto Plata

Funcion: Laboratorio Avanzado de Ciberseguridad

| **Departamentos**    | Hosts | Vlans |
| -------------------- | ----- | ----- |
| DFIR                 | 3     | 310   |
| Malware Research Lab | 4     | 320   |
| Threat Intelligence  | 5     | 330   |
| Red Team             | 8     | 340   |
| Ciberseguridad (SOC) | 15    | 350   |

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
