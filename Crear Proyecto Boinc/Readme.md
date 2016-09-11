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
$  ./make_project --url_base <direccionServidor> --dbuser boincadm <nombreProyecto>
```
se deben cambiar los campos direccionServidor y nombreProyecto por los valores correctos

### 
installar program 'htpasswd'
sudo apt-get install apache2-utils
-cb html/ops/.htpasswd boincadm boincadm
###
