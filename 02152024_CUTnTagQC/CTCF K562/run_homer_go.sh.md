```shell
#!/bin/bash

set -euo pipefail

  

LIST=input_files.txt

GENOME=hg38

OUTDIR=homer_GO_results

  

mkdir -p "$OUTDIR"

  

while read -r prefix; do

    [[ -z "$prefix" ]] && continue

    [[ "$prefix" =~ ^# ]] && continue

  

    BED="${prefix}.bed"

  

    if [[ ! -f "$BED" ]]; then

        echo "Warning: missing $BED" >&2

        continue

    fi

  

    echo "Running GO/pathway for $BED"

  

    annotatePeaks.pl "$BED" "$GENOME" -go "${OUTDIR}/${prefix}" \

        > "${OUTDIR}/${prefix}.annot.txt"

  

done < "$LIST"
```