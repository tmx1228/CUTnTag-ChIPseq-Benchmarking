```python

import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

# =========================
# 1. read input files
# =========================

peak_file = "ChIP_only_with_motif.bed"
tss_file = "/path/to/lab_data/reference/human_genome/hg38_tss_1bp_strand.sorted.bed"

peaks = pd.read_csv(
    peak_file,
    sep="\t",
    header=None,
    usecols=[0, 1, 2],
    names=["chr", "start", "end"]
)

tss = pd.read_csv(
    tss_file,
    sep="\t",
    header=None,
    usecols=[0, 1, 2, 3, 4, 5],
    names=["chr", "start", "end", "gene", "score", "strand"]
)

# peak midpoint
peaks["mid"] = ((peaks["start"] + peaks["end"]) / 2).astype(int)

# TSS position
tss["tss"] = tss["start"]   # since TSS bed should be 1bp, start is enough


# =========================
# 2. nearest TSS by chromosome
# =========================

results = []

for chrom in sorted(peaks["chr"].unique()):
    peak_sub = peaks[peaks["chr"] == chrom].copy()
    tss_sub = tss[tss["chr"] == chrom].copy()

    if len(peak_sub) == 0 or len(tss_sub) == 0:
        continue

    peak_sub = peak_sub.sort_values("mid").reset_index(drop=True)
    tss_sub = tss_sub.sort_values("tss").reset_index(drop=True)

    tss_positions = tss_sub["tss"].to_numpy()

    for _, row in peak_sub.iterrows():
        mid = row["mid"]

        # binary search nearest TSS
        idx = np.searchsorted(tss_positions, mid)

        candidates = []
        if idx > 0:
            candidates.append(idx - 1)
        if idx < len(tss_positions):
            candidates.append(idx)

        best_idx = None
        best_abs_dist = None

        for j in candidates:
            d = abs(mid - tss_positions[j])
            if best_abs_dist is None or d < best_abs_dist:
                best_abs_dist = d
                best_idx = j

        nearest = tss_sub.iloc[best_idx]
        tss_pos = nearest["tss"]
        strand = nearest["strand"]

        # signed distance relative to transcription direction
        if strand == "+":
            signed_dist_bp = mid - tss_pos
        else:
            signed_dist_bp = tss_pos - mid

        results.append({
            "chr": chrom,
            "peak_mid": mid,
            "nearest_gene": nearest["gene"],
            "strand": strand,
            "tss": tss_pos,
            "signed_dist_bp": signed_dist_bp,
            "signed_dist_kb": signed_dist_bp / 1000.0
        })

dist_df = pd.DataFrame(results)
dist_df.to_csv("peak_to_nearest_TSS_signed_distance.tsv", sep="\t", index=False)


# =========================
# 3. bin distances
# =========================

# bins in kb, matching the style in your figure
bins = [-np.inf, -500, -50, -5, 0, 5, 50, 500, np.inf]
labels = ["<-500", "-500 to -50", "-50 to -5", "-5 to 0",
          "0 to 5", "5 to 50", "50 to 500", ">500"]

dist_df["bin"] = pd.cut(
    dist_df["signed_dist_kb"],
    bins=bins,
    labels=labels,
    right=False
)

count_series = dist_df["bin"].value_counts(sort=False)
percent_series = count_series / count_series.sum() * 100

plot_df = pd.DataFrame({
    "bin": labels,
    "count": count_series.reindex(labels).fillna(0).astype(int).values,
    "percent": percent_series.reindex(labels).fillna(0).values
})


# =========================
# 4. plot
# =========================

fig, ax = plt.subplots(figsize=(6, 5))

x = np.arange(len(plot_df))
bars = ax.bar(x, plot_df["percent"], edgecolor="black")

# x labels
ax.set_xticks(x)
ax.set_xticklabels(plot_df["bin"], rotation=45, ha="right")

# y label
ax.set_ylabel("Region-gene associations")
ax.set_xlabel("Distance to TSS (kb)")

# counts on bars
for i, (pct, cnt) in enumerate(zip(plot_df["percent"], plot_df["count"])):
    ax.text(i, pct + 0.5, str(cnt), ha="center", va="bottom", fontsize=9)

# TSS arrow between -5~0 and 0~5
ax.annotate(
    "TSS",
    xy=(4, max(plot_df["percent"]) * 1.02),
    xytext=(3.5, max(plot_df["percent"]) * 1.12),
    arrowprops=dict(arrowstyle="-|>", lw=1.5),
    ha="center"
)

ax.set_ylim(0, max(plot_df["percent"]) * 1.2)
ax.set_title("Binned by orientation and distance to TSS", fontweight="bold")

plt.tight_layout()
plt.savefig("binned_orientation_distance_to_TSS.pdf")
plt.savefig("binned_orientation_distance_to_TSS.png", dpi=300)
plt.show()
```