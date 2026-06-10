# Guía de Configuración — Ubuntu Server 22.04 LTS

**Autor:** Gary Avendaño Rosado

Este documento es una guía paso a paso para configurar un servidor Ubuntu 22.04 LTS desde cero. Cubre la configuración inicial del sistema, el servidor web NGINX, Code Server como IDE en la nube, PostgreSQL como base de datos, pgAdmin como cliente de administración web, y la instalación de Python desde el código fuente.

> **Nota:** Todos los comandos deben ejecutarse como usuario con privilegios `sudo`. Reemplaza los valores entre `< >` con tus propios datos (dominio, usuario, contraseña, etc.).

---

## Tabla de Contenidos

1. [Configuración Inicial del Sistema](#1-configuración-inicial-del-sistema)
2. [Instalación y Configuración de NGINX](#2-instalación-y-configuración-de-nginx)
3. [Code Server (IDE en la Nube)](#3-code-server-ide-en-la-nube)
4. [PostgreSQL](#4-postgresql)
5. [pgAdmin 4 (Administración Web de PostgreSQL)](#5-pgadmin-4-administración-web-de-postgresql)
6. [Configuración de PostgreSQL](#6-configuración-de-postgresql)
7. [Python (Instalación desde Código Fuente)](#7-python-instalación-desde-código-fuente)

---

## 1. Configuración Inicial del Sistema

Después de crear la máquina virtual, es importante configurar el nombre del host y los alias de red para que el servidor sea fácilmente identificable en la red.

### 1.1 Cambiar el nombre del hostname

Edita el archivo `/etc/hostname` para asignar un nombre descriptivo a tu servidor. Este nombre se mostrará en la línea de comandos y será utilizado para identificar la máquina en la red.

```bash
sudo nano /etc/hostname
```

Reemplaza el contenido con el nombre que desees, por ejemplo: `mi-servidor`.

### 1.2 Configurar el archivo hosts

El archivo `/etc/hosts` asocia direcciones IP con nombres de host. Debes agregar una entrada que vincule la IP del servidor con su nombre para que la resolución local funcione correctamente.

```bash
sudo nano /etc/hosts
```

Asegúrate de que exista una línea como la siguiente (reemplaza los valores según tu configuración):

```
127.0.1.1    mi-servidor
```

### 1.3 Reiniciar el servidor

Para que los cambios de hostname surtan efecto, reinicia la máquina:

```bash
sudo systemctl reboot
```

---

## 2. Instalación y Configuración de NGINX

NGINX es un servidor web de alto rendimiento que usaremos como proxy inverso para nuestros servicios. En esta sección lo instalaremos y configuraremos con HTTPS usando Certbot (Let's Encrypt).

### 2.1 Instalar NGINX

Instala NGINX desde los repositorios oficiales de Ubuntu:

```bash
sudo apt install -y nginx
```

### 2.2 Instalar Certbot

Certbot es la herramienta oficial de Let's Encrypt para obtener y renovar certificados SSL/TLS gratuitos. Lo instalamos via Snap:

```bash
sudo snap install certbot --classic
```

### 2.3 Renombrar el archivo de configuración por defecto

NGINX incluye un sitio de configuración por defecto. Lo renombramos a un nombre más descriptivo para nuestro proyecto:

```bash
sudo mv /etc/nginx/sites-available/default /etc/nginx/sites-available/<tu-dominio>.conf
```

### 2.4 Actualizar el enlace simbólico en sites-enabled

Eliminamos el enlace antiguo y creamos uno nuevo que apunte a nuestro archivo de configuración renombrado:

```bash
sudo rm /etc/nginx/sites-enabled/default
sudo ln -s /etc/nginx/sites-available/<tu-dominio>.conf /etc/nginx/sites-enabled/
```

> **Explicación:** NGINX lee los sitios activos desde `sites-enabled/`. Este directorio contiene enlaces simbólicos (shortcuts) a los archivos de configuración reales que están en `sites-available/`. Así puedes desactivar un sitio simplemente eliminando el enlace, sin borrar la configuración.

### 2.5 Verificar la sintaxis de la configuración

Antes de aplicar cualquier cambio, siempre verifica que la configuración no tenga errores de sintaxis:

```bash
sudo nginx -t
```

Si el comando reporta errores, revísalos antes de continuar. **Nunca reinicies NGINX si la sintaxis no es válida**, ya que el servicio no arrancará.

### 2.6 Reiniciar NGINX

Aplica la nueva configuración reiniciando el servicio:

```bash
sudo systemctl restart nginx
```

### 2.7 Obtener certificado SSL con Certbot

Una vez que tu dominio apunte correctamente a la IP del servidor y NGINX esté funcionando, ejecuta Certbot para obtener el certificado HTTPS. Este comando configurará automáticamente NGINX para usar el certificado:

```bash
sudo certbot --nginx -d <tu-dominio.com> -d www.<tu-dominio.com>
```

Certbot también instala un timer de systemd que renueva automáticamente los certificados antes de que expiren. Puedes verificar que la renovación automática funciona con:

```bash
sudo certbot renew --dry-run
```

### 2.8 Editar la configuración del sitio

Para personalizar la configuración de NGINX (agregar proxies, rutas, etc.):

```bash
sudo nano /etc/nginx/sites-available/<tu-dominio>.conf
```

---

## 3. Code Server (IDE en la Nube)

Code Server es una versión de Visual Studio Code que se ejecuta en el servidor y se accede desde el navegador. Esto te permite tener un entorno de desarrollo completo accesible desde cualquier dispositivo con un navegador web.

### 3.1 Instalar Code Server

Puedes instalar Code Server de dos maneras:

**Opción A — Script automático (recomendado):**

```bash
curl -fsSL https://code-server.dev/install.sh | sh
```

**Opción B — Descargar el paquete .deb manualmente:**

Visita la página de releases para obtener la URL de la última versión:
[https://github.com/coder/code-server/releases](https://github.com/coder/code-server/releases)

Descarga el paquete correspondiente a tu arquitectura (amd64 para la mayoría de servidores):

```bash
wget https://github.com/coder/code-server/releases/download/v<VERSION>/code-server_<VERSION>_amd64.deb
```

Instala el paquete descargado:

```bash
sudo apt install ./code-server_<VERSION>_amd64.deb
```

> **Nota:** Reemplaza `<VERSION>` con el número de versión real, por ejemplo `4.96.4`.

### 3.2 Iniciar y habilitar Code Server

Arranca el servicio y configúralo para que se inicie automáticamente con el sistema:

```bash
sudo systemctl start code-server@$USER
sudo systemctl enable --now code-server@$USER
```

> **Explicación:** El símbolo `$USER` es una variable del sistema que contiene tu nombre de usuario actual. Esto permite que el servicio se ejecute bajo tu usuario específico.

### 3.3 Configurar NGINX como proxy inverso para Code Server

Edita la configuración de tu sitio en NGINX para redirigir el tráfico hacia Code Server, que por defecto escucha en el puerto 8080:

```bash
sudo nano /etc/nginx/sites-available/<tu-dominio>.conf
```

Reemplaza el contenido con la siguiente configuración (ajusta `server_name` con tu dominio o IP):

```nginx
server {
    listen 80;
    listen [::]:80;

    server_name <tu-dominio.com>;

    location / {
        proxy_pass http://localhost:8080/;
        proxy_set_header Host $host;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection upgrade;
        proxy_set_header Accept-Encoding gzip;
    }
}
```

> **Explicación:**
> - `proxy_pass` redirige todo el tráfico entrante al puerto 8080 donde corre Code Server.
> - `proxy_set_header Upgrade` y `Connection` son necesarios para que WebSocket funcione (requerido por Code Server para la comunicación en tiempo real).
> - `$host` pasa el nombre de dominio original de la petición.

### 3.4 Configurar la contraseña de Code Server

Edita el archivo de configuración de Code Server para establecer una contraseña segura:

```bash
nano ~/.config/code-server/config.yaml
```

Modifica el campo `password` con una contraseña fuerte:

```yaml
bind-addr: 127.0.0.1:8080
auth: password
password: <tu-contraseña-segura>
cert: false
```

### 3.5 Reiniciar los servicios

Aplica los cambios reiniciando ambos servicios:

```bash
sudo systemctl restart nginx
sudo systemctl restart code-server@$USER
```

Ahora puedes acceder a Code Server desde tu navegador en `http://<tu-dominio.com>` (o `https://` si configuraste Certbot).

---

## 4. PostgreSQL

PostgreSQL es un sistema de gestión de bases de datos relacional robusto y de código abierto. Es ampliamente utilizado en aplicaciones web por su fiabilidad y rendimiento.

### 4.1 Instalar PostgreSQL y el cliente

Instala el servidor de bases de datos y el cliente de línea de comandos:

```bash
sudo apt install -y postgresql postgresql-client
```

### 4.2 Verificar el estado del servicio

Confirma que PostgreSQL está en ejecución:

```bash
sudo systemctl status postgresql
```

Deberías ver `active (running)` en la salida. Presiona `q` para salir del modo de visualización.

---

## 5. pgAdmin 4 (Administración Web de PostgreSQL)

pgAdmin es la herramienta de administración más popular para PostgreSQL. Proporciona una interfaz web completa para gestionar bases de datos, ejecutar consultas y visualizar el esquema.

### 5.1 Agregar el repositorio oficial de pgAdmin

Primero, importa la clave GPG del repositorio de pgAdmin de forma segura (el método con `apt-key` está deprecado):

```bash
curl -fsS https://www.pgadmin.org/static/packages_pgadmin_org.pub | sudo gpg --dearmor -o /usr/share/keyrings/packages-pgadmin-org.gpg
```

Crea el archivo de repositorio:

```bash
sudo sh -c 'echo "deb [signed-by=/usr/share/keyrings/packages-pgadmin-org.gpg] https://ftp.postgresql.org/pub/pgadmin/pgadmin4/apt/jammy pgadmin4 main" > /etc/apt/sources.list.d/pgadmin.list'
```

> **Explicación:** `jammy` es el nombre en clave de Ubuntu 22.04. Si usas otra versión de Ubuntu, cambia este valor según corresponda.

### 5.2 Instalar pgAdmin y Apache

Actualiza los repositorios e instala pgAdmin junto con Apache (necesario como servidor web para la interfaz de pgAdmin):

```bash
sudo apt update
sudo apt install -y pgadmin4-web apache2
```

### 5.3 Configurar el puerto de Apache

Edita el archivo de puertos de Apache para evitar conflictos con NGINX, que ya usa el puerto 80. Cambia el puerto de Apache a uno disponible (por ejemplo, 5050):

```bash
sudo nano /etc/apache2/ports.conf
```

Modifica la línea `Listen 80` por:

```
Listen 5050
```

### 5.4 Configurar el Virtual Host de Apache

Edita el sitio por defecto de Apache para que escuche en el nuevo puerto:

```bash
sudo nano /etc/apache2/sites-available/000-default.conf
```

Cambia la primera línea `<VirtualHost *:80>` por:

```apache
<VirtualHost *:5050>
```

Recarga Apache para aplicar los cambios:

```bash
sudo service apache2 reload
```

### 5.5 Configurar pgAdmin

Ejecuta el script de configuración web de pgAdmin. Este script creará un usuario administrador y configurá el servicio web:

```bash
sudo /usr/pgadmin4/bin/setup-web.sh
```

El script te pedirá un correo electrónico y una contraseña que usarás para iniciar sesión en la interfaz web de pgAdmin.

### 5.6 Acceder a pgAdmin

Abre tu navegador y navega a `http://<ip-del-servidor>:5050/pgadmin4`. Inicia sesión con las credenciales que configuraste en el paso anterior.

---

## 6. Configuración de PostgreSQL

En esta sección configuraremos PostgreSQL para permitir conexiones remotas y crearemos un usuario y una base de datos.

### 6.1 Permitir conexiones remotas

Edita el archivo `pg_hba.conf` para definir quién puede conectarse y con qué método de autenticación.

> **Nota:** Si no estás seguro de la versión de PostgreSQL instalada, ejecuta `ls /etc/postgresql/` para verificar el número de versión. Ubuntu 22.04 LTS incluye PostgreSQL 14 por defecto.

```bash
sudo nano /etc/postgresql/14/main/pg_hba.conf
```

Agrega la siguiente línea al final del archivo para permitir conexiones desde cualquier dirección IP con contraseña encriptada:

```
# Acceso remoto
host    all    all    all    scram-sha-256
```

> **Advertencia de seguridad:** La configuración `all` permite conexiones desde cualquier IP. En producción, reemplaza el último `all` con el rango de IPs específico de tu red (por ejemplo, `192.168.1.0/24`).

### 6.2 Escuchar en todas las interfaces

Por defecto, PostgreSQL solo escucha en `localhost`. Para permitir conexiones remotas, edita el archivo de configuración principal (ajusta el número de versión según lo verificado en el paso anterior):

```bash
sudo nano /etc/postgresql/14/main/postgresql.conf
```

Busca la línea `#listen_addresses = 'localhost'` y cámbiala a:

```
listen_addresses = '*'
```

> **Explicación:** El asterisco `*` le indica a PostgreSQL que escuche en todas las interfaces de red disponibles. Si prefieres escuchar solo en una IP específica, reemplaza `*` con esa dirección.

### 6.3 Reiniciar PostgreSQL

Aplica los cambios de configuración:

```bash
sudo systemctl restart postgresql
```

### 6.4 Configurar el firewall

Abre los puertos de PostgreSQL (5432) y del puerto de pgAdmin/Apache (5050) en el firewall de Ubuntu:

```bash
sudo ufw allow postgresql
sudo ufw allow 5050
```

### 6.5 Crear un usuario y base de datos

Crea un usuario del sistema que será el propietario de la base de datos:

```bash
sudo adduser <nombre-usuario>
```

Accede a la consola de PostgreSQL como el usuario `postgres` (el superusuario predeterminado):

```bash
sudo -u postgres psql
```

Dentro de la consola de PostgreSQL (`psql`), ejecuta los siguientes comandos:

```sql
-- Crear un rol (usuario) de PostgreSQL con el mismo nombre del usuario del sistema
CREATE USER <nombre-usuario> WITH PASSWORD '<tu-contraseña>';

-- Crear una base de datos asignada a ese usuario
CREATE DATABASE <nombre-db> OWNER <nombre-usuario>;

-- Conceder privilegios adicionales si es necesario
ALTER USER <nombre-usuario> WITH SUPERUSER;
```

Para salir de la consola de PostgreSQL:

```sql
\q
```

---

## 7. Python (Instalación desde Código Fuente)

Si necesitas una versión de Python más reciente que la que incluye Ubuntu 22.04 (Python 3.10), puedes compilarla desde el código fuente. Este método te da control total sobre la versión y las optimizaciones.

### 7.1 Actualizar el sistema

Asegúrate de que todos los paquetes del sistema estén actualizados:

```bash
sudo apt update && sudo apt upgrade -y
```

### 7.2 Instalar dependencias de compilación

Necesitas herramientas de compilación y bibliotecas de desarrollo para compilar Python correctamente:

```bash
sudo apt install -y build-essential libssl-dev libbz2-dev zlib1g-dev \
  libncurses5-dev libncursesw5-dev libreadline-dev libsqlite3-dev \
  libgdbm-dev libgdbm-compat-dev libffi-dev liblzma-dev tk-dev \
  libdb5.3-dev libgmp3-dev libmpfr-dev libmpc-dev libgmp-dev
```

> **Explicación:** Cada biblioteca (`-dev`) proporciona archivos de encabezado necesarios para que Python pueda enlazarse con funcionalidades del sistema como SSL, compresión, bases de datos SQLite, entre otras.

### 7.3 Descargar el código fuente de Python

Ve a la página oficial de Python ([https://www.python.org/downloads/source/](https://www.python.org/downloads/source/)) y copia el enlace de la versión que deseas instalar. Luego descárgala y descomprímela:

```bash
cd /usr/src
sudo wget https://www.python.org/ftp/python/3.14.0/Python-3.14.0.tgz
sudo tar xzf Python-3.14.0.tgz
```

> **Nota:** Reemplaza `3.14.0` con la versión específica que necesites.

### 7.4 Compilar e instalar Python

Configura, compila e instala Python. Este proceso puede tardar varios minutos dependiendo de los recursos del servidor:

```bash
cd Python-3.14.0
sudo ./configure --enable-optimizations
sudo make altinstall
```

> **Importante:** Usamos `make altinstall` en lugar de `make install` para **no sobrescribir** el binario `python3` del sistema. Esto es fundamental porque muchas herramientas del sistema operativo dependen de la versión de Python preinstalada. Si la sobrescribes, podrías romper componentes del sistema.

### 7.5 Verificar la instalación

Confirma que la nueva versión de Python se instaló correctamente:

```bash
python3.14 --version
```

Deberías ver la versión que compilaste, por ejemplo: `Python 3.14.0`.

### 7.6 Configurar como versión predeterminada (opcional)

Si deseas que `python3` apunte a la versión recién instalada, elige **una** de estas dos opciones:

**Opción A — Usar el sistema de alternativas de Ubuntu (más control):**

Registra la nueva versión manualmente y selecciona cuál usar por defecto:

```bash
sudo update-alternatives --install /usr/bin/python3 python3 /usr/local/bin/python3.14 2
sudo update-alternatives --config python3
```

**Opción B — Usar el paquete `python-is-python3` (más simple):**

Este paquete de Ubuntu crea un enlace directo de `python3` a la versión activa:

```bash
sudo apt install -y python-is-python3
```

> **Importante:** No uses ambas opciones a la vez. Elige la que mejor se adapte a tu caso. La Opción A te permite alternar entre múltiples versiones instaladas; la Opción B es más simple pero menos flexible.

Verifica que el cambio se aplicó:

```bash
python3 --version
```

> **⚠️ Advertencia:** Cambiar la versión predeterminada de Python puede afectar scripts y herramientas del sistema que esperan la versión original de Ubuntu (3.10). Si encuentras problemas, revierte el cambio con `sudo update-alternatives --config python3` y selecciona la versión original.

---

## Limpieza del Historial de Terminal

Si necesitas limpiar el historial de comandos del terminal (por ejemplo, después de configurar contraseñas en texto plano durante la instalación), puedes hacerlo con:

```bash
history -c && echo "" > ~/.bash_history
```

> **Nota:** Úsalo con precaución. El historial de comandos es útil para rastrear lo que se ha hecho en el servidor. Solo limpia el historial si es estrictamente necesario por razones de seguridad (por ejemplo, si escribiste contraseñas como argumentos de comandos).
