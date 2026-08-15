! ############################################################
! CONFIGURACION ISP (NUBE PUBLICA)
! ############################################################

enable
configure terminal

hostname ISP-NUBE
no ip domain-lookup

! --- Conexion a SANTO DOMINGO (Hub) ---
interface Ethernet0/3
 description ENLACE A SANTO DOMINGO
 ip address 1.0.0.1 255.255.255.252
 no shutdown

! --- Conexion a SANTIAGO (Spoke 1) ---
interface Ethernet0/1
 description ENLACE A SANTIAGO
 ip address 1.0.0.5 255.255.255.252
 no shutdown

! --- Conexion a LA ROMANA (Spoke 2) ---
interface Ethernet0/2
 description ENLACE A LA ROMANA
 ip address 1.0.0.9 255.255.255.252
 no shutdown

! --- Conexion a PUERTO PLATA (Spoke 3) ---
interface Ethernet1/1
 description ENLACE A PUERTO PLATA
 ip address 1.0.0.13 255.255.255.252
 no shutdown

end
write memory



configure terminal

ip route 10.0.0.0 255.255.254.0 1.0.0.14
ip route 10.0.2.0 255.255.254.0 1.0.0.14
ip route 10.0.4.0 255.255.254.0 1.0.0.14
ip route 10.0.6.0 255.255.254.0 1.0.0.14
ip route 10.0.17.64 255.255.255.240 1.0.0.14

! --- 1. Interfaz hacia la Nube  ---
interface Ethernet1/0
 description ENLACE A INTERNET CLOUD (CLARO)
 ! Pedimos IP automáticamente a tu red física
 ip address dhcp
 ip nat outside
 no shutdown
exit

! --- 2. Interfaces hacia tus Sucursales (Internas) ---

interface Ethernet0/0
 description Hacia el Web-Server
 ip nat inside

interface Ethernet0/1
 description Hacia R-SANTIAGO
 ip nat inside

interface Ethernet0/3
 description Hacia R-SD
 ip nat inside

interface Ethernet0/2
 description Hacia la sucursal de Romana
 ip nat inside

interface e1/1
description Hacia la sucursal de Puerto plata
ip nat inside

! --- 3. Lista de Acceso y Regla de Traducción (PAT) ---
access-list 100 permit ip any any

! Aplicamos la sobrecarga (Overload) hacia el puerto e1/0
ip nat inside source list 100 interface Ethernet1/0 overload

end
write memory
