# Compilación Cruzada aplicación ITK con Docker

## Prerrequitos
 * [Construcción stática de ITK para android](https://github.com/DAD-Grid/Tutorials/tree/master/Compilaci%C3%B3n%20ITK)




##Compilar la aplicación que utiliza la librería ITK

 *copiar el proyecto dentro de la carpeta donde se encuentra la construccion de ITK
```sh
$    cp ruta/a/mi/proyecto crossCompile
```

Ejecutar el contenedor e iniciar una sesión bash dentro de él. La carpeta /work del contenedor está mapeada a la carpeta actual crossCompile.

```sh
$    dockcross bash
```

compilar el proyecto indicando la ubicación de la carpeta donde está construido ITK de forma estática para android


```sh
$    cmake  -DITK_DIR=/path/to/static/ITK -DCMAKE_EXE_LINKER_FLAGS="-fPIE -pie"
```
* También se deben indicar las de possition independant executables (pie) que está soportado desde la version 4.1 de android y es obligatorio dede 5.0

