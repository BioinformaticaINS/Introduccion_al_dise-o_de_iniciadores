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
Si visualizamos las carpetas clonadas dentro de primer3 deberiamos observar lo siguiente
```bash
ls -lh
```
total 68K

-rwxrwxr-x  1 ins_user ins_user 3,5K Kup  24 10:14 cmp_settings.pl

-rwxrwxr-x  1 ins_user ins_user 3,0K Kup  24 10:14 create_test_folders.pl

-rw-rw-r--  1 ins_user ins_user  368 Kup  24 10:14 example

drwxrwxr-x  2 ins_user ins_user 4,0K Kup  24 10:14 kmer_lists

-rw-rw-r--  1 ins_user ins_user  18K Kup  24 10:14 LICENSE

-rw-rw-r--  1 ins_user ins_user  589 Kup  24 10:14 README.md

drwxrwxr-x  2 ins_user ins_user 4,0K Kup  24 10:14 settings_files

drwxrwxr-x  3 ins_user ins_user 4,0K Kup  24 10:16 src

drwxrwxr-x 13 ins_user ins_user  20K Kup  24 10:17 test



Ahora Trabajemos con primer3

# 1. Primer3
Diseña cebadores de PCR a partir de secuencias de ADN. Es ampliamente utilizado (190 000 resultados en Google para "primer3").
Puedes buscar mas información en:
https://primer3.org/manual.html#inputOutputConventions

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
## Ejercicio: Diseñemos primers para el gen GP del virus del ébola
creamos la carpeta donde vamos a trabajar y entramos en ella
```bash
mkdir my_primers
cd my_primers/
```
Generamos el archivo input
```bash
nano ebola
```
Le damos informacion de la secuencia y parametros que querramos configurar
```bash
SEQUENCE_ID=example
SEQUENCE_TEMPLATE=GATGAAGATTAAGCCGACAGTGAGCGTAATCTTCATCTCTCTTAGATTATTTGTTTTCCAGAGTAGGGGTCGTCAGGTCCTTTTCAATCGTGTAACCAAAA>
SEQUENCE_TARGET=350,292
PRIMER_TASK=generic
PRIMER_PICK_LEFT_PRIMER=1
PRIMER_PICK_INTERNAL_OLIGO=0
PRIMER_PICK_RIGHT_PRIMER=1
PRIMER_OPT_SIZE=20
PRIMER_MIN_SIZE=18
PRIMER_MAX_SIZE=22
PRIMER_PRODUCT_SIZE_RANGE=292-400
PRIMER_EXPLAIN_FLAG=1
=
```
Corremos primer3 y visualizamos el resultado
```bash
primer3_core ebola
```

Podemos hacer el output mas entendible
```bash
primer3_core --format_output ebola
```

## Resultados
Veras un output como este:

Using 0-based sequence positions

OLIGO            start  len      tm     gc%  any_th  3'_th hairpin seq

LEFT PRIMER        269   22   57.40   40.91   29.19  29.19   33.32 AGGTTAGTGATGTCGACAAACT

RIGHT PRIMER       668   20   60.11   55.00    0.00   0.00   42.81 GCGAAAGTCGTTCCTCGGTA

SEQUENCE SIZE: 2406


### Sección "ADDITIONAL OLIGOS"
Esta sección muestra cada par de primers (izquierdo y derecho) junto con sus características:

start: Posición inicial del primer en la secuencia.

len: Longitud del primer (número de nucleótidos).

tm: Temperatura de fusión (en °C), que indica a qué temperatura el primer se desnaturaliza.

gc%: Porcentaje de bases G y C en el primer, lo que influye en la estabilidad.

any_th: Puntaje que evalúa la complementariedad en cualquier parte del primer (riesgo de formar dímeros o estructuras indeseadas).

3'_th: Puntaje específico para la complementariedad en el extremo 3' (muy crítico para evitar la formación de dímeros extendidos).

hairpin: Evalúa la posibilidad de que el primer se doble y forme una estructura tipo "hairpin" (auto-doblamiento).

seq: La secuencia de nucleótidos del primer.


### Sección: "Statistics"
considered:
Indica el número total de candidatos evaluados.

too many Ns:
Se refiere a los candidatos que contienen demasiadas bases ambiguas ("N").

in target:
Se refiere a los candidatos que se encuentran dentro de la región objetivo.

in excl reg:
Se refiere a los candidatos ubicados en regiones que se deben excluir.

not ok reg:
Se refiere a los candidatos que no cumplen con los criterios básicos establecidos.

bad GC%:
Indica los candidatos que presentan problemas con el porcentaje de bases G y C.

tm GC clamp:
Se relaciona con problemas en la estabilidad del "clamp" de GC en la temperatura de fusión.

tm too low:
Se refiere a los candidatos cuya temperatura de fusión es demasiado baja.

tm too high:
Se refiere a los candidatos cuya temperatura de fusión es demasiado alta.

high any_th compl:
Indica los candidatos con alta complementariedad en cualquier parte, lo que podría favorecer la formación de dímeros.

high 3'_th compl:
Indica los candidatos con alta complementariedad en el extremo 3', lo que puede provocar problemas en la amplificación.

hairpin:
Se refiere a los candidatos que tienen tendencia a formar estructuras secundarias tipo "hairpin" (autodoble).

poly pin:
Indica los candidatos afectados por repeticiones de nucleótidos que pueden inducir formación de estructuras indeseadas.

high end stab ok:
Se refiere a los candidatos que cumplen con el criterio de estabilidad en el extremo.

Pair Stats:
Resume la evaluación de los pares de primers, indicando el total de pares considerados, cuántos fueron descartados por tener un tamaño de producto inaceptable y cuántos cumplen todos los criterios.


## Analicemos la especifidad de mis primers
Activemos el ambiente bioinfo
```bash
conda activate bioinfo
```
Instalemos el programa ispcr
```bash
conda install bioconda::ispcr
```

**Sintaxis**
isPcr sequence_genome.fasta/fna   primers.txt   amplicons.txt

_sequence_genome.fasta/fna_

Creamos la carpeta data y entramos
```bash
mkdir data
cd data/
```
Copia el archivo genome_ebola.fna del genoma del virus del ebola en data

_primers.txt_

Creamos el archivo txt que contiene los primers 
El archivo de primers debe tener la siguiente estructura:
NOME DO PRIMER <TAB> PRIMER1 <TAB> PRIMER2

```bash
nano primers.txt
primer1 AGGTTAGTGATGTCGACAAACT GCGAAAGTCGTTCCTCGGTA
```

_Corremos isPcr para buscar amplicones_

```bash
isPcr genome_ebola.fna primers.txt amplicons.txt
```













