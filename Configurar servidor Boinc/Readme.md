# Configurar servidor Boinc

//TODO: Mejorar redacción y  explicación

Este tutorial se hizo basado en:
https://www.youtube.com/watch?v=vVZ44scS7pk&list=PLjoVdrzADmhm1_aOwGMTcZgKgPwVPh_ko&index=1
https://boinc.berkeley.edu/trac/wiki/ServerIntro

Este tutorial se en sistema operativo Ubuntu 16.04.1x64

## Prerrequitos
 * Sistema Operativo Linux
 * Git
 * Curl

## Instalación Dependencias

### Instalar paquetes que utiliza boinc
```sh
$  sudo apt-get install mysql-server mysql-client php5 apache2 php5-gd php5-cli php5-mysql python-mysqldb libssl-dev libcurl4-openssl-dev libnotify-dev
```

### Instalar paquetes para construir boinc

```sh
$  sudo apt-get install build-essential libtool automake autoconf pkg-config libmysql++-dev libssl-dev make git vim
```

## Crear usuario boincadm

### Crear usuario

```sh
$ adduser boincadm
```

### Agregar usuario a grupo de apache

```sh
$ usermod -a -G boincadm www-data
```

### Crearu usuario en mysql

```sh
$ mysql -u root -p
> GRANT ALL ON *.* TO 'boincadm'@'localhost';
> SET PASSWORD FOR 'boincadm'@'localhost'='';
> exit;
```

## Construri Boinc

### Clonar repositori de boinc

```sh
$ git clone https://github.com/BOINC/boinc.git boinc-src
```

### Compilar codigo fuente

```sh
$ cd boinc-src
$ ./_autosetup
$ ./configure --disable-client
$ make
```
