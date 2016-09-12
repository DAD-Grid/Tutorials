# Compilación del Wrapper de Boinc para ARM

El siguiente tutorial muestra cómo realizar el procesos de compilación cruzada del wrapper de Boinc para dispositivos Android con arquitectura ARM. En esta carpeta se encuentran los archivos de configuración modificados para usarlos como base.

## Prerrequisitos
* Servidor Boinc Instalado y Funcionando.
* cmake version 2.8.12.2
* Ingresar como usuario boincadm (Configurado anteriormente en el tutorial **Configurar servidor Boinc**)


### Configuración del NDK android

* Instalación de wget para descargar el NDK de los repositorios de Google y unzip para descomprimir los archivos.

```sh
$  sudo apt-get install wget unzip
```

* Para la ejecución exitosa de este tutorial, es importante tener la sesión iniciada con el usuario boincadm.

```sh
$  cd $HOME
```
* Crear la carpeta NVPACK donde se almacenará el NDK de Amdroid.
```sh
$  mkdir NVPACK && cd NVPACK
```

* Descargar el zip del NDK para linux. La versión que se utiliza para el desarrollo de este tutorial es la 10e con el fin de mantener consistencia con la compilación cruzada de ITK.

```sh
$  wget https://dl.google.com/android/repository/android-ndk-r10e-linux-x86_64.zip
```
* Descomprimir y borrar el zip

```sh
$  unzip android-ndk-r10e-linux-x86_64.zip && rm android-ndk-r10e-linux-x86_64.zip
```

* Cambiar el nombre de la carpeta para que coincida con los archivos de configuración de Boinc.

```sh
$  mv android-ndk-r10e-linux-x86_64 android-ndk-r10e
```

### Descargar el código fuente de Openssl

El cliente boinc utiliza la librería de openssl por lo que es necesario compilarla para Android.

```sh
$  cd $HOME
```

```sh
$  git clone https://github.com/openssl/openssl.git
```

### Descargar el código fuente de curl
El cliente boinc utiliza la librería de curl para hacer las peticiones HTTP al servidor por lo que es necesario compilar la librería para Android.

```sh
$  cd $HOME
```

```sh
$  git clone  https://github.com/curl/curl.git
```

```sh
$  cd curl
```
Generar el makefile de curl para que pueda ser compilado por el script de Boinc.

```sh
$  cmake .
```


### Configuración del servidor Boinc para compilar librerias de Boinc para Android.


```sh
$  cd $HOME/boinc-src/android
```

Construir el toolchain de Android con el script que provee Boinc para la arquitectura ARM.

```sh
$  ./build_androidtc_arm.sh
```

El siguiente paso es modificar los scripts de boinc para compilar openssl, curl y las librerías para ARM.

Los archivos a modificar son los siguientes:

* build_curl_arm.sh
* build_openssl_arm.sh
* build_wrapper_arm.sh

* #### Scipt _**build_curl_arm.sh**_

La ruta de la variable CURL debe apuntar al lugar donde se descargó el código fuente. Para esto se debe sobreescribir la ruta que viene por defecto cambiándola por:

```sh
$  CURL="$HOME/curl"
```
* #### Scipt _**build_openssl_arm.sh**_

La ruta de la variable OPENSSL debe apuntar al lugar donde se descargó el código fuente. Para esto se debe sobreescribir la ruta que viene por defecto cambiándola por:

```sh
$  OPENSSL="$HOME/openssl"
```
* #### Scipt _**build_wrapper_arm.sh**_

Revisar que las variables definidas sean consistentes con lo definido previamente.

Se le debe agregar a la variable CXX_FLAGS las  banderas fPIE y pie. Esto con el fin de generar ejecutables PIE (position independent executables) exigidos desde Android 5.0 y soportados desde la versión 4.1. En el momento de creación de este tutorial la variable completa deberìa quedar de la siguiente manera.
```sh
$  export CXXFLAGS="--sysroot=$TCSYSROOT -DANDROID -Wall -I$TCINCLUDES/include -funroll-loops -fexceptions -O3 -fomit-frame-pointer -fPIE -pie"
```
* ### Corrección archivo configure.ac
En la carpeta fuente de boinc (boinc-src) se encuentra el archivo configure.ac que utiliza el script build_wrapper_arm.sh, este hace
una verificación de la versión de curl y si no se tiene la versión 7.17.1, la compilación va a terminar con errores.
Por esta razón es necesario elimiar esta comprobación en el archivo configure.ac comentareando la siguiente línea:
```sh
  LIBCURL_CHECK_CONFIG([yes], [7.17.1], [haveCurl=yes], [haveCurl=no])
```
y cambiarla por la siguiente linea,  para indicar que si hay una version de curl construida: 
```sh
  haveCurl=yes
```

### Compilar el Wrapper para android

Ejecutar los siguientes scripts en este orden (es necesario realizar estas operaciones como super usuario para que se realice la instalación correctamente)

```sh
$ cd $HOME/boinc-src/android
$ sudo ./build_curl_arm.sh
$ sudo ./build_openssl_arm.sh
$ sudo ./build_libraries_arm.sh
$ sudo ./build_wrapper_arm.sh 
```

## Errores Comunes

* El dispositivo solo acepta ejecutables position independent. Agregar las banderas -fPIE -pie a CXX_FLAGS en el archivo build_wrapper_arm.sh
* El wrapper se ejecuta pero no hay un error al subir los resultados. TODO: nombres de archivos, modificación del código fuente
