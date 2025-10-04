## Instalación de Servicio con Docker
En este README se explicará brevemente a través de la linea de comandos como instalar la imagen de Alpine, crear distintos contenedores que corran dicha imagen y probar la conexión entre ellas con la herramienta Docker.




## Índice

* [Descripción General](#Instalación-de-Servicio-con-Docker)

* [Descarga e Instalación de la imagen de "Alpine"](#Descarga-e-Instalación-de-la-imagen-de-"Alpine")

* [Instalación de WordPress](#Instalación-de-WordPress)

* [Configurar Apache para WordPress](#Configurar-Apache-para-WordPress)

* [Configurar la Base de Datos](#Configurar-la-Base-de-Datos)

* [Configurar la conexión entre la BBDD y WordPress](#Configuración-de-conneción-de-WordPress-con-la-Base-de-Datos)

* [Configurar el WordPress](#Configuración-de-WordPress)

* [Realizar tu Primer Post!](#Haz-tu-Primer-Post)

* [Contacto](#Contacto)

* [Documentación](#Documentación)


## Descarga e Instalación de la imagen de "Alpine"

Para comenzar tendremos que instalar la imagen de la distribución de Linux Alpine, para esto utilizaremos el comando:
    
```bash
docker pull alpine
```
<br><br>
Aquí una captura de pantalla de como debería verse la terminal mientras descargamos la imagen:

 ![Instalación de Alpine](Imagenes/1.png)

<br><br>
## Instalación de WordPress
Crea el directorio de Instalación y descarga los archivos de **[WordPress.org](https://wordpress.org)**:

```bash
sudo mkdir -p /srv/www
sudo chown www-data: /srv/www
curl https://wordpress.org/latest.tar.gz | sudo -u www-data tar zx -C /srv/www
```

<br><br>
Captura de pantalla de como se ve el comando en la terminal:

 ![Instalación de WordPress](imagenes/2.png)

 ## Configurar Apache para WordPress

Creamos el sitio de Apache con el siguiente comando:

```bash
sudo nano /etc/apache2/sites-available/wordpress.conf
```
<br><br>
Dentro del editor inyectamos las siguientes lineas de codigo:

```bash
<VirtualHost *:80>
    DocumentRoot /srv/www/wordpress
    <Directory /srv/www/wordpress>
        Options FollowSymLinks
        AllowOverride Limit Options FileInfo
        DirectoryIndex index.php
        Require all granted
    </Directory>
    <Directory /srv/www/wordpress/wp-content>
        Options FollowSymLinks
        Require all granted
    </Directory>
</VirtualHost>
```
<br><br>
Guardamos con: Ctrl + O y cerramos con: Ctrl + X

Captura de pantalla de como se ve el editor de texto del terminal:

 ![Configuración de apache](imagenes/3.png)

<br><br>
Seguido modificaremos el archivo de configuración por defecto para agregar un Hostname al que el WordPress respondera sus llamadas. Este nombre de host debe estar mapeado a tu caja de alguna manera, por ejemplo, a través de DNS, o mediante ediciones en los sistemas del cliente. Hay que agregar ServerName como se ve el siguiente ejemplo:

```bash
<VirtualHost *:80>
    ServerName hostname.example.com
    ... # the rest of the VHost configuration
</VirtualHost>
```

<br><br>
Captura de pantalla agregando esta linea al archivo:

![Configuración de apache](imagenes/6.png)

<br><br>
Para finalizar la configuración del Apache, reiniciaremos el servicio para aplicar todos los cambios con el siguiente comando:

```bash
sudo service apache2 reload
```

<br><br>
Captura de pantalla aplicando el comando:

![Configuración de apache](imagenes/4.png)

## Configurar la Base de Datos 

Para configurar la base de datos primero tenemos que crearla en MySQL, utilizaremos los siguientes comandos:

```bash
$ sudo mysql -u root
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 7
Server version: 5.7.20-0ubuntu0.16.04.1 (Ubuntu)

Copyright (c) 2000, 2017, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> CREATE DATABASE wordpress;
Query OK, 1 row affected (0,00 sec)

mysql> CREATE USER wordpress@localhost IDENTIFIED BY '<your-password>';
Query OK, 1 row affected (0,00 sec)

mysql> GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP,ALTER
    -> ON wordpress.*
    -> TO wordpress@localhost;
Query OK, 1 row affected (0,00 sec)

mysql> FLUSH PRIVILEGES;
Query OK, 1 row affected (0,00 sec)

mysql> quit
Bye
```
<br><br>
- Capturas de pantalla de la configuración de la base de datos:

![Configuración de BBDD](imagenes/5.png)
<br><br>
![Configuración de BBDD](imagenes/7.png)
<br><br>
![Configuración de BBDD](imagenes/8.png)
<br><br>
![Configuracion de WordPress en BBDD](imagenes/9.png)

## Configuración de conneción de WordPress con la Base de Datos

Ahora tenemos que configurar el WordPress para que use la base de datos. Primero copiaremos la archivo de configuración de ejemplo a "wp-config.php":
```bash
sudo -u www-data cp /srv/www/wordpress/wp-config-sample.php /srv/www/wordpress/wp-config.php
```
<br><br>

A continuación, establece las credenciales de la base de datos en el archivo de configuración (no reemplaces database_name_here ni username_here en los comandos de abajo. Sí reemplaza <your-password> con la contraseña de tu base de datos):
```bash
sudo -u www-data sed -i 's/database_name_here/wordpress/' /srv/www/wordpress/wp-config.php
sudo -u www-data sed -i 's/username_here/wordpress/' /srv/www/wordpress/wp-config.php
sudo -u www-data sed -i 's/password_here/<your-password>/' /srv/www/wordpress/wp-config.php
```
<br><br>

Para terminar, abrimos una sesion para configurar el siguiente archivo con nano:
```bash
sudo -u www-data nano /srv/www/wordpress/wp-config.php
```
<br><br>

Hay que buscar las siguientes líneas:
```bash
define( 'AUTH_KEY',         'put your unique phrase here' );
define( 'SECURE_AUTH_KEY',  'put your unique phrase here' );
define( 'LOGGED_IN_KEY',    'put your unique phrase here' );
define( 'NONCE_KEY',        'put your unique phrase here' );
define( 'AUTH_SALT',        'put your unique phrase here' );
define( 'SECURE_AUTH_SALT', 'put your unique phrase here' );
define( 'LOGGED_IN_SALT',   'put your unique phrase here' );
define( 'NONCE_SALT',       'put your unique phrase here' );
```
<br><br>

Elimina esas líneas (ctrl+k borrará una línea cada vez que presiones la combinación). Luego reemplázalas con el contenido de **[aqui](https://api.wordpress.org/secret-key/1.1/salt/.)** (Esa dirección es un generador aleatorio que devuelve claves completamente aleatorias cada vez que se abre). Este paso es importante para asegurarte de que tu sitio no sea vulnerable a ataques de “secretos conocidos”.
<br><br>

Guarda y cierra el archivo con los comandos Ctrl + X seguido de ENTER.
- Captura de pantalla con los comandos por terminal:
![Configuracion de WordPress en BBDD](imagenes/10.png)
- Captura de pantalla con los cambios realizados en las líneas señaladas anteriormente:
![Configuracion de WordPress en BBDD](imagenes/11.png)
<br><br>

## Configuración de WordPress 
- Abre http://localhost/ en tu navegador(si estas utilizando una maquina cliente tienes que usar la direccion IP de la maquina servidor).
- Se te pedirá el título de tu nuevo sitio, un nombre de usuario, una contraseña y una dirección de correo electrónico. Ten en cuenta que el nombre de usuario y la contraseña que elijas aquí son para WordPress, y no dan acceso a ninguna otra parte de tu servidor.
- ⚠️:**Atención:** elige un nombre de usuario y una contraseña diferentes a las credenciales de MySQL (base de datos) que configuramos para el uso de WordPress, y diferentes a tus credenciales para iniciar sesión en el escritorio o la terminal de tu ordenador o servidor.
- Puedes elegir si quieres que tu sitio sea indexado por los motores de búsqueda.
<br><br>
Capturas de pantalla con los pasos finales de configuración:
![Configuracion de WordPress en BBDD](imagenes/13.png)
<br><br>
![Configuracion de WordPress en BBDD](imagenes/14.png)
<br><br>
![Configuracion de WordPress en BBDD](imagenes/15.png)
<br><br>
![Configuracion de WordPress en BBDD](imagenes/16.png)
<br><br>
![Configuracion de WordPress en BBDD](imagenes/17.png)
<br><br>

## Haz tu Primer Post:
- Borra el Post de "Hello World"
- Para crear una nueva entrada, haz clic en el botón “Añadir nueva”. Verás un elegante editor WYSIWYG con opciones de formato de texto simples (pero potentes). Si lo prefieres, puedes cambiar al modo Texto para trabajar con HTML puro.
* Captura de pantalla de un modelo de plantilla de WordPress personalizada:
<img width="2496" height="1366" alt="image" src="https://github.com/user-attachments/assets/774b9d60-30ed-4df7-92d3-dc80873aa4c3" />

## Contacto
José Gregorio Cámara Depablos - [@CabbaGG](https://twitter.com/CabbaGG) - JOS.95camara@gmail.com

Project Link: [https://github.com/CabbaGG2/PSP_InstalacionWordPress](https://github.com/CabbaGG2/PSP_InstalacionWordPress/edit/main/README.md)

## Documentación

* [Instala y Configura WordPress](https://ubuntu.com/tutorials/install-and-configure-wordpress#8-write-your-first-post)

