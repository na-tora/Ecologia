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
# 1. Verificamos que las herramientas estén instaladas.
sabremos q estan instaladas si todas nos da un número de versión.
```
fastp --version
fastqc --version
multiqc --version
```
# 2. Creamos las carpetas necesarias
Trimmed → donde guardará los archivos recortados sin adaptadores. 

FastQC_afterTrim → reportes de FastQC después del trimming.

logs → donde se guardan los registros de ejecución; es decir todo el proceso. 
```
mkdir -p Trimmed FastQC_afterTrim logs
```
# 3. Definimos el número de núcleos
Esto permite que el análisis sea más rápido; va a depender de tu computadora.Puedes cambiar el número 4 por la cantidad de núcleos disponibles (por ejemplo 2, 8 o 12).
```
NTHREADS=4
```
# 4. Bucle para recorrer los archivos FASTQ
El siguiente bloque busca todos los archivos que terminan en _R1.fastq.gz (cadena sentido) y busca su pareja _R2.fastq.gz (cadena antisentido).
```
for r1 in *_R1.fastq.gz; do
  r2="${r1/_R1/_R2}"
  if [ -f "$r2" ]; then
    sample="${r1%%_R1*}"
    echo "Procesando: $sample"
```
# 5. fastp
Limpia las lecturas de los adaptadores

--detect_adapter_for_pe → detecta automáticamente adaptadores

--qualified_quality_phred 20 → corta bases con calidad baja (< Q20)

--length_required 50 → descarta lecturas más cortas que 50 bp

--html y --json → genera reportes por muestra

> logs/... → guarda los mensajes de consola en archivos de log
```
fastp \
  -i "$r1" -I "$r2" \
  -o "Trimmed/${sample}_R1.trim.fastq.gz" \
  -O "Trimmed/${sample}_R2.trim.fastq.gz" \
  --detect_adapter_for_pe \
  --qualified_quality_phred 20 \
  --length_required 50 \
  --thread "$NTHREADS" \
  --html "Trimmed/${sample}_fastp.html" \
  --json "Trimmed/${sample}_fastp.json" \
  > "logs/${sample}_fastp.log" 2>&1
```
# 6. Si no encuentra la pareja
La siguiente parte del código evita errores si falta un archivo _R2 y deja constancia en logs/missing_pairs.txt.
```
else
  echo "Pareja no encontrada para $r1 -> $r2" | tee -a logs/missing_pairs.txt
fi
```
# 7. FastQC sobre archivos recortados
Esto crea reportes de calidad nuevos después del trimming (ya sin adaptadores) en la carpeta FastQC_afterTrim.
```
fastqc -t 4 Trimmed/*_R1.trim.fastq.gz Trimmed/*_R2.trim.fastq.gz -o FastQC_afterTrim
```
# 8. Resumen global
Crea un archivo llamado multiqc_report.html con el resumen global.
```
cd FastQC_afterTrim
multiqc .
```





