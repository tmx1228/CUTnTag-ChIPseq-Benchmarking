wd:/path/to/path/to/external_data/chip/sy_data
local wd:/path/to/local_data/0215_CUTnTagQC/combine_analysis/PE_SE_comparison
```python
# I have a macs2 peak file names $PE, and another macs2 peak file names $SE, I want to compare the peak length distribution for these 2 files in a shell script, the bins size for hist plot should be the same to be comparable(can use a python script, I want to save the file name including ${R} and plot title ${R} and $R is given in shell scripts.).
# Peak length distribution hist for every sample

import sys
import numpy as np
import matplotlib.pyplot as plt

def read_peak_lengths(file_path):
    """Read a BED file and return a list of peak lengths"""
    lengths = []
    try:
        with open(file_path, 'r') as f:
            for line in f:
                if line.startswith('#'):  # Skip comment lines
                    continue
                parts = line.strip().split()
                if len(parts) < 3:
                    continue
                start = int(parts[1])
                end = int(parts[2])
                lengths.append(end - start)
    except FileNotFoundError:
        print(f"Error: file {file_path} not found!")
        sys.exit(1)
    return lengths

def main():
    if len(sys.argv) != 5:
        print("Usage: python plot_peak_hist.py <PE file> <SE file> <output filename> <title>")
        sys.exit(1)

    pe_file = sys.argv[1]
    se_file = sys.argv[2]
    output_file = sys.argv[3]
    title = sys.argv[4]

    # Read peak lengths
    pe_lengths = read_peak_lengths(pe_file)
    se_lengths = read_peak_lengths(se_file)

    if not pe_lengths or not se_lengths:
        print("Error: no valid peak data in input files!")
        sys.exit(1)

    # Calculate global bin range
    all_scores = scores1 + scores2
    min_score = min(all_scores)
    max_score = max(all_scores)
    bins = np.linspace(min_score, max_score, 100)  # Use 100 bins

    # Plot histogram
    plt.figure(figsize=(10, 6))
    plt.hist(
        pe_lengths,
        bins=bins,
        alpha=0.5,
        label='PE Peaks',
        color='blue'
    )
    plt.hist(
        se_lengths,
        bins=bins,
        alpha=0.5,
        label='SE Peaks',
        color='red'
    )
    plt.xlabel('Peak Length (bp)')
    plt.ylabel('Frequency')
    plt.title(title)
    plt.legend()
    plt.grid(True, linestyle='--', alpha=0.7)
    plt.savefig(output_file, dpi=300, bbox_inches='tight')
    plt.close()

if __name__ == '__main__':
    main()




# Extract peak lengths, skipping header lines starting with '#'
awk '!/^#/ {if ($3 > $2) print $3 - $2}' "$PE" > pe_lengths_${R}.txt
awk '!/^#/ {if ($3 > $2) print $3 - $2}' "$SE" > se_lengths_${R}.txt

# Generate the plot using Python
/path/to/lab_data/bin/anaconda3/envs/py3.6/bin/python plot_peak_lengths.py pe_lengths_${R}.txt se_lengths_${R}.txt "${R}_peak_dist.png" "Peak Length Distribution: $R"

# Cleanup temporary files
rm -rf  pe_lengths_${R}.txt se_lengths_${R}.txt



# Generate the plot using Python
/path/to/lab_data/bin/anaconda3/envs/py3.6/bin/python plot_peak_lengths.py ${R}_PE_motif_scores.txt ${R}_sudo_SE_motif_scores.txt "${R}_peak_scores.png" "Peak Score Distribution: $R"

# Cleanup temporary files
rm -rf  ${R}_PE_motif_scores.txt ${R}_sudo_SE_motif_scores.txt





# Generate the hist plot for every sample using Python
/path/to/lab_data/bin/anaconda3/envs/py3.6/bin/python plot_peak_lengths.py dist_${R}_PE_peaksWmotif_summits_wMotif.txt dist_${R}_SE_peaksWmotif_summits_wMotif.txt "${R}_peak_distance_to_motif.png" "Peak Distance to CTCF Motif Distribution: $R"

# Cleanup temporary files
rm -rf  ${R}_PE_peaksWmotif_summits.bed ${R}_SE_peaksWmotif_summits.bed dist_${R}_PE_peaksWmotif_summits_wMotif.txt dist_${R}_SE_peaksWmotif_summits_wMotif.txt
```