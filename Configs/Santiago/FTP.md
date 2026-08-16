# FTP Server — Ubuntu

## 1. Instalar FTP Server

```bash
sudo apt install -y vsftpd
```

---

## 2. Configurar FTP

### Crear copia de seguridad

```bash
sudo cp /etc/vsftpd.conf /etc/vsftpd.conf.bak
```

### Configurar vsftpd

```bash
sudo tee /etc/vsftpd.conf > /dev/null << 'EOF'

listen=YES
listen_ipv6=NO

anonymous_enable=NO
local_enable=YES
write_enable=YES

chroot_local_user=YES
allow_writeable_chroot=YES

pasv_enable=YES
pasv_min_port=40000
pasv_max_port=40100

EOF
```

---

## 3. Crear usuarios FTP

```bash
for u in neury reylin starlin franchesca darling randy; do
    sudo adduser --disabled-password --gecos "" $u
    echo "$u:${u^}2026*" | sudo chpasswd
done
```

### Usuarios creados

| Usuario      | Contraseña        |
| ------------ | ----------------- |
| `neury`      | `Neury2026*`      |
| `reylin`     | `Reylin2026*`     |
| `starlin`    | `Starlin2026*`    |
| `franchesca` | `Franchesca2026*` |
| `darling`    | `Darling2026*`    |
| `randy`      | `Randy2026*`      |

---

## 4. Reiniciar y habilitar el servicio

```bash
sudo systemctl restart vsftpd
sudo systemctl enable vsftpd
sudo systemctl status vsftpd
```

---

## 5. Verificar puerto FTP

Comprobar que el servidor está escuchando en el puerto **21**:

```bash
sudo ss -tlnp | grep :21
```

---

## 6. Instalar cliente FTP

```bash
sudo apt install -y ftp
```

---

## 7. Probar conexión FTP

Conectarse al servidor utilizando su dirección IP:

```bash
ftp 10.0.10.2
```

### Credenciales de prueba

```text
Username: neury
Password: Neury2026*
```

---

## 8. Verificación

Si la conexión es correcta, el servidor debe solicitar las credenciales y permitir el acceso con el usuario configurado.

```text
ftp 10.0.10.2
Name: neury
Password: Neury2026*
```
