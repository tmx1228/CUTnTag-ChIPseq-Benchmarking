---
---
```shell

wd=(base) researcher:/path/to/path/to/external_data/sicer$

    
    cuttag=GSM3680226_K562_WT_CUTTag_H3K4me3_PE_10000000-W200-G600.scoreisland
    chip=GSM945299_K562_WT_ChIP_H3K4me3_SE_10000000-W200-G600.scoreisland
    
    intersectBed -a $cuttag -b $chip -v > test_cuttag_u.bed
    intersectBed -b $cuttag -a $chip -v > test_chip_u.bed
    
    awk -F'\t' 'BEGIN{SUM=0}{ SUM+=$3-$2 }END{print SUM}' test_chip_u.bed
    awk -F'\t' 'BEGIN{SUM=0}{ SUM+=$3-$2 }END{print SUM}'  test_cuttag_u.bed
    
    
```
#### new strategy to plot the overlap length:

> ChIP unique peak length
> overlap peak length
> CUTTag unique peak length

![[Projects/02152024_CUTnTagQC/CUTTag__HM_sicer_peak__ChIP_unique_peak_and_CUTTag_unique_peak.resources/unknown_filename.png]]


