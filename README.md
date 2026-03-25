# Custom Shell - Sistemas Operativos

Una shell interactiva desarrollada en **C** que implementa funcionalidades avanzadas de gestión de procesos, manipulación de memoria, sistema de archivos y entorno de ejecución.

## Características Principales

Este proyecto simula un intérprete de comandos con capacidades extendidas:

-   **Gestión de Archivos:** Listado detallado (`list`), borrado recursivo (`deltree`), visualización de estadísticas de archivos (`stat`).
-   **Manipulación de Memoria:** Control sobre bloques de memoria mediante `malloc`, memoria compartida (`shared`) y mapeo de ficheros (`mmap`).
-   **Control de Procesos:** Ejecución de programas en primer y segundo plano (`fork`, `exec`), gestión de una lista de trabajos (`jobs`).
-   **Historial:** Sistema de persistencia de comandos ejecutados durante la sesión.

---

## Estructura del Proyecto

El código está modulado para facilitar su mantenimiento:

-   `shell.c`: Punto de entrada del programa y bucle principal (Read-Eval-Print Loop).
-   `funciones.c`: Implementación de la lógica de los comandos principales.
-   `lista.c` / `lista.h`: Librería interna para la gestión de estructuras de datos (historial, memoria, procesos).
-   `cabeceras.h`: Definiciones globales, librerías del sistema y prototipos.
-   `Makefile`: Archivo de configuración para la compilación automatizada.

---

## Compilación y Ejecución

Para compilar el proyecto, asegúrate de tener instalado el compilador `gcc` y la herramienta `make`.

1. **Compilar el proyecto:**
```bash
  make
  ```

2. **Ejecutar la shell:**
```bash
  ./shell
```

3. **Limpiar archivos binarios:**
```bash 
  make clean
```

---

## Comandos Disponibles

Una vez iniciada la shell, puedes consultar la ayuda detallada de cada comando usando `help [cmd]`.

### Comandos Generales

- **authors [-l|-n]**: Muestra los nombres y logins de los autores. `-l` imprime solo los logins y `-n` solo los nombres.
- **pid [-p]**: Muestra el PID del proceso de la shell. Con `-p` muestra el PID del proceso padre.
- **chdir [dir]**: Cambia el directorio actual de trabajo. Sin argumentos, imprime el directorio actual.
- **date**: Muestra la fecha actual en formato `DD/MM/YYYY`.
- **time**: Muestra la hora actual en formato `hh:mm:ss`.
- **hist [-c|-N]**: Gestiona el historial de comandos. `-c` vacía la lista y `-N` muestra los primeros N comandos.
- **command N**: Repite el comando número `N` del listado histórico.
- **infosys**: Muestra información de la máquina actual (vía la llamada al sistema `uname`).
- **help [cmd]**: Muestra una lista de comandos disponibles o una ayuda breve sobre el uso de `cmd`.

### Gestión de Archivos y Directorios

- **list [-reca] [-recb] [-hid][-long][-link][-acc] n1 n2...**: Lista el contenido de directorios. 
    - `-hid`: Incluye archivos ocultos.
    - `-recb` / `-reca`: Listado recursivo (antes o después).
    - Resto de parámetros funcionan como en `stat`.
- **create [-f] [name]**: Crea archivos (usando `-f`) o directorios.
- **stat [-long][-link][-acc] name1 name2...**: Proporciona información sobre archivos o directorios.
    - `-long`: Listado largo.
    - `-acc`: Tiempo de acceso (accesstime).
    - `-link`: Si es un enlace simbólico, muestra el path contenido.
- **delete [name1 name2...]**: Borra archivos y/o directorios vacíos.
- **deltree [name1 name2...]**: Borra archivos y directorios no vacíos de forma recursiva.
- **open [file] mode**: Abre un archivo y lo añade a la lista de la shell. Modos: `cr` (O_CREAT), `ap` (O_APPEND), `ex` (O_EXCL), `ro` (O_RDONLY), `rw` (O_RDWR), `wo` (O_WRONLY) y `tr` (O_TRUNC).
- **close [df]**: Cierra el descriptor de fichero `df` y lo elimina de la lista.
- **dup [df]**: Duplica el descriptor `df` creando una nueva entrada en la lista de archivos.
- **listopen**: Lista los archivos abiertos por la shell, indicando descriptor, nombre y modo.

### Gestión de Memoria

- **malloc [-free] [tam]**: Reserva un bloque de memoria de tamaño `tam`. Con `-free` libera un bloque previamente reservado.
- **shared [-free|-create|-delkey] cl [tam]**: Gestiona memoria compartida con clave `cl`.
    - `-create`: Crea un bloque de tamaño `tam`.
    - `-free`: Desmapea el bloque de memoria.
    - `-delkey`: Elimina la clave de memoria del sistema.
- **mmap [-free] fich prm**: Mapea el archivo `fich` con permisos `prm`. Con `-free` lo desmapea.
- **read fich addr cont**: Lee `cont` bytes desde el fichero `fich` a la dirección de memoria `addr`.
- **write [-o] fiche addr cont**: Escribe `cont` bytes desde la dirección `addr` al fichero `fiche` (`-o` para sobreescribir).
- **memdump addr cont**: Vuelca en pantalla el contenido de `cont` bytes de la posición de memoria `addr`.
- **memfill addr cont byte**: Llena la memoria desde `addr` con el valor `byte`.
- **mem [-blocks|-funcs|-vars|-all|-pmap]**: Muestra detalles de la memoria del proceso (bloques, funciones, variables o mapa completo).
- **recurse [n]**: Llama a una función recursiva `n` veces.

### Procesos y Entorno

- **uid [-get|-set] [-l] [id]**: Accede a las credenciales del proceso. 
    - `-get`: Muestra credenciales actuales.
    - `-set id`: Cambia la credencial al valor numérico `id`.
    - `-set -l id`: Cambia la credencial al login `id`.
- **showvar var**: Muestra el valor y las direcciones de la variable de entorno `var`.
- **changevar [-a|-e|-p] var valor**: Cambia el valor de una variable de entorno.
    - `-a`: Vía el tercer argumento de `main`.
    - `-e`: Vía `environ`.
    - `-p`: Vía `putenv`.
- **subsvar [-a|-e] var1 var2 valor**: Reemplaza la variable `var1` por `var2=valor`.
- **showenv [-environ|-addr]**: Muestra el entorno del proceso.
- **fork**: La shell realiza un `fork` y espera a que el hijo finalice.
- **exec VAR1 VAR2... prog args... [@pri]**: Ejecuta `prog` sin crear un proceso nuevo, usando un entorno que solo contiene `VAR1, VAR2...`.
- **jobs**: Lista los procesos en segundo plano.
- **deljobs [-term][-sig]**: Elimina procesos de la lista. `-term` para terminados, `-sig` para terminados por señal.
- **job [-fg] pid**: Muestra información del proceso `pid`. Con `-fg` lo pasa a primer plano.

### Salida

- **quit / exit / bye**: Finaliza la ejecución de la shell.
