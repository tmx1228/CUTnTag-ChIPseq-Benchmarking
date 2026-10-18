![Pasted image 20260612134720](../assets/Pasted%20image%2020260612134720.png)

> [!ocr-extractor]- Extracted text
> 11] III] cell
> TTT NNR NY 0 NNN technique INN NE technique
> J00 ANE IREETRR EET IT caller JN A WO BORE 1 1 cet
> Jr NOS MINN treatment 10 (TERE EET] treatment
> B IT ET i i
> B 4 l X ud 08 k ak if Vag, 08
> H H and fr i
> 8 FE ~ 2 J a 0.6 i i# E © 06
> » " lll = fg i =
> we i 04 ' it i 04
> | | ume 2
> |“ . 02 2 jis §%2
> jo i BE] 0 | i EEE RE: °
> 8 a i a : RC ot i
> | Bi -0.2 “EE i -0.2
> 3 “ 11%] ol wll id ai
> i ar cl LEE pr oo he -04
> B 5% “5 i =r - rr = ’
> [| i [ie Ba i ER bd i
> 8 % if TR | mel SEES
> B + po % of iH fe
> | 4 hE" Fo SE
> = Pe Tl ee
> | | FL m— =z he ot or A ie [RUT Lat] it iff !
> HET Ewa | aE 1 igs | i
> = . TT ANE LER te he
> | | » i $i 4. - Bh {i .
> Lo Ege NE i” Ti
> U7 [A TR i i iii L
> yo - V A ¢ . 6 Nt = 1 - i
> — z j h - i = - war 2 .
> frome RET, 4B RE | pmo - bil EN
> 0
> eo
> 2 i 8
> treatment treatment
> —SA1_KO — — ot SA2 KO_cell —— —uCTreg_DM_72h —SAL_KD — AZ KO Rescue _of_SA2 in SA2 XO cull —T —iCTrey Dh Tn
> — one_72n - 7m - [R— - ERD — SANG ONG Th —uSAneg DHA Tn — — FEN — EFXD — EFRescue
> — NT —hSATB1 —shSATE2 — SATB — ary Puss — Late Pass TT SSA SSNES SRNR Safe Se fe
> flaps Trmoled wes CDipos T_cols WT wes [CMneg trealed 63 we emply_vechr rescos we DBD_rescus Temes TAT Chm emt em nan Gs me
> w= DB0pos rescue KD DBD oaspe w—reeting we antneg CD3 CD28 _symulated
> ww DBDpos_rescus Li 06D DBDpos — resting ww artineg_CCI_CD28_strmuisted
> technique
> caller —ChiPsey — CUTTag
> macs? — cnr seer
> coll
> tech ASTI TCT — DLDneg! —TCH1 Dw — CTE —arkat
> w—ChiPseq —CUTTag — Caconeg? — Toe — MCF — panded — PSO —CACO2 Ww TTCreg#%6
> cel — BEPIONC BEP2D0E BASTING — Hgpocampus — HEPC
> — ASTI -— TCT —— DLDnegt -— WTC11 — DLDY — HCT116 — Jurkat

#### peak overlap heatmap
```R
setwd("/sfs/weka/path/to/path/to/external_data")


library(readr)
library(dplyr)
library(pheatmap)

mat_top50k <- readRDS("/path/to/path/to/external_data/binary_peak_matrix_top50000.rds")

# Read metadata
meta <- read_tsv(
  "/path/to/path/to/external_data/ctcf_meta_all_peaks.txt",
  show_col_types = FALSE
)

# Align to matrix column order
meta2 <- meta[match(colnames(mat_top50k), meta$id), ]
stopifnot(all(meta2$id == colnames(mat_top50k)))

# Inter-sample correlation
cor_mat <- cor(mat_top50k, method = "pearson")

# annotation
anno <- data.frame(
  treatment = meta2$treatment,
  caller = meta2$caller,
  technique = meta2$technique,
  cell = meta2$cell
)

rownames(anno) <- meta2$id
anno <- anno[colnames(cor_mat), , drop = FALSE]

# Color palette
ann_colors <- list(
  technique = setNames(
    grDevices::hcl.colors(length(unique(anno$technique)), "Dark 2"),
    unique(as.character(anno$technique))
  ),
  treatment = setNames(
    grDevices::hcl.colors(length(unique(anno$treatment)), "Set 2"),
    unique(as.character(anno$treatment))
  ),
  caller = setNames(
    grDevices::hcl.colors(length(unique(anno$caller)), "Set 3"),
    unique(as.character(anno$caller))
  ),
  cell = setNames(
    grDevices::hcl.colors(length(unique(anno$cell)), "Dynamic"),
    unique(as.character(anno$cell))
  )
)

pdf("CTCF_binary_peak_correlation_heatmap.pdf", width = 8, height = 7)
pheatmap(
  cor_mat,
  annotation_col = anno,
  annotation_row = anno,
  annotation_colors = ann_colors,
  
  show_rownames = FALSE,
  show_colnames = FALSE,
  
  clustering_method = "ward.D2",
  
  color = colorRampPalette(c("navy", "white", "firebrick3"))(100),
  border_color = NA
)
dev.off()
```


### plot legend
```R
pdf("CTCF_binary_peak_annotation_legend_grouped_horizontal.pdf", width = 12, height = 5)

par(mar = c(1, 1, 1, 1))
plot.new()
plot.window(xlim = c(0, 1), ylim = c(0, 1))

box_w <- 0.025
box_h <- 0.018
text_gap <- 0.008
item_gap <- 0.04
row_gap <- 0.08
title_gap <- 0.055

draw_grouped_legend <- function(title, color_vec, x_start, y_start,
                                max_x = 0.95,
                                cex_title = 1.1,
                                cex_text = 0.8) {
  
  ## title
  text(x_start, y_start, title, adj = c(0, 0.5), font = 2, cex = cex_title)
  
  x <- x_start
  y <- y_start - title_gap
  
  for (nm in names(color_vec)) {
    
    ## Estimate item width based on label length
    label_w <- strwidth(nm, cex = cex_text)
    item_w <- box_w + text_gap + label_w + item_gap
    
    ## Wrap to a new line if the item does not fit
    if (x + item_w > max_x) {
      x <- x_start
      y <- y - row_gap
    }
    
    rect(
      xleft = x,
      ybottom = y - box_h / 2,
      xright = x + box_w,
      ytop = y + box_h / 2,
      col = color_vec[[nm]],
      border = NA
    )
    
    text(
      x + box_w + text_gap,
      y,
      nm,
      adj = c(0, 0.5),
      cex = cex_text
    )
    
    x <- x + item_w
  }
  
  return(y - row_gap * 1.3)
}

y <- 0.92

y <- draw_grouped_legend(
  "treatment",
  ann_colors$treatment,
  x_start = 0.05,
  y_start = y
)

y <- draw_grouped_legend(
  "caller",
  ann_colors$caller,
  x_start = 0.05,
  y_start = y
)

y <- draw_grouped_legend(
  "technique",
  ann_colors$technique,
  x_start = 0.05,
  y_start = y
)

y <- draw_grouped_legend(
  "cell",
  ann_colors$cell,
  x_start = 0.05,
  y_start = y
)

dev.off()
```
[CTCF_binary_peak_annotation_legend_grouped_horizontal.pdf](../assets/CTCF_binary_peak_annotation_legend_grouped_horizontal.pdf)

> [!ocr-extractor]- Extracted text
> treatment
> SA1_KO mmm SA2_KO mm Rescue_of_SA2_in_SA2_KO_cell mmm WT mmm siCTneg_Dh1_72h
> mmm siSA2neg_Dh6_72h mmm siSA2neg_Dh8_72h mm Aux mmm EFEndo mmm EFKD mmm EFRescue
> mmm shWT mmm shSATB1 mmm shSATB2 mmm shSATB3 mmm Early Pass mmm | ate_Pass
> mmm Rapa_Treated mmm CD4pos_T_cells_ WT mm ICMneg_treated_6d empty_vector_rescue DBD_rescue
> DBDpos_rescue KD DBD DBDpos resting antineg_CD3_CD28_stimulated
> caller
> macs2 sicer seacr
> technique
> mmm ChIPseq mmm CUTTag
> cell
> mmm A673 mmm TC71 mmm DLDneg1 mmm WTC11 mmm DLDA1 mmm HCT116 mmm Jurkat
> mmm Caconeg2 mmm Teell mmm HCF mmm expanded mmm IMR90 CACO2 TTCneg466


### peak intensity heatmap data 
```R
setwd("/path/to/path/to/external_data/")
logCPM_sub <- readRDS(  
  "/path/to/path/to/external_data/logCPM_sub_50k.rds"  
)
meta_file <- "/path/to/path/to/external_data"  
meta <- fread(meta_file)  
  
# Check column names  
stopifnot(all(c("id","paired","GSM","technique","cell","treatment") %in% names(meta)))  
meta[, paired := as.character(paired)]  
meta[, technique := as.character(technique)]  
meta[, cell := as.character(cell)]  
meta[, treatment := as.character(treatment)]

library(dplyr)
library(tibble)

## 0) meta2：Keep only required columns
meta2 <- as.data.frame(meta[, c("GSM","cell","technique","treatment")])

## 1) Align samples
keep <- intersect(colnames(logCPM), meta2$GSM)
logCPM2 <- logCPM[, keep, drop = FALSE]
meta2 <- meta2[match(colnames(logCPM2), meta2$GSM), ]
stopifnot(all(meta2$GSM == colnames(logCPM2)))

### Step 1 Calculate sample correlations
cor_mat <- cor(logCPM_sub, method = "pearson")

### ## Step 2 Prepare annotations
anno <- data.frame(  
  cell = meta2$cell,  
  technique = meta2$technique,  
  treatment = meta2$treatment  
)  
  
rownames(anno) <- meta2$GSM
anno <- anno[colnames(cor_mat), ]
# Reorder annotation columns first
anno <- anno[, c("treatment", "cell", "technique")]
```

### plot heatmap
```R
library(pheatmap)
library(RColorBrewer)


pdf("CTCF_signal_correlation_heatmap.pdf", width = 8, height = 7)  
pheatmap(
  cor_mat,
  annotation_col = anno,
  annotation_row = anno,
  annotation_colors = ann_colors,

  show_rownames = FALSE,
  show_colnames = FALSE,

  clustering_method = "ward.D2",

  color = colorRampPalette(c("navy", "white", "firebrick3"))(100),
  border_color = NA
)
dev.off()


```

### plot legend
```R
pdf("CTCF_signal_correlation_heatmap_annotation_legend_grouped_horizontal.pdf", width = 12, height = 5)

par(mar = c(1, 1, 1, 1))
plot.new()
plot.window(xlim = c(0, 1), ylim = c(0, 1))

box_w <- 0.025
box_h <- 0.018
text_gap <- 0.008
item_gap <- 0.04
row_gap <- 0.08
title_gap <- 0.055

draw_grouped_legend <- function(title, color_vec, x_start, y_start,
                                max_x = 0.95,
                                cex_title = 1.1,
                                cex_text = 0.8) {
  
  ## title
  text(x_start, y_start, title, adj = c(0, 0.5), font = 2, cex = cex_title)
  
  x <- x_start
  y <- y_start - title_gap
  
  for (nm in names(color_vec)) {
    
    ## Estimate item width based on label length
    label_w <- strwidth(nm, cex = cex_text)
    item_w <- box_w + text_gap + label_w + item_gap
    
    ## Wrap to a new line if the item does not fit
    if (x + item_w > max_x) {
      x <- x_start
      y <- y - row_gap
    }
    
    rect(
      xleft = x,
      ybottom = y - box_h / 2,
      xright = x + box_w,
      ytop = y + box_h / 2,
      col = color_vec[[nm]],
      border = NA
    )
    
    text(
      x + box_w + text_gap,
      y,
      nm,
      adj = c(0, 0.5),
      cex = cex_text
    )
    
    x <- x + item_w
  }
  
  return(y - row_gap * 1.3)
}

y <- 0.92

y <- draw_grouped_legend(
  "treatment",
  ann_colors$treatment,
  x_start = 0.05,
  y_start = y
)

y <- draw_grouped_legend(
  "technique",
  ann_colors$technique,
  x_start = 0.05,
  y_start = y
)

y <- draw_grouped_legend(
  "cell",
  ann_colors$cell,
  x_start = 0.05,
  y_start = y
)

dev.off()

```
