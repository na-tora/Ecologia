# Adaptadores
Como nuestras muestras contienen adaptadores, es necesario hacer un recorte
Recordando que un adaptador es una secuencia de ADN Sintéticos muy especificos que se unen a los extremos de los fragmentos de ADN que queremos amplificar. Es importante revisar la cantidad de adaptadores que se obtienen debido a que luego hay más adaptadores que de el fragmeto de interes; en nuestro caso hay muchos adaptadores por lo que es necesario hacer un recorte.
<img width="1200" height="800" alt="fastqc_adapter_content_plot" src="https://github.com/user-attachments/assets/92c6ecb8-7295-4416-b84b-52bd7ca19787" />

Trimmomatic (recorte)

Trimmomatic suele usarse para quitar adaptadores y recortar bases de baja calidad.

Parámetros clave (ejemplo):

ILLUMINACLIP:adapters.fasta:2:30:10 → quita adaptadores usando archivo adapters.fasta

LEADING:3 → quita bases con calidad <3 al inicio

TRAILING:3 → quita bases con calidad <3 al final

SLIDINGWINDOW:4:20 → ventana de 4 bases, recorta cuando el promedio <20

MINLEN:50 → descarta lecturas <50 nt después de recorte
# 1. Instalar Conda (en caso de que no lo tengamos)
Usaremos el comando wget para descargar el instalador
```
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
```
#### 1.2 Verifica que se descargó
```
ls -lh Miniconda3-latest-Linux-x86_64.sh
```
#### 1.3 Ejecuta el instalador
```
bash Miniconda3-latest-Linux-x86_64.sh
```
#### 1.4 Activa Conda
```
source ~/.bashrc
```
#### 1.5 Verificar la versión 
```
conda --version
```
# 2. Preparar el entorno 
### opcional: crear y activar un entorno conda (recomendado)
```
conda create -n qc_env -c conda-forge -c bioconda fastqc multiqc trimmomatic fastp -y
conda activate qc_env
```
## 2. FastQC inicial (evaluación antes del recorte)
```
mkdir -p 01_fastqc_inicial

fastqc -t 4 *.fastq.gz -o 01_fastqc_inicial/
```



