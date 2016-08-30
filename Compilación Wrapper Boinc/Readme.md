# Compilación del Wrapper de Boinc para ARM

El siguiente tutorial muestra cómo hacer la compilación del wrapper de Boinc para dispositivos android con arquitectura ARM. En esta carpeta se encuentran los archivos de configuración modificados para usarlos como base

## Prerrequisitos
* Servidor Boinc Instalado y Funcionando.
* cmake (Buscar versión actual, no hay documentación sobre cuál sirve.)


### Configuración del NDK android

* Instalación de wget para descargar el NDK de los repositorios de Google y unzip para descomprimir los archivos

```sh
$  sudo apt-get install wget unzip
```

* Para la ejecución exitosa de este tutorial, es importante tener la sesión iniciada con el usuario boincadm

```sh
$  cd $HOME
```

```sh
$  mkdir NVPACK && cd NVPACK
```

Descargar el zip del NDK para linux. La versión que se utiliza para el desarrollo de este tutorial es la 10e con el fin de mantener consistencia con la compilación cruzada de ITK

```sh
$  wget https://dl.google.com/android/repository/android-ndk-r10e-linux-x86_64.zip
```
Descomprimir y borrar el zip

```sh
$  unzip android-ndk-r10e-linux-x86_64.zip && rm android-ndk-r10e-linux-x86_64.zip
```

Cambiar el nombre de la carpeta para que coincida con los archivos de configuración de Boinc, OJO REVISAR si sí hay que hacerlo.

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


### Configuración del servidor boincadm


```sh
$  cd $HOME/boinc-src/android
```

Construir el toolchain de Android con el script que provee Boinc para la arquitectura ARM.

```sh
$  ./build_androidtc_arm.sh
```

El siguiente paso es modificar los scripts de boinc para compilar openssl, curl y las librerìas para arm

Los archivos a modificar son los siguientes.

* build_curl_arm.sh
* build_openssl_arm.sh
* build_wrapper_arm.sh

#### build_curl_arm.sh

La ruta de la variable CURL debe apuntar al lugar donde se descargó el código fuente. En este caso es:

```sh
$  CURL="$HOME/curl"
```
#### build_open_ssl.sh

La ruta de la variable OPENSSL debe apuntar al lugar donde se descargó el código fuente. En este caso es:

```sh
$  OPENSSL="$HOME/openssl"
```
#### build_wrapper_arm.sh (REVISAR)

Revisar que las variables definidas sean consistentes con lo definido previamente.

Se le debe agregar a la variable CXX_FLAGS las  banderas fPIE y pie. Esto con el fin de generar ejecutables PIE (position independent executables) Exigidos desde Android 5.0 y soportados desde la versión 4.1. En el momento de creación de este tutorial la variable completa deberìa quedar de la siguiente manera.
```sh
$  export CXXFLAGS="--sysroot=$TCSYSROOT -DANDROID -Wall -I$TCINCLUDES/include -funroll-loops -fexceptions -O3 -fomit-frame-pointer -fPIE -pie"
```

### Compilar el Wrapper para android

Ejecutar los siguientes scripts en este orden (es necesario hacer esta operación como super usuario para que se realice la instalación correctamente)

```sh
$  ./build_curl_arm.sh
$  ./build_openssl_arm.sh
$  ./build_libraries_arm.sh
$  ./build_wrapper_arm.sh 
```

## Errores Comunes

* El dispositivo solo acepta ejecutables position independent. Agregar las banderas -fPIE -pie a CXX_FLAGS en el archivo build_wrapper_arm.sh
* El wrapper se ejecuta pero no hay un error al subir los resultados. TODO: nombres de archivos, modificación del código fuente
