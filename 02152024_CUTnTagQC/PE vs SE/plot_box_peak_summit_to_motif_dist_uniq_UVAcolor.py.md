```python
#!/usr/bin/env python3
import sys
import numpy as np
import matplotlib.pyplot as plt
from scipy.stats import mannwhitneyu

def read_values(file_path):
    values = []
    with open(file_path, "r") as f:
        for line in f:
            line = line.strip()
            if not line or line.startswith("#"):
                continue
            try:
                values.append(float(line))
            except ValueError:
                continue
    return values

def format_pvalue(p):
    if p < 1e-4:
        return f"p = {p:.1e}"
    else:
        return f"p = {p:.4f}"

def add_pvalue(ax, x1, x2, y, h, text):
    ax.plot([x1, x1, x2, x2], [y, y+h, y+h, y], lw=0.9, c="black")
    ax.text((x1 + x2) / 2, y + h, text, ha="center", va="bottom", fontsize=7)

def main():
    if len(sys.argv) != 7:
        print("Usage: python plot_box_peak_summit_to_motif_dist_uniq.py <filePE> <fileSE> <filePEuniq> <fileSEuniq> <output> <title>")
        sys.exit(1)

    filePE, fileSE, filePEuniq, fileSEuniq, output, title = sys.argv[1:7]

    pe = read_values(filePE)
    se = read_values(fileSE)
    pe_uniq = read_values(filePEuniq)
    se_uniq = read_values(fileSEuniq)

    data = [pe, se, pe_uniq, se_uniq]
    labels = ["PE", "pseudo SE", "PE uniq", "pseudo SE uniq"]
    colors = ["#E57200", "#232D4B", "#E57200", "#232D4B"]

    for path, values in zip([filePE, fileSE, filePEuniq, fileSEuniq], data):
        if len(values) == 0:
            print(f"Error: {path} has no valid numeric values.")
            sys.exit(1)

    plt.rcParams.update({
        "font.family": "Arial",
        "font.size": 9,
        "axes.titlesize": 10,
        "axes.labelsize": 9,
        "xtick.labelsize": 8,
        "ytick.labelsize": 8
    })

    fig, ax = plt.subplots(figsize=(3.0, 2.8), dpi=300)

    bp = ax.boxplot(
        data,
        tick_labels=labels,
        patch_artist=True,
        showfliers=False,
        widths=0.55,
        medianprops=dict(color="black", linewidth=1.8),
        whiskerprops=dict(color="black", linewidth=1.0),
        capprops=dict(color="black", linewidth=1.0),
        boxprops=dict(edgecolor="black", linewidth=1.0)
    )

    for patch, color in zip(bp["boxes"], colors):
        patch.set_facecolor(color)
        patch.set_alpha(0.75)

    ax.set_ylabel("Peak summit to motif distance (bp)", fontweight="bold")
    ax.set_title(title, fontweight="bold", pad=4, fontsize=10)
    ax.tick_params(axis="x", labelrotation=28)

    ax.grid(axis="y", color="#DDDDDD", linestyle="-", linewidth=0.6, alpha=0.8)
    ax.set_facecolor("white")
    ax.spines["top"].set_visible(False)
    ax.spines["right"].set_visible(False)

    comparisons = [
        (1, 2, pe, se),
        (1, 3, pe, pe_uniq),
        (3, 4, pe_uniq, se_uniq)
    ]

    all_values = pe + se + pe_uniq + se_uniq
    y_min = 0
    y_top = np.percentile(all_values, 99)

    step = max(y_top * 0.05, 1.5)
    h = max(y_top * 0.03, 0.8)
    base_y = y_top - step * 14

    for i, (x1, x2, group1, group2) in enumerate(comparisons):
        stat, p = mannwhitneyu(group1, group2, alternative="two-sided")
        p_text = format_pvalue(p)
        y = base_y + i * step
        add_pvalue(ax, x1, x2, y, h, p_text)

    ax.set_ylim(y_min, base_y + len(comparisons) * step + h + step)

    plt.tight_layout()
    plt.savefig(output, dpi=300, bbox_inches="tight")
    plt.close()

if __name__ == "__main__":
    main()
```