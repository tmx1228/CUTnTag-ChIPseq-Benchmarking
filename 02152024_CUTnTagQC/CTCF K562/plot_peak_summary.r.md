```R
library(readr)
library(dplyr)
library(ggplot2)
library(forcats)
library(scales)

df <- read_tsv("peak_set_summary.tsv")

# More readable display names
df <- df %>%
  mutate(
    peak_set_label = recode(
      peak_set,
      "ChIP_CUT_shared_no_motif" = "ChIP-seq/CUT&Tag shared\nno motif",
      "ChIP_CUT_shared_with_motif" = "ChIP-seq/CUT&Tag shared\nwith motif",
      "ChIP_only_no_motif" = "ChIP-seq only\nno motif",
      "ChIP_only_with_motif" = "ChIP-seq only\nwith motif",
      "CUT_only_no_motif" = "CUT&Tag only\nno motif",
      "CUT_only_with_motif" = "CUT&Tag only\nwith motif",
      "ChIP_bias_no_motif" = "ChIP-seq bias\nno motif",
      "ChIP_bias_with_motif" = "ChIP-seq bias\nwith motif",
      "CUT_bias_no_motif" = "CUT&Tag bias\nno motif",
      "CUT_bias_with_motif" = "CUT&Tag bias\nwith motif"
    )
  )

# Preserve original order
df$peak_set_label <- factor(df$peak_set_label, levels = rev(df$peak_set_label))

# 1. peak number
p1 <- ggplot(df, aes(x = peak_set_label, y = n_peaks)) +
  geom_col(fill = "#2C7FB8", color = "black", width = 0.75) +
  coord_flip() +
  scale_y_continuous(labels = label_comma()) +
  labs(x = NULL, y = "Number of peaks", title = "Peak number") +
  theme_classic(base_size = 12) +
  theme(
    plot.title = element_text(face = "bold", hjust = 0.5),
    axis.text.y = element_text(color = "black"),
    axis.text.x = element_text(color = "black")
  )

ggsave("peak_number_barplot.pdf", p1, width = 7, height = 5)

# 2. median peak length
p2 <- ggplot(df, aes(x = peak_set_label, y = median_len)) +
  geom_col(fill = "#2C7FB8", color = "black", width = 0.75) +
  coord_flip() +
  labs(x = NULL, y = "Median peak length (bp)", title = "Median peak length") +
  theme_classic(base_size = 12) +
  theme(
    plot.title = element_text(face = "bold", hjust = 0.5),
    axis.text.y = element_text(color = "black"),
    axis.text.x = element_text(color = "black")
  )

ggsave("peak_median_length_barplot.pdf", p2, width = 7, height = 5)

# 3. total bp
p3 <- ggplot(df, aes(x = peak_set_label, y = total_bp)) +
  geom_col(fill = "#2C7FB8", color = "black", width = 0.75) +
  coord_flip() +
  scale_y_continuous(labels = label_comma()) +
  labs(x = NULL, y = "Total covered bp", title = "Total genomic coverage") +
  theme_classic(base_size = 12) +
  theme(
    plot.title = element_text(face = "bold", hjust = 0.5),
    axis.text.y = element_text(color = "black"),
    axis.text.x = element_text(color = "black")
  )

ggsave("peak_total_bp_barplot.pdf", p3, width = 7, height = 5)
```