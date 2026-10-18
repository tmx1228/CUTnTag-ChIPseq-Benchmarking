---
---
```shell
#!/bin/bash
    
    if [ $# -lt 2 ];then
        echo "Need 2 parameters! <name> <size>"
        exit
    fi
    
    R=$1
    P=$2
    
    # FILES:
    WD=../
    BEDWD=$WD/bedfiles
    
    ## library file for Bowtie2
    INDEX=/path/to/lab_data/reference/bowtie2_index/hg38
    ## genome len for samtools
    LEN=/path/to/lab_data/reference/human_genome/chromInfo.txt
    ## hg38.gtf
    GTF=/path/to/lab_data/reference/human_genome/hg38.knownGene.gtf
    ## hg38.fa
    FA=/path/to/lab_data/reference/human_genome/hg38.fa
    
    # load module
    module load gcc
    
    # Read the input bed file
    input_bed=$BEDWD/${R}.bed
    
    
    # Get the input number from the user
    echo "Enter a number (n): "
    sample_size=$P
    n=`expr $sample_size / 1000000` 
    # Use a for loop to generate numbers from 1 to n
    for (( i=1; i<=$n; i++ )); do
        echo $i
        down_sample_size=`expr $i \* 1000000`
        output_bed=${R}_${down_sample_size}.bed
    	# Check if the input bed file exists
    	if [ ! -f "$input_bed" ]; then
    	    echo "Input bed file not found: $input_bed"
    	    exit 1
    	fi
    	
    	# Randomly sample down the bed file
    	shuf $input_bed | head -n $down_sample_size > $output_bed
    	
    	echo "Bed file sampled down to $down_sample_size lines and saved to $output_bed"
    done
    
    #down_sample_size=200000
    #output_bed=${R}_${down_sample_size}.bed
    #shuf $input_bed | head -n $down_sample_size > $output_bed
    #echo "Bed file sampled down to $down_sample_size lines and saved to $output_bed"
    #
    #down_sample_size=500000
    #output_bed=${R}_${down_sample_size}.bed
    #shuf $input_bed | head -n $down_sample_size > $output_bed
    #echo "Bed file sampled down to $down_sample_size lines and saved to $output_bed"
    



```
	