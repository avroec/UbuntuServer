### UbuntuServer 22.04
Gary Avendaño Rosado.

Al crear ya la maquina virtual esta es mi configuración:

```bash
sudo nano /etc/hostname
```
```bash
sudo nano /etc/hosts
```
```bash
sudo systemctl reboot
```

### Al reiniciar.

```bash
sudo apt install -y nginx
```
```bash
sudo snap install certbot --classic
```
```bash
sudo mv /etc/nginx/sites-available/defaut /etc/nginx/sites-available/NOMBRE.conf
```
```bash
sudo rm -rf /etc/nginx/sites-enabled/defaut
```
```bash
sudo ln -s /etc/nginx/sites-available/NOMBRE.conf /etc/nginx/sites-enabled/
```
```bash
sudo nginx -t
```
```bash
sudo systemctl restart nginx
```
### Agregar configuración.

```bash
sudo nano /etc/nginx/sites-available/NOMBRE.conf
```

### Code Server.

```bash
curl -fsSL https://code-server.dev/install.sh | sh
```

Abrir este link: [Release Code](https://github.com/coder/code-server/releases)

```bash
wget https://github.com/coder/code-server/releases/download/v4.**/**_amd64.deb
```
```bash
sudo apt install ./code-server_**_amd64.deb
```
```bash
sudo systemctl start code-server@$USER
```
```bash
sudo systemctl enable --now code-server@$USER
```
```bash
sudo nano /etc/nginx/sites-available/NOMBRE.conf
```
```bash
server {
  listen 80;
  listen [::]:80;

  server_name yourdomain.com;
  #server_name system-ip-addres;

  location / {
    proxy_pass http://localhost:8080/;
    proxy_set_header Host $host;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection upgrade;
    proxy_set_header Accept-Encoding gzip;
  }
}
```
```bash
nano ~/.config/code-server/config.yaml
```
```bash
sudo systemctl restart code-server@$USER
```

### Uninstallation or remove Code Server.

```bash
sudo systemctl stop nginx
```
```bash
sudo systemctl stop code-serverf@$USER
```
```bash
sudo apt remove code-server
```
```bash
sudo apt remove nginx
```

### Postgres.

```bash
sudo apt install -y postgresql
```
```bash
systemctl status postgresql
```
```bash
sudo apt install -y postgresql-client
```

### Cómo configurar PostgreSQL en Ubuntu 22.04 LTS.

```bash
sudo nano /etc/postgresql/14/main/pg_hba.conf
```
```bash
# Acceso remoto
host    all             all             all                     scram-sha-256
```
```bash
sudo nano /etc/postgresql/14/main/postgresql.conf
```
```bash
listen_addresses = '*'                  # what IP address(es) to listen on;
```
```bash
sudo systemctl restart postgresql
```
```bash
sudo ufw allow postgresql
```

### pgAdmin en Ubuntu 20.04.

```bash
wget -qO- https://www.pgadmin.org/static/packages_pgadmin_org.pub | sudo apt-key add -
```
```bash
sudo nano /etc/apt/sources.list.d/pgadmin.list
```
```bash
deb https://ftp.postgresql.org/pub/pgadmin/pgadmin4/apt/jammy pgadmin4 main
```
```bash
sudo apt update
```
```bash
sudo apt install -y pgadmin4-web
```
```bash
sudo apt-get install -y apache2
```
```bash
sudo nano /etc/apache2/ports.conf
```
```bash
sudo nano /etc/apache2/sites-available/000-default.conf
```
```bash
sudo service apache2 reload
```
```bash
sudo /usr/pgadmin4/bin/setup-web.sh
```

### Mismo usuario de hostname.

```bash
adduser AQUIUSUARIO
```
```bash
su - postgres
```
```bash
createuser AQUIUSUARIO
```
```bash
createdb db_test -O AQUIUSUARIO
```
```bash
psql
```
```bash
ALTER USER postgres WITH PASSWORD 'MICONTRASEÑA';
```
```bash
ALTER USER postgres WITH SUPERUSER;
```
### Eliminar comandos del terminal.
```bash
echo "" > ~/.bash_history && history -c && exit
```



### Python Config.


Para actualizar a Python 3.** en tu Ubuntu Server 22.04, puedes seguir estos pasos:

1. **Actualizar el sistema**:
   Abre una terminal y ejecuta:
   ```bash
   sudo apt update
   sudo apt upgrade
   ```

2. **Instalar las dependencias necesarias**:
   Necesitarás algunas herramientas para compilar Python. Ejecuta:
   ```bash
   sudo apt install -y build-essential libssl-dev libbz2-dev libzlib1g-dev \
   libncurses5-dev libncursesw5-dev libreadline-dev libsqlite3-dev \
   libgdbm-dev libgdbm-compat-dev libffi-dev liblzma-dev tk-dev \
   libdb5.3-dev libgmp3-dev libmpfr-dev libmpc-dev libgmp-dev
   ```

3. **Descargar Python 3.**:
   Ve a la página oficial de Python (https://www.python.org/downloads/source/) y copia el enlace de la versión 3.14. Luego ejecuta:
   ```bash
   cd /usr/src
   sudo wget https://www.python.org/ftp/python/3.14.0/Python-3.14.0.tgz
   sudo tar xzf Python-3.14.0.tgz
   ```

4. **Compilar e instalar Python**:
   Cambia al directorio de Python y compílalo:
   ```bash
   cd Python-3.14.0
   sudo ./configure --enable-optimizations
   sudo make altinstall
   ```

   **Nota**: Usamos `make altinstall` para evitar sobrescribir la versión predeterminada de Python que viene con Ubuntu.

5. **Verificar la instalación**:
   Después de la instalación, verifica que Python 3.14 esté disponible ejecutando:
   ```bash
   python3.14 --version
   ```

6. **Crear un enlace simbólico (opcional)**:
   Si deseas usar `python3` para referenciar Python 3.14, puedes crear un enlace simbólico:
   ```bash
   sudo ln -s /usr/local/bin/python3.14 /usr/bin/python3
   ```

Con estos pasos, deberías tener Python 3.14 instalado en tu Ubuntu Server 22.04.



Para hacer que Python 3.14 sea la versión predeterminada en tu Ubuntu Server 22.04, puedes seguir estos pasos después de instalar Python 3.14:

1. **Crear un enlace simbólico para `python3`**:
   Si ya has instalado Python 3.14 y deseas que sea la versión utilizada por defecto al ejecutar `python3`, puedes crear un enlace simbólico. Primero, asegúrate de que el enlace no exista ya:

   ```bash
   sudo update-alternatives --install /usr/bin/python3 python3 /usr/local/bin/python3.14 2
      sudo apt install python-is-python3
   ```

   Esto añade Python 3.14 a la lista de alternativas para `python3` y le asigna una prioridad de `2`.

2. **Configurar la versión predeterminada**:
   Puedes usar el siguiente comando para seleccionar la versión que deseas usar por defecto:

   ```bash
   sudo update-alternatives --config python3
   ```

   Esto te mostrará una lista de las versiones de Python 3 instaladas. Ingresa el número correspondiente a Python 3.14 y presiona Enter.

3. **Verificar la configuración**:
   Para asegurarte de que `python3` ahora apunta a Python 3.14, ejecuta:

   ```bash
   python3 --version
   ```

Si todo está correcto, deberías ver que la versión predeterminada es Python 3.14. 

Recuerda que cambiar la versión predeterminada de Python puede afectar a otros programas y scripts que dependen de la versión original que viene con Ubuntu, así que ten cuidado al hacer estos cambios.
