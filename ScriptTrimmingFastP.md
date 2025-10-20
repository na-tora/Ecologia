# 1. Verificamos que tengamos todo
Sabremos que estan instaladas si todas nos da un número de versión.
```
fastp --version
```
# 2. Creamos todas las carpetas necesarias
Trimmed → donde guardará los archivos recortados sin adaptadores.

FastQC_afterTrim → reportes de FastQC después del trimming.

logs → donde se guardan los registros de ejecución; es decir todo el proceso.
```
mkdir -p Trimmed FastQC_afterTrim logs
```
# 3. Definimos el número de nucleos
Esto permite que el análisis sea más rápido; va a depender de tu computadora.Puedes cambiar el número 4 por la cantidad de núcleos disponibles (por ejemplo 2, 8 o 12).
```
NTHREADS=4
```
# 4. Bucle para recorrer los archivos FASTQ
```
for r1 in *_R1.fastq.gz; do
  r2="${r1/_R1/_R2}"
  if [ -f "$r2" ]; then
    sample="${r1%%_R1*}"
    echo "Procesando: $sample"
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
  else
    echo "Pareja no encontrada para $r1 -> $r2" | tee -a logs/missing_pairs.txt
  fi
done
```
# 5. FastQC sobre archivos recortados
Se vuelve a correr FastQC, para crear nuevos reportes de calidad después del trimming (ya sin adaptadores) en la carpeta FastQC_afterTrim.
```
fastqc -t 4 Trimmed/*_R1.trim.fastq.gz Trimmed/*_R2.trim.fastq.gz -o FastQC_afterTrim
cd FastQC_afterTrim
```
# 6. Resumen global
```
Crea un archivo llamado multiqc_report.html con el resumen global.
multiqc .
explorer.exe .
```
