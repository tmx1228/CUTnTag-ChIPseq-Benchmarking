---
---
```shell
bowtie2 -p 8 -X 2000 -3 115 -x /path/to/lab_data/Data/Mapping\_index/bowtie2/hg38\_clean -1 /path/to/lab_data/Project/TCF1\_Tcell/Data/fastq/CART/bulkATACb3/${R}\_p1.fastq.gz -2 /path/to/lab_data/Project/TCF1\_Tcell/Data/fastq/CART/bulkATACb3/${R}\_p2.fastq.gz -S ${R}.sam 2>&1 >>/dev/null | tee ${R}\_bowtie2PE.out

# filtering & samTObam

samtools view -bS ${R}.sam > ${R}\_raw.bam

samtools view -H ${R}\_raw.bam > ${R}\_filtered.sam

samtools view -f 0x2 ${R}\_raw.bam | awk 'NR % 2 == 1{mapq=$5;forward=$0} NR % 2 == 0{if($5>=30 && mapq>=30 && substr($3,1,3)"chr" && $7"=" ) print forward"\\n"$0}' >> ${R}\_filtered.sam

samtools view -bS ${R}\_filtered.sam > ${R}.bam

wait;

rm ${R}.sam ${R}\_filtered.sam

# bed and rdSEbed

bamToBed -i ${R}.bam -bedpe | awk '{OFS="\\t"; print $1,$2,$6,$7,$8,"."}' | sort -k 1,1 -k 2,2g -k 3,3g|cut -f 1-3 |uniq > ${R}\_PEuniq.bed

python /path/to/lab_data/Script/general/PEbed\_To\_rdSEbed.py -i ${R}\_PEuniq.bed -o ${R}\_rdSE.bed

# callpeak

#macs2 callpeak -g hs -B -f BED -q 0.01 --SPMR --keep-dup all --nomodel --extsize 146 -t ${R}\_rdSE.bed -n ${R}\_m2rdSE

#mv ${R}\_m2rdSE\_treat\_pileup.bdg ${R}\_m2rdSE.bdg

#bdg2bw ${R}\_m2rdSE.bdg /path/to/lab_data/Data/Genome/hg38/hg38\_clean.len

macs2 callpeak -g hs -B -f BED -q 0.01 --SPMR --keep-dup all --nomodel --extsize 146 -t ${R}\_rdSE.bed -n ${R}\_m2rdSE

mv ${R}\_m2rdSEll\_treat\_pileup.bdg ${R}.bdg

bdg2bw ${R}.bdg /path/to/lab_data/Data/Genome/hg38/hg38\_clean.len

## BEDPE

/path/to/external_data/path/to/external_data/combineReads/PEdata
