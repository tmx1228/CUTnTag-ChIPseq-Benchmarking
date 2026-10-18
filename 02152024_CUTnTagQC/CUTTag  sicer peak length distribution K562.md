---
---
```shell
WD=/path/to/path/to/external_data/peak\_len
```

#### select the peak file and put comparable pairs into 1 folder

< myPipeline
```shell

    #!/bin/bash
    
    if [ $# -lt 2 ];then
    	echo "Need 2 parameters! <chip> <cuttag> <size> <factor>"
    	exit
    fi
    
    ChIP=$1
    CUTTag=$2
    Size=$3
    Factor=$4
    
    # FILES:
    WD=./
    SICERWD=$WD/sicer
    
    ## library file for Bowtie2
    INDEX=/path/to/lab_data/reference/bowtie2_index/hg38
    ## genome len for samtools
    LEN=/path/to/lab_data/reference/human_genome/chromInfo.txt
    ## hg38.gtf
    GTF=/path/to/lab_data/reference/human_genome/hg38.ncbiRefSeq.gtf
    ## hg38.fa
    FA=/path/to/lab_data/reference/human_genome/hg38.fa
    
    # load module
    module load gcc
    module load bedtools
    
    # set dir
    if [ ! -d "$SICERWD/overlap" ]; then
    	mkdir $SICERWD/overlap
    fi
    
    # Get the input number from the user
    echo "Enter a number (n): "
    sample_size=$Size
    n=`expr $sample_size / 1000000`
    
    mkdir chip
    mkdir cuttag
    # Use a for loop to generate numbers from 1 to n
    for (( i=1; i<=$n; i++ )); do
    	echo $i
    	down_sample_size=`expr $i \* 1000000`
    	chip=$SICERWD/${ChIP}_${down_sample_size}-W200-G600.scoreisland
    	ln -s $chip chip
    	cuttag=$SICERWD/${CUTTag}_${down_sample_size}-W200-G600.scoreisland
    	ln -s $cuttag cuttag
    	
    
```
< myPipeline.slurm
```shell

    #!/bin/bash
    
    # generate trimmed fastq & run fastqc & mapping with pipeline
    #SBATCH --ntasks=1
    #SBATCH --mem-per-cpu=20gb
    #SBATCH --partition=standard
    #SBATCH --time=24:00:00
    #SBATCH --job-name=hm_overlap
    #SBATCH --output=result_%a.out
    #SBATCH --error=result_%a.error
    #SBATCH --account=cphg_cz3d
    #SBATCH --mail-user=contact@example.com
    #SBATCH --mail-type=end
    #SBATCH --array=1-6
    
    #Run program
    
    OPTS=$(sed -n "${SLURM_ARRAY_TASK_ID}"p ../input_files_overlap.txt)  #6
    
    ./myPipeline $OPTS
    
    
```
#### run python script in each folder

> plot_peak_len_line_together.py
```python
import glob

import pandas as pd

import matplotlib.pyplot as plt

import numpy as np

  

files = sorted(glob.glob("*.scoreisland"))

print("Matched files:", files)

  

plt.figure(figsize=(6.3, 4))

  

bins = np.arange(0, 10001, 400)

  

for file in files:

    bed_data = pd.read_table(file, header=None, usecols=[1, 2])

    bed_data["length"] = bed_data[2] - bed_data[1]

  

    print(file, "n =", len(bed_data), "min =", bed_data["length"].min(), "max =", bed_data["length"].max())

  

    counts, edges = np.histogram(

        bed_data["length"],

        bins=bins,

        range=(0, 10000),

        density=True

    )

    centers = (edges[:-1] + edges[1:]) / 2

  

    window = 5

    kernel = np.ones(window) / window

    smooth_counts = np.convolve(counts, kernel, mode="same")

  

    if "ChIP" in file:

        color = "#232D4B"

    elif "CUTTag" in file:

        color = "#E57200"

    else:

        color = "gray"

  

    label = file

  

    plt.plot(

        centers,

        smooth_counts,

        linewidth=2.2,

        color=color,

        label=label

    )

  

plt.legend(frameon=False, fontsize=8)

plt.title("H3K4me3 peak length distribution", fontweight="bold")

plt.xlabel("Peak length (bp)")

plt.ylabel("Density")

plt.xlim(0, 10000)

plt.tight_layout()

plt.savefig("samples.peak_length_density_smooth.png", dpi=300)

plt.close()
```
![Pasted image 20260405015505](assets/Pasted%20image%2020260405015505.png)

> [!ocr-extractor]- Extracted text
> H3K4me3 peak length distribution
> 0.00030 GSM3680226_K562_WT_CUTTag_H3K4me3_PE_26000000-W200-G600.scoreisland
> GSM9A5299 K562 WT _ChIP_H3K4me3_SE_26000000-W200-G600.scoreisland
> 0.00025
> 0.00020
> En
> € 0.00015
> [0]
> a)
> 0.00010
> 0.00005
> 0.00000
> 0 2000 4000 6000 8000 10000
> Peak length (bp)

> SICER peaks are not continuous intervals, so smoothing is needed.


