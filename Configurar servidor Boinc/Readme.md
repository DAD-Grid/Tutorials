# Configurar servidor Boinc

Este tutorial se basa en:
* https://www.youtube.com/watch?v=vVZ44scS7pk&list=PLjoVdrzADmhm1_aOwGMTcZgKgPwVPh_ko&index=1
* https://boinc.berkeley.edu/trac/wiki/ServerIntro

## Prerrequitos
 * Sistema Operativo Linux. Distribución Ubuntu 14.04 LTS.

## Instalación Dependencias

### Paquetes que necesita Boinc
```sh
    -mysql-server 
    -mysql-client 
    -php5 
    -apache2 
    -php5-gd 
    -php5-cli 
    -php5-mysql 
    -python-mysqldb 
    -libssl-dev 
    -libcurl4-openssl-dev 
    -libnotify-dev
```
##### *Script*
```sh
$  sudo apt-get update #opcional, pero recomendado
$  sudo apt-get install mysql-server mysql-client php5 apache2 php5-gd php5-cli php5-mysql python-mysqldb libssl-dev libcurl4-openssl-dev libnotify-dev
```
## **IMPORTANTE! Recordar contraseña con la que se configura mysql**
### Paquetes para construir boinc

```sh
  -build-essential 
  -libtool automake 
  -autoconf 
  -pkg-config 
  -libmysql++-dev 
  -libssl-dev
  -make 
  -git 
```
##### *Script*
```sh
$  sudo apt-get install build-essential libtool automake autoconf pkg-config libmysql++-dev libssl-dev make git
```

## Creación de usuario boincadm
### Registrar datos personales asociados al usuario boincadm
##### *Script*

```sh
$ sudo adduser boincadm
```

## **IMPORTANTE! Recordar contraseña con la que se configura el usuario boincadm**

### Agregar usuario boincadm al grupo de sudo

##### *Script*

```sh
$ sudo adduser boincadm sudo
```

### Agregar usuario boincadm al grupo de apache

##### *Script*

```sh
$ sudo usermod -a -G boincadm www-data
```
### Creación de usuario en mysql
#### Este será el usuario que se usará durante este tutorial para que Boinc administre la base de datos de cada proyecto.
#### La contraseña que solicita para entrar a _mysql_ es la que se indicó en la instalación de los paquetes que necesita Boinc.
##### *Líneas de comando*
```sql
$ mysql -u root -p
> GRANT ALL ON *.* TO 'boincadm'@'localhost';
> SET PASSWORD FOR 'boincadm'@'localhost'='';
> exit;
```
### Cambiar de sesión al boincadm e ir a la carpeta raiz

##### *Script*
```sh
$ su boincadm
$ cd $HOME
```
## Construcción de Boinc

### Clonar repositorio de boinc en la carpeta boinc-src

##### *Script*

```sh
$ git clone https://github.com/BOINC/boinc.git boinc-src
```

### Compilación del código fuente de Boinc
##### *Líneas de comando*
```sh
$ cd boinc-src
$ ./_autosetup
$ ./configure --disable-client
$ make
```

## Habilitar el modulo CGI de apache

Si la versión de apache es mayor o igual a 2.4 es necesario activar este modulo y reinicar apache

##### *Script*
```sh
$ sudo a2enmod cgi
$ sudo service apache2 restart
```
