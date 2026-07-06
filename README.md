# Analisis_Lactiplantibacillus_plantarum
Proyecto de análisis genómico comparativo de cepas probióticas

Objetivo:
Este proyecto tiene como objetivo comparar tres cepas de Lactiplantibacillus plantarum mediante métricas de alineamiento genómico obtenidas a partir de archivos BAM, con el fin de evaluar la calidad de los datos y seleccionar la cepa más adecuada para estudios genómicos posteriores relacionados con aplicaciones en alimentos fermentados como yogur probiótico.

Contexto biologico:
Lactiplantibacillus plantarum es una bacteria láctica con amplio uso en fermentación de alimentos y potencial probiótico.

En un contexto de laboratorio, múltiples cepas pueden ser secuenciadas, pero no todas presentan la misma calidad de datos genómicos. Por ello, es necesario evaluar métricas de alineamiento antes de realizar análisis funcionales más costosos.

🧪 Cepas analizadas
- ERR1554589 (Lp790)
- ERR1554590 (Lp813)
- ERR1554591 (Lp998)

## Pipeline bioinformático:

```text
FASTQ
  ↓
Genoma de referencia
  ↓
BWA MEM
  ↓
SAM → BAM
  ↓
Sorted BAM + Index
  ↓
Script de análisis
  ↓
Tablas + Reporte final
```

Pasos de ejecucion:

##  Ejecución del pipeline

1. Colocar archivos FASTQ en `/data`
2. Ejecutar alineamiento con BWA MEM
3. Convertir SAM a BAM con samtools
4. Ordenar e indexar BAM
5.  Ejecutar script de análisis:


## Metricas evaluadas:

Tabla 1: Calidad de alineamiento
- Total de lecturas
- Lecturas mapeadas
- Lecturas no mapeadas
- Porcentaje de alineamiento


Tabla 2: Calidad de alineamiento
- MAPQ promedio
- Lecturas con MAPQ ≥ 30
- Clasificación de calidad

## Resultado principal 
La cepa con mejor desempeño global fue:
ERR1554591
Esta cepa presentó:
- Mayor porcentaje de alineamiento
- Mayor MAPQ promedio
- Mayor confiabilidad en los alineamientos

Conclusion: La comparación permitió identificar diferencias en la calidad de alineamiento entre cepas de L. plantarum.
La cepa ERR1554591 fue seleccionada como la mejor candidata para estudios genómicos posteriores orientados a aplicaciones en fermentación láctica.

Estructura del proyecto:

```bash
proyecto_LAB/
│
├── data/
├── reference/
├── alignment/
├── results/
└── scripts/
```

## Herramientas utilizadas:
- BWA
- SAMtools
- Bash scripting
- Linux (Cloud Shell)
- FastQ / BAM format analysis



## 📜 Script 2: `tablas_bam.sh`

Este script automatiza el análisis de los archivos **BAM** generados tras el alineamiento de las tres cepas de *Lactiplantibacillus plantarum*.

### Funciones principales

- Verifica que exista el directorio de alineamientos.
- Recorre automáticamente todos los archivos `.sorted.bam`.
- Calcula métricas de alineamiento mediante **SAMtools**.
- Obtiene el porcentaje de lecturas alineadas.
- Calcula el **MAPQ promedio** de cada cepa.
- Cuenta las lecturas con **MAPQ ≥ 30**.
- Genera dos tablas comparativas.
- Selecciona automáticamente la cepa con mejor calidad de alineamiento.
- Crea el reporte final `reporte_bam.txt`.

### Código - bases para entender su funcionamiento y objetivo

```bash
#!/bin/bash

# Proyecto: Análisis de cepas de Lactiplantibacillus plantarum
# Autoras: Ivanna Alexandra y Anadia Nicool

BAM_DIR="../alingment"
OUT="../results/reporte_bam.txt"

# Verificar que exista el directorio de alineamientos
if [ ! -d "$BAM_DIR" ]; then
    echo "No se encontró el directorio de alineamientos."
    exit 1
fi

# Analizar automáticamente cada archivo BAM
for bam in "$BAM_DIR"/*.sorted.bam
do
    cepa=$(basename "$bam" .sorted.bam)

    total=$(samtools view -c "$bam")
    mapped=$(samtools view -F 4 -c "$bam")
    unmapped=$(samtools view -f 4 -c "$bam")

    mapq=$(samtools view "$bam" | \
        awk '{s+=$5;n++} END{if(n>0) printf "%.2f",s/n}')

    mapq30=$(samtools view "$bam" | \
        awk '$5>=30{c++} END{print c+0}')

    # Generación automática de tablas y reporte
done
```

> **Nota:** El código completo del script se encuentra en `scripts/tablas_bam.sh`.







## Autoria:
Ivanna Alexandra Canales Araujo y Anadia Nicool Quispe Zamata

Nota final: Este análisis es una aproximación bioinformática basada en métricas de alineamiento y no representa validación experimental directa del rendimiento fermentativo de las cepas.
