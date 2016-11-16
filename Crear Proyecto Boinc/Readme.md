# Crear Proyecto Boinc


## Prerrequitos
 * Servidor Boinc construido
 * Usuario de mysql boincadm

## Crear Proyecto

### Ir a la carpeta funde de boinc tools

```sh
$  cd ~/boinc-src/tools
```

### Crear proyecto
```sh
$  ./make_project --url_base <direccionServidor, puede ser ip o por dns ej http://boinc.com> --db_user boincadm <nombreProyecto>
```
se deben cambiar los campos <direccionServidor\> y <nombreProyecto\> por los valores correctos. Se recomeinda que la direccion del Servidor
no sea una ip por que cambiarla es un proceso complicado.

### Configuraciones de proyecto

A continuación se deben agregar comandos de configuaracion a apache para dar permisos a las diferentes carpetas. Para esto, se debe 
agregar el contenido del archivo <nombreProyecto\>.httpd.conf, que se encuentra en la carpeta del proyecto donde su nombre es nombreProyecto. 
Si la version de apache es mayor a 2.4 es necesario cambiar las lineas de "Order" y "Allow" por  "Require all granted" o "Require all denied", segun corresponda.

```sh
$ cat nombreProyecto.httpd.conf >> /etc/apache2/apache2.conf
```

Luego se agrega el script que se encuentra en ~/projects/<nombreProyecto\>/<nombreProyecto\>.cronjob para ejecutar los trabajos cron que constantemente
activan los demonios.

```sh
$  crontab ~/projects/<nombreProyecto>/<nombreProyecto>.cronjob
```
Por ultimo, para utilizar la interfaz gráfica de administración del proyecto se debe crear un usuairo y una contraseña utilizando el programa 'htpasswd' que se encuentra 
en apache2-utils.

```sh
$  sudo apt-get install apache2-utils
$  htpasswd -cb ~/projects/<nombreProyecto>/html/ops/.htpasswd <usuario> <contraseña>
```
Al realizar esta operación se puede cargar la url <direccionServidor\>/<nombreProyecto\>_ops.

