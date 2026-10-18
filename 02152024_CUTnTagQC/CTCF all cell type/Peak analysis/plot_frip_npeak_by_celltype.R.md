wd:(r4) researcher:/path/to/path/to/external_data
```R
# ==== Load packages ====
library(dplyr)
library(tidyr)
library(ggplot2)

data <- read.table("data.txt", header = 1, sep = "\t")

# ==== Set colors for cell types ====
cell_colors <- c(
  "A673" = "#1f77b4",         # blue
  "B35T1NC" = "#ff7f0e",      # orange
  "BEP2DNC" = "#2ca02c",      # green
  "BEP2DOE" = "#d62728",      # red
  "CACO2" = "#9467bd",        # purple
  "Caconeg2" = "#8c564b",     # brown
  "cell" = "#e377c2",         # pink-purple
  "DLD1" = "#7f7f7f",         # gray
  "DLDneg1" = "#bcbd22",      # yellow-green
  "expanded" = "#17becf",     # cyan
  "HCF" = "#aec7e8",          # light blue
  "HCT116" = "#ffbb78",       # light orange
  "Hippocampus" = "#98df8a",  # light green
  "HSPCs" = "#ff9896",        # light red
  "IMR90" = "#c5b0d5",        # light purple
  "Jurkat" = "#c49c94",       # light brown
  "TC71" = "#f7b6d2",         # light pink
  "Tcell" = "#c7c7c7",        # light gray
  "TTCneg466" = "#dbdb8d",    # beige
  "WTC11" = "#9edae5"         # light cyan
)

# ==== Data preparation ====

# FRiP in long format
frip_long <- data %>%
  select(sample, cell, frip_macs, frip_sicer, frip_seacr) %>%
  pivot_longer(cols = starts_with("frip_"), names_to = "method", values_to = "FRiP") %>%
  mutate(method = recode(method,
                         "frip_macs" = "MACS2",
                         "frip_sicer" = "SICER2",
                         "frip_seacr" = "SEACR"))

# Peak count in long format
peak_long <- data %>%
  select(sample, cell, n_peaks_macs, n_peaks_sicer, n_peaks_seacr) %>%
  pivot_longer(cols = starts_with("n_peaks_"), names_to = "method", values_to = "Peak_Count") %>%
  mutate(method = recode(method,
                         "n_peaks_macs" = "MACS2",
                         "n_peaks_sicer" = "SICER2",
                         "n_peaks_seacr" = "SEACR"))

# ==== Plot FRiP by cell ====

frip_plot <- ggplot(frip_long, aes(x = cell, y = FRiP, fill = cell)) +
  geom_violin(trim = FALSE, alpha = 0.4) +
  geom_boxplot(width = 0.15, outlier.shape = NA) +
  facet_wrap(~method, scales = "free_y") +
  scale_fill_manual(values = cell_colors) +
  coord_flip() +
  theme_bw() +
  theme(
    axis.text.y = element_text(face = "bold", size = 10),
    strip.text = element_text(face = "bold", size = 12),
    legend.position = "none"
  ) +
  labs(title = "FRiP Distribution by Cell Type", y = "FRiP", x = "Cell Type")

# ==== Plot peak count by cell ====

peak_plot <- ggplot(peak_long, aes(x = cell, y = Peak_Count, fill = cell)) +
  geom_violin(trim = FALSE, alpha = 0.4) +
  geom_boxplot(width = 0.15, outlier.shape = NA) +
  facet_wrap(~method, scales = "free_y") +
  scale_y_log10() +
  scale_fill_manual(values = cell_colors) +
  coord_flip() +
  theme_bw() +
  theme(
    axis.text.y = element_text(face = "bold", size = 10),
    strip.text = element_text(face = "bold", size = 12),
    legend.position = "none"
  ) +
  labs(title = "Peak Count Distribution by Cell Type", y = "Number of Peaks (log10)", x = "Cell Type")

# ==== Save figure ====
ggsave("FRiP_by_celltype.png", frip_plot, width = 10, height = 6, dpi = 300)
ggsave("PeakCount_by_celltype.png", peak_plot, width = 10, height = 6, dpi = 300)
```

![Pasted image 20260301235929](../../assets/Pasted%20image%2020260301235929.png)

> [!ocr-extractor]- Extracted text
> FRIiP Distribution by Cell Type
> _ Mmacs2 SEAR _ SICER2
> WTC11 —_— WTC11 —_— WTC11 _—
> TTCneg466 —m— TTCneg466 —o— TTCneg466 ——
> Tcell —o— Tcell —— Tcell —m—
> TC71 —— TC71 —o— TC71 —_—
> Jurkat — Jurkat —_— Jurkat] ——mm=——
> IMR90 —m— IMR90 -— IMR90 ——
> HSPCs ' HSPCs - HSPCs ——
> Hippocampus — Hippocampus —-— Hippocampus ——
> LQ HCT116 —— HCT116 —— HCT116 ——
> ~ HCF —os— HCF — HCF —_—a—
> 3 expanded + expanded - expanded +
> DLDnegl a DLDnegl —_— DLDnegl _—
> DLD1 —o— DLD1 — DLD1 —a=—
> Caconeg2 | Caconeg2 | Caconeg2 |
> CACO2 —m— CACO2 —m— CACO2 —m—
> BEP2DOE . BEP2DOE . BEP2DOE .
> BEP2DNC —— BEP2DNC —— BEP2DNC — =
> B35T1NC — mm B35T1NC —_—=— B35T1NC —_—
> A673 — A673 — = A673 ——
> 0.0 0.5 1.0 0.0 0.5 1.0 0.0 0.5 1.0
> FRiP

![Pasted image 20260301235941](../../assets/Pasted%20image%2020260301235941.png)

> [!ocr-extractor]- Extracted text
> Peak Count Distribution by Cell Type
> _ macs2 SEAR _ SICER2
> WTC11 + WTC11 —=— WTC11 —o-
> TTCneg466 —_ TTCneg466 -— TTCneg466 +
> Tcell — Tcell —a— Tcell --
> TC71 = TC71 —— TC71 [
> Jurkat] —mmmm=——— Jurkat —o— Jurkat —_—
> IMR90 + IMR90 - IMR90 +
> HSPCs —+ HSPCs - HSPCs -
> Hippocampus —— Hippocampus —o- Hippocampus ——
> LQ HCT116 — HCT116 — HCT116 —
> F HCF a HCF oe HCF —
> 3 expanded * expanded * expanded ’
> DLDnegl —m— DLDnegl -* DLDnegl —-
> DLD1 —- DLD1 - DLD1 +
> Caconeg?2 Caconeg?2 | Caconeg?2
> CACO2 * CACO2 | CACO2 '
> BEP2DOE . BEP2DOE . BEP2DOE .
> BEP2DNC + BEP2DNC BEP2DNC |
> B35T1NC + B35T1NC * B35T1NC *
> A673 —e— A673 —e— A673 —=—
> le-02 1e+01 1e+04 1e+07 le-02 1e+01 1e+04 1e+07 le-02 1e+01 1e+04 1e+07
> Number of Peaks (log10)

