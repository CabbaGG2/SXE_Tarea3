## Instalación de Servicio con Docker
En este README se explicará brevemente a través de la linea de comandos como instalar la imagen de Alpine, crear distintos contenedores que corran dicha imagen y probar la conexión entre ellas con la herramienta Docker.




## Índice

* [Descripción General](#Instalación-de-Servicio-con-Docker)

* [Descarga e Instalación de la imagen de "Alpine"](#Descarga-e-Instalación-de-la-imagen-de-"Alpine")

* [Creación de contenedor con Alpine](#Creación-de-contenedor-con-Alpine)

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

Para saber si la imagen se instaló correctamente en nuestro equipo se utiliza el siguiente comando:
    
```bash
docker images
```
<br><br>
Aquí una captura de pantalla:

 ![Instalación de Alpine](Imagenes/2.png)

<br><br>
## Creación de contenedor con Alpine
Para crear el contenedor de Alpine sin nombre utilizamos el comando:

```bash
docker run -it alpine
```

<br><br>
Captura de pantalla de como se ve el comando en la terminal:

 ![Creación de contenedor](Imagenes/3.png)
 <br><br>
Como podemos observar en la foto, el contenedor arranca automaticamente.

<br><br>
Para averiguar cual es el nombre del contenedor que se creó utilizamos el comando:

```bash
docker ps -a
```

Esto nos muestra todos los contenedores creados con sus respectivas imagenes, y su estatus actual ya sea que esten corriendo o apagados.
En este ejemplo el contener de alpine se llama "cool_hypatia"
<br><br>
Captura de pantalla de como se ve la terminal con el comando:
 ![Creación de contenedor](Imagenes/4.png)
 <br><br>

 ## Creación de Contenedor Alpine con nombre

Se creará el contenedor "dam_alp1" con el siguiente comando:

```bash
docker container create -i -t --name dam_alp1 alpine
```
<br><br>
Captura de pantalla de la vista desde terminal del comando:
![Creación de contenedor dam_alp1](Imagenes/5.png)

<br><br>
Para iniciar el contenedor utilizamos el siguiente comando:

```bash
docker container start --attach -i dam_alp1
```
<br><br>

Captura de pantalla de la maquina virtual funcionando desde terminal:
![Creación de contenedor dam_alp1](Imagenes/6.png)

<br><br>

## Pruebas de conexión 

Para averiguar el ping que tiene asignado el contenedor de Alpine se utilizará el siguiente comando dentro de la maquina virtual:

```bash
ip addr
```
<br><br>
- Captura de pantalla de el resultado de el comando por terminal:

![Prueba de ping](Imagenes/7.png)
<br><br>
Como podemos ver en la imagen, el ping del equipo virtual es: 127.0.0.1.

<br><br>
Para realizar un ping a Google utilizaremos el siguiente comando desde la terminal de el Alpine activo:

```bash
ping -c 4 google.com
```
Captura de pantalla mostrando la respuesta del ping:

![Prueba de ping](Imagenes/8.png)
<br><br>

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




