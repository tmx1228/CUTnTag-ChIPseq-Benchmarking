```python
import pandas as pd

import matplotlib.pyplot as plt

from textwrap import fill

from matplotlib.lines import Line2D

  

# =========================

# 1. read input

# =========================

df = pd.read_csv("go_results.tsv", sep="\t")

  

# fix typo

df["categorry"] = df["categorry"].replace({

    "shared onfident": "shared confident"

})

  

# color score

df["score"] = -df["logP"]

  

# =========================

# 2. set plotting order

# =========================

group_order = [

    "shared confident",

    "ChIP-seq confident",

    "CUT&Tag confident",

    "ChIP-seq no motif",

    "CUT&Tag no motif",

    "ChIP-seq bias",

    "CUT&Tag bias"

]

  

# order terms by max score across groups

term_order = (

    df.groupby("Term")["score"]

    .max()

    .sort_values(ascending=False)

    .index

    .tolist()

)

  

df["categorry"] = pd.Categorical(df["categorry"], categories=group_order, ordered=True)

df["Term"] = pd.Categorical(df["Term"], categories=term_order, ordered=True)

  

# numeric positions

x_map = {g: i for i, g in enumerate(group_order)}

y_map = {t: i for i, t in enumerate(term_order)}

  

df["x"] = df["categorry"].map(x_map)

df["y"] = df["Term"].map(y_map)

  

# =========================

# 3. plot main panel

# =========================

fig, ax = plt.subplots(figsize=(11, 7.5))

  

size_scale = 12  # main bubble scaling

  

sc = ax.scatter(

    df["x"],

    df["y"],

    s=df["# of Genes in Term"] / size_scale,

    c=df["score"],

    cmap="viridis",

    edgecolors="black",

    linewidths=0.4

)

  

# x axis

ax.set_xticks(range(len(group_order)))

ax.set_xticklabels(group_order, rotation=30, ha="right")

  

# y axis with wrapped labels

wrapped_terms = [fill(t, width=28) for t in term_order]

ax.set_yticks(range(len(term_order)))

ax.set_yticklabels(wrapped_terms, rotation=0, ha="right", va="center")

  

# first term at top

ax.invert_yaxis()

  

ax.set_xlabel("")

ax.set_ylabel("")

  

# leave room on right for size legend + colorbar

plt.subplots_adjust(left=0.42, right=0.78, bottom=0.18, top=0.95)

  

# =========================

# 4. shorter colorbar on right-lower side

# =========================

# [left, bottom, width, height] in figure coordinates

cax = fig.add_axes([0.81, 0.20, 0.025, 0.46])

cbar = fig.colorbar(sc, cax=cax)

cbar.set_label("-log10(FDR/q)")

  

# =========================

# 5. size legend above colorbar

# =========================

# use custom display sizes instead of real bubble areas

legend_labels = [100, 1000, 5000, 10000]

legend_marker_sizes = [3, 7, 11, 15]

  

handles = [

    Line2D(

        [0], [0],

        marker="o",

        color="black",

        markerfacecolor="black",

        linestyle="",

        markersize=ms

    )

    for ms in legend_marker_sizes

]

  

fig.legend(

    handles,

    [str(s) for s in legend_labels],

    title="# of Genes",

    frameon=False,

    loc="upper left",

    bbox_to_anchor=(0.79, 0.88),

    labelspacing=0.8,

    handletextpad=0.8,

    borderpad=0.3

)

  

# =========================

# 6. save

# =========================

plt.savefig("go_bubble_plot.pdf")

plt.savefig("go_bubble_plot.png", dpi=300)

plt.show()
```