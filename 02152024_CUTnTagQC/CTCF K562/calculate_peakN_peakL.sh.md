```shell
#!/bin/bash

set -euo pipefail

  

LIST=input_files.txt

OUT=peak_set_summary.tsv

  

echo -e "peak_set\tn_peaks\ttotal_bp\tmedian_len\tmean_len" > "$OUT"

  

while read -r prefix; do

    [[ -z "$prefix" ]] && continue

  

    f="${prefix}.bed"

  

    if [[ ! -f "$f" ]]; then

        echo "Warning: file not found: $f" >&2

        continue

    fi

  

    awk -v name="$prefix" '

    BEGIN{OFS="\t"}

    {

        len=$3-$2

        if(len>0){

            n++

            sum+=len

            a[n]=len

        }

    }

    END{

        if(n==0){

            print name,0,0,"NA","NA"

            exit

        }

        asort(a)

        if(n%2==1){

            med=a[(n+1)/2]

        } else {

            med=(a[n/2]+a[n/2+1])/2

        }

        mean=sum/n

        print name,n,sum,med,mean

    }' "$f" >> "$OUT"

  

done < "$LIST"
```