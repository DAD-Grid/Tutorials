# Compilación Cruzada ITK con Docker

//TODO: Mejorar redacción y  explicación

## Prerrequitos
 * Sistema Operativo Linux
 * Git 
 * Curl

## Instalación de Docker

### Descargar e Instalar Docker 

```sh
$  curl -sSL https://get.docker.com/ | sh
```

### Agregar el usuario actual al grupo Docker

Esto permite ejecutar docker sin ser administrador


```sh
$   usermod -aG docker <usuario>
```
## Compilación de ITK

### Crear un directorio de trabajo

```sh
$   mkdir cross-compile && cd cross-compile
```

### Descargar el branch release de ITK

```sh
$    git clone https://github.com/InsightSoftwareConsortium/ITK.git --branch release
```

### Descargar y ejecutar la imagen para hacer la compilación cruzada de ITK

```sh
$    docker pull dockcross/android-arm
```

Una vez descargada la imagen, se puede ejecutar. Actualmente el proceso por defecto de la imagen imprime a la salida estándar un shell script que facilita su uso.
En caso que la imagen cambie su funcionamiento en el futuro, remitirse al [repositorio de Github](https://github.com/dockcross/dockcross).

#### Ejecutar el contenedor y guardar la salida al archivo con nombre dockcross

```sh
$    docker run --rm dockcross/android-arm > ./dockcross
```

#### Mover este script al path para poder ejecutarlo desde cualquier directorio

```sh
$    sudo cp dockcross /usr/bin/
```
NOTA: Revisar los permisos del archivo y caso de que no tenga permisos de ejecución, agregarlos.

```sh
$    sudo chmod +x /usr/bin/dockcross
```

### Compilar ITK

Ejecutar el contenedor e iniciar una sesión bash dentro de él. La carpeta /work del contenedor está mapeada a la carpeta actual del host. Esto significa que cualquier cambio en esa carpeta dentro del contenedor se va a ver reflejado en el sistema de archivos del host.

```sh
$    dockcross bash
```

* Esto inicia un sesión en bash en el directorio /work del contenedor

```sh
$    mkdir build
```


```sh
$    cd build/
```

```sh
$    ccmake ../ITK/
```

* Modificar el contenido de las siguientes banderas

| Bandera                   | Valor      |
|---------------------------|------------|
| ITK_BUILD_DEFAULT_MODULES | OFF        |
| ITK_DYNAMIC_LOADING       | OFF        |
| BUILD_SHARED_LIBS         | OFF        |
| BUILD_TESTING             | OFF        |
| CMAKE_EXE_LINKER_FLAGS    | -fPIE -pie |

* Una vez terminado el proceso, hacer make.

```sh
$    make
```

* Una vez terminada la construcción, el resultado estará en la carpeta build del host.