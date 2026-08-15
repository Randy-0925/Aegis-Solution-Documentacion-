DNS SERVER:

sudo nano /etc/bind/db.aegis.com.do
$TTL    604800
@   IN  SOA  ns1.aegis.com.do. admin.aegis.com.do. (
              4   ; Serial
         604800   ; Refresh
          86400   ; Retry
        2419200   ; Expire
         604800 ) ; Negative Cache TTL
;
@       IN  NS      ns1.aegis.com.do.
ns1     IN  A       10.0.10.2
www     IN  A       1.0.0.18
mail    IN  A       1.0.0.18
@       IN  MX  10  mail.aegis.com.do.
srv     IN  A       10.0.10.2

sudo named-checkzone aegis.com.do /etc/bind/db.aegis.com.do
sudo systemctl restart bind9

sudo resolvectl flush-caches
dig @10.0.10.2 srv.aegis.com.do +short
ping -c3 srv.aegis.com.do
