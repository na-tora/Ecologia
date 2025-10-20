# Nos posicionamos en la carpeta donde están nuestras muestras (fastq.gz) y revisamos que estén ahí
```
cd /mnt/c/Users/Julieta/Desktop/FASTQPRUEBA
ls -lh
```
# Creamos una carpeta para nuestros archivos de salida
```
 mkdir -p FastQC_Results
sudo apt update
```
# Instalamos FastQC
```
sudo apt install fastqc default-jre -y
```
# Nos aseguramos que se haya instalado, checando la versión
```
fastqc --versión
```
# Corremos FastQC, * indica todas las muestras con terminación .fastq.gz y -o indica la carpeta donde se guardarán
```
fastqc *.fastq.gz -o FastQC_Results
```
# Ya que termine, verificamos que se crearon los archivos .html y .zip
```
ls -lh FastQC_Results
```
# Ejecutar MultiQC
```
cd FastQC_Results
```

# Lo abrimos en el navegador 
```
explorer.exe .
```
#para sacar un reporte general 
```
multiqc .
```
