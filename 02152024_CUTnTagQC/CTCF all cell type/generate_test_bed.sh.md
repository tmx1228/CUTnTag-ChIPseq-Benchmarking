```shell
bam=/path/to/path/to/external_data/PE/bamfiles/GSM9252905_ChIPseq_CTCF_Jurkat_resting.q30.name_sorted.bam
bedtools bamtobed -bedpe -i $bam > test.bedpe

awk 'BEGIN{OFS="\t"} {
    start = ($2<$5?$2:$5);
    end   = ($3>$6?$3:$6);
    print $1, start, end, $7, $8, $9;
}' test.bedpe > test.fragments.bed

```