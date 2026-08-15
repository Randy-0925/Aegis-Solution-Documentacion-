3. Servidor Ubuntu — IP fija 10.0.10.2/23

sudo nano /etc/netplan/01-netcfg.yaml
network:
  version: 2
  ethernets:
    eth0:
      addresses: [10.0.10.2/23]
      routes:
        - to: default
          via: 10.0.10.1
      nameservers:
        addresses: [10.0.10.2]

sudo netplan apply

4. isc-dhcp-server — excluyendo la .1 (gateway) y la .2 (server)
sudo apt install -y isc-dhcp-server
# /etc/default/isc-dhcp-server
INTERFACESv4="ens3"

# /etc/dhcp/dhcpd.conf
authoritative;
default-lease-time 604800;
max-lease-time 604800;

subnet 10.0.10.0 netmask 255.255.254.0 {
  range 10.0.10.3 10.0.11.250;
  option routers 10.0.10.1;
  option domain-name-servers 10.0.10.2;
  option domain-name "aegis.com.do";
}

subnet 10.0.12.0 netmask 255.255.255.0 {
  range 10.0.12.10 10.0.12.250;
  option routers 10.0.12.1;
  option domain-name-servers 10.0.10.2;
}

subnet 10.0.14.0 netmask 255.255.255.0 {
  range 10.0.14.10 10.0.14.250;
  option routers 10.0.14.1;
  option domain-name-servers 10.0.10.2;
}

subnet 10.0.17.32 netmask 255.255.255.240 {
  range 10.0.17.34 10.0.17.46;
  option routers 10.0.17.33;
  option domain-name-servers 10.0.10.2;
}

sudo systemctl restart isc-dhcp-server
sudo systemctl enable isc-dhcp-server
sudo systemctl status isc-dhcp-server

sudo journalctl -u isc-dhcp-server -f    # mira si llegan DHCPDISCOVER relayed
