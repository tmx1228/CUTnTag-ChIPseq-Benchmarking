```shell
#!/bin/bash

set -euo pipefail

  

LIST=input_files.txt

REFWD=/path/to/lab_data/reference/human_genome

TSS=$REFWD/hg38_4k_promoter_geneID.bed

OUTDIR=tss_distance

  

mkdir -p "$OUTDIR"

  

while read -r prefix; do

    [[ -z "$prefix" ]] && continue

  

    f="${prefix}.bed"

  

    if [[ ! -f "$f" ]]; then

        echo "Warning: file not found: $f" >&2

        continue

    fi

  

    bedtools closest -a "$f" -b "$TSS" -d > "${OUTDIR}/${prefix}.closest_tss.tsv"

  

done < "$LIST"
```