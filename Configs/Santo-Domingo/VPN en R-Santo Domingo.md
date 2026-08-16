DMVPN Hub — Santo Domingo
Configuración de la interfaz Tunnel1
enable
configure terminal

interface Tunnel1

description TUNNEL HUB SANTO DOMINGO

ip address 10.1.100.1 255.255.255.0
ip mtu 1400

tunnel source Ethernet0/0
tunnel mode gre multipoint

ip nhrp network-id 2026
ip nhrp map multicast dynamic
ip nhrp authentication Empresa-1

no shutdown

exit
OSPF para el túnel
router ospf 1

network 10.1.100.0 0.0.0.255 area 0
Guardar configuración
end
write memory
