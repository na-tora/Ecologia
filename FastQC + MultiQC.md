# Análisis Genómico: FastQC + MultiQC

Este script documenta el flujo de trabajo para evaluar la calidad de secuencias genómicas usando FastQC y sintetizar los resultados con MultiQC. 

---

## 1. Creacion de nuestra carpeta 
Una vez dentro de nuestra carpeta crearemos una carpeta con el nombre: bacterias 
```bash
mkdir bacterias
```

## 2. Copiaremos los archivos de Pseudomonas en nuestra carpeta 
```bash
cp -r /home/lab13/Documents/Pseudomonas/* /home/lab13/Documents/Ecologia_AnalisisGenomico/TorresAna/bacterias/
```
confirmar que todos los archivos esten dentro de nuestra carpeta llamada bacterias

## 3. Confirmaremos que tengamos las herramientas de trabajo
En nuestro caso usaremos Fastq y multiqc
```bash
fastqc --version
multiqc --version
```
### 3.1 En caso de no tenerlas las instalaremos con conda 
```bash
conda install -c bioconda fastqc multiqc
```

## 4. Análisis de los archivos Fastq con fastqc

Primero creamos una carpeta donde se guardaran todos los archivos del análisis 
```bash
mkdir fastqc_results
```

Fastqc realiza un reporte de calidad por cada archivo
Este código es en caso de que los archivos no este comprimidos, es decir con la terminacion .gz
```
for file in *.fastq; do
    fastqc "$file" -o fastqc_results/
done
```
En caso de que los archivos se encuentren comprimidos (con terminación .gz) usaremos el siguiente código 
```
for file in *.fastq.gz; do
    fastqc "$file" -o fastqc_results/
done
```
### 4.1 Crearemos un reporte general 
```
cd fastqc_results
multiqc .
```








