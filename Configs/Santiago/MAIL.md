4. Correo (Postfix + Dovecot) 

sudo apt install -y postfix dovecot-imapd dovecot-pop3d mailutils

Durante la instalación de Postfix te va a salir una pantalla azul 


Configurar Postfix:


sudo postconf -e "myhostname = mail.aegis.com.do"

sudo postconf -e "mydomain = aegis.com.do"

sudo postconf -e "myorigin = /etc/mailname"

sudo postconf -e "inet_interfaces = all"

sudo postconf -e "mydestination = \$myhostname, aegis.com.do, localhost"

echo "aegis.com.do" | sudo tee /etc/mailname


Crear los usuarios (si no existen aún en esa VM):

for u in neury reylin starlin franchesca darling randy; do

  sudo adduser --disabled-password --gecos "" $u
  
  echo "$u:${u^}2026*" | sudo chpasswd
  
done


Configurar Dovecot para usar Maildir:

sudo sed -i 's/#mail_location = .*/mail_location = maildir:~\/Maildir/' /etc/dovecot/conf.d/10-mail.conf

grep "mail_location" /etc/dovecot/conf.d/10-mail.conf



Reiniciamos ambos servicios:

sudo systemctl restart postfix

sudo systemctl enable postfix

sudo systemctl restart dovecot

sudo systemctl enable dovecot

sudo systemctl status postfix

sudo systemctl status dovecot



Verificar que están escuchando en los puertos correctos:

sudo ss -tlnp | grep -E ':25|:143|:110'

Debes ver :25 (SMTP, Postfix) y :143 (IMAP, Dovecot).

Prueba de envío manual (SMTP con telnet):

sudo apt install -y telnet

telnet localhost 25

Ya adentro, escribe línea por línea (cada Enter envía la línea):

EHLO aegis.com.do

MAIL FROM:<neury@aegis.com.do>

RCPT TO:<reylin@aegis.com.do>

DATA

Subject: prueba correo aegis


Confirmar que llegó al buzón 

sudo ls -la /var/mail/
sudo cat /var/mail/reylin


