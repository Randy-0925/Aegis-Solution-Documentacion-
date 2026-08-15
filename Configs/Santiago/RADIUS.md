3. RADIUS (FreeRADIUS) — instalación y prueba completa
Instalar:
sudo apt install -y freeradius freeradius-utils
Configurar el cliente autorizado a consultar (quién puede pedirle autenticación al RADIUS):

sudo nano /etc/freeradius/3.0/clients.conf

Al final del archivo, agregamos:
client santiago-lan {
    ipaddr     = 10.0.0.0/8
    secret     = AEGIS-2026
}

Crear los usuarios:

sudo nano /etc/freeradius/3.0/users

Al principio del archivo, agregamos :

neury      Cleartext-Password := "Neury2026*"
reylin     Cleartext-Password := "Reylin2026*"
starlin    Cleartext-Password := "Starlin2026*"
franchesca Cleartext-Password := "Franchesca2026*"
darling    Cleartext-Password := "Darling2026*"
randy      Cleartext-Password := "Randy2026*"

Validar la sintaxis ANTES de iniciarlo como servicio :

sudo systemctl stop freeradius
sudo freeradius -X

Deja correr ese comando y espera a que en la última línea diga:

Ready to process requests


radtest neury Neury2026* 10.0.10.2 0 AEGIS-2026

Debe responder algo como:
Received Access-Accept Id ... from 10.0.10.2:1812 to 10.0.10.2:...
Si dice Access-Reject, revisa usuario/clave exactos en /etc/freeradius/3.0/users.

Cuando funcione, volvemos a la primera terminal, presiona Ctrl+C para salir del modo debug, y actívalo como servicio normal:

sudo systemctl start freeradius
sudo systemctl enable freeradius
sudo systemctl status freeradius






