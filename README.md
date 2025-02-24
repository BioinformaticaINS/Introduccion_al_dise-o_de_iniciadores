# Introducción al diseño de iniciadores
- Autor: Dámaris Esquén
- Fecha: 14/02/2025
  

## En bash, instalemos primer3 desde github
```bash
sudo apt update
sudo apt upgrade
sudo apt-get install -y build-essential g++ cmake git-all
git clone https://github.com/primer3-org/primer3.git primer3
cd primer3/src
make
make test
```


# 1. Primer3
Diseña cebadores de PCR a partir de secuencias de ADN. Es ampliamente utilizado (190 000 resultados en Google para "primer3").

## 1.1. Sintaxis
La sintaxis general es:
primer3_core [opciones] [ input_file ]

Los argumentos de linea de comando son:
```bash
primer3_core [ --format_output ] [ --default_version=1|--default_version=2 ] [ --io_version=4 ] [ --p3_settings_file=<file_path> ] [ --echo_settings_file ] [ --strict_tags ] [ --output=<file_path> ] [ --error=<file_path> ] [ input_file ]
```

### 1.1.1. Argumentos Principales:

--about: Muestra la versión del programa y finaliza la ejecución.
--default_version=n: Selecciona la versión de los valores predeterminados (n=2 es la actual y n=1 la antigua).

--format_output: Produce una salida orientada al usuario en lugar de a programas.
--strict_tags: Genera error si se encuentra alguna etiqueta desconocida en la entrada (o en el archivo de configuración).
--p3_settings_file=<archivo>: Permite cargar parámetros globales desde un archivo, que pueden ser sobreescritos por la entrada.
--echo_settings_file: Imprime las etiquetas del archivo de configuración (no tiene efecto si se usa --format_output o no se provee un archivo).
--io_version=4: Es el único valor válido, por compatibilidad.
--output=<archivo>: Define el archivo donde se escribe la salida (por defecto, stdout).
--error=<archivo>: Define el archivo donde se escriben los mensajes de error (por defecto, stderr).

### 1.1.2. Archivos de entrada y salida
Se utiliza el formato Boulder-IO, en el cual cada registro está compuesto por pares TAG=VALOR y finaliza con una línea que contiene únicamente =.

Ejemplo
```bash
SEQUENCE_ID=example
SEQUENCE_TEMPLATE=GTAGTCAGTAGACNATGACNACTGACGATGCAGACNACACACACACACACAGCACACAGGTATTAGTGGGCCATTCGATCCCGACCCAAATCGATAGCTACGATGACG
SEQUENCE_TARGET=37,21
...
=
```

























