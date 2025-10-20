# 1. Verificamos que tengamos todo
fastp --version
mkdir -p Trimmed FastQC_afterTrim logs
NTHREADS=4
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
fastqc -t 4 Trimmed/*_R1.trim.fastq.gz Trimmed/*_R2.trim.fastq.gz -o FastQC_afterTrim
cd FastQC_afterTrim
multiqc .
explorer.exe .
