# Week 14

## 1. Download the metadata
```
bio search --header --csv  PRJNA887926 > metadata.csv
```
## 2. Create a design.csv file
```
run_accession,sample_aliasm,group
SRR21835898,NT1,control
SRR21835899,NaP3,treatment
SRR21835896,NT3,control
SRR21835897,NT2,control
SRR21835900,NaP2,treatment
SRR21835901,NaP1,treatment
```

## 3. Makefile
### I did this run on this paper: https://www.ncbi.nlm.nih.gov/bioproject/PRJNA887926/ , and built on the Makefile from week 13

## 4. Make all
```
make all_samples NAME=staphylococcus_aureus THREADS=8
```

## 5. First 20 from counts.csv file:
```
GeneID,Chromosome,Start,End,Strand,Length,SRR21835898,SRR21835899,SRR21835896,SRR21835897,SRR21835900,SRR21835901
SAUSA300_0001,CP000255.1,544,1905,+,1362,635,551,487,564,640,531
SAUSA300_0002,CP000255.1,2183,3316,+,1134,365,363,308,287,372,373
SAUSA300_0003,CP000255.1,3697,3942,+,246,23,21,3,8,30,18
SAUSA300_0004,CP000255.1,3939,5051,+,1113,179,265,87,155,300,247
SAUSA300_0005,CP000255.1,5061,6995,+,1935,263,541,247,254,489,523
SAUSA300_0006,CP000255.1,7032,9695,+,2664,269,427,196,264,440,432
SAUSA300_0007,CP000255.1,9782,10612,-,831,36,2,22,14,28,16
SAUSA300_0008,CP000255.1,10920,12434,+,1515,470,386,361,476,380,415
SAUSA300_0009,CP000255.1,12813,14099,+,1287,314,184,536,448,159,165
SAUSA300_0010,CP000255.1,14749,15444,+,696,2,10,2,2,2,16
SAUSA300_0011,CP000255.1,15441,15770,+,330,8,11,14,6,9,6
SAUSA300_0012,CP000255.1,16133,17101,+,969,217,227,174,183,281,230
SAUSA300_0013,CP000255.1,17392,18330,+,939,151,492,86,143,503,491
SAUSA300_0014,CP000255.1,18345,20312,+,1968,585,2209,636,547,2449,2595
SAUSA300_0015,CP000255.1,20309,20755,+,447,314,1025,375,303,933,1113
SAUSA300_0016,CP000255.1,20787,22187,+,1401,628,1719,551,620,1682,1996
SAUSA300_0017,CP000255.1,22465,23748,+,1284,74,45,54,110,62,48
SAUSA300_0020,CP000255.1,24952,25653,+,702,147,224,124,140,271,264
SAUSA300_0021,CP000255.1,25666,27492,+,1827,758,1174,924,844,1035,1234
```
## 6. Discussion:  
### Consistently expressed genes
SAUSA300_0001: counts 487–640 across all six SRRs → stable expression.
SAUSA300_0012: counts 174–281 → moderate, consistent coverage.

### Low-expression genes
SAUSA300_0010: counts 2–16 → very low expression.
Variable-expression / potential differential expression genes
SAUSA300_0014: counts in controls 585, 636, 547 vs treatments 2209, 2449, 2595 → clear difference between conditions.

<img width="1918" height="1013" alt="image" src="https://github.com/user-attachments/assets/8aff7a94-9bbb-420a-be14-0b0708d9ded1" />

<img width="1916" height="1021" alt="image" src="https://github.com/user-attachments/assets/f7fdb907-556b-47f5-9677-056e82334b37" />

<img width="1918" height="1021" alt="image" src="https://github.com/user-attachments/assets/80d89593-fde4-478a-8f78-bd67848d0368" />


## 7. RNA-seq, PCA, heatmap, functional enrichment analysis
### I had to run this separately as an R script in RStudio through PSU roar collab:
```
#!/usr/bin/env Rscript

# ==========================================
# RNA-seq downstream analysis (class-style)
# PCA, Heatmap, Functional Enrichment
# ==========================================

# -----------------------------
# Load/install required packages
# -----------------------------
packages <- c("ggplot2", "pheatmap", "matrixStats", "gprofiler2", "enrichR")
for (pkg in packages) {
  if (!requireNamespace(pkg, quietly = TRUE)) {
    install.packages(pkg, repos = "https://cloud.r-project.org")
  }
  library(pkg, character.only = TRUE)
}

# -----------------------------
# File paths (interactive use)
# -----------------------------
counts_file <- "counts.csv"     # your counts matrix
design_file <- "design.csv"     # your design file
outdir <- "rna_seq"             # output folder

dir.create(outdir, showWarnings = FALSE)

cat("# RNA-seq analysis\n")
cat("Counts file:", counts_file, "\n")
cat("Design file:", design_file, "\n")
cat("Output directory:", outdir, "\n\n")

# -----------------------------
# Load design
# -----------------------------
design <- read.csv(design_file, stringsAsFactors = TRUE)
design$sample <- as.character(design$run_accession)  # matches counts columns
design$group  <- as.factor(design$Group)             # condition/group
design <- design[design$sample %in% colnames(counts), ]
rownames(design) <- design$sample

# -----------------------------
# Load counts
# -----------------------------
counts_raw <- read.csv(counts_file, sep=",", check.names = FALSE)
samples <- colnames(counts_raw)[7:ncol(counts_raw)]  # columns 7 onward are samples
counts <- as.matrix(counts_raw[, samples])
rownames(counts) <- counts_raw$GeneID
mode(counts) <- "integer"

# Subset design to only samples present in counts
design <- design[design$sample %in% colnames(counts), ]

# After loading counts and converting to integer
counts_log <- log2(counts + 1)   # <--- add this line


# -----------------------------
# Continue with log-transform, PCA, heatmap, enrichment...


# =============================
# Filter low-variance genes
# =============================
gene_vars <- rowVars(counts_log)
keep_genes <- gene_vars > 1e-6  # small number instead of 0
counts_log_filtered <- counts_log[keep_genes, ]

# Filter zero-variance columns (samples)
sample_vars <- apply(counts_log_filtered, 2, var)
counts_log_filtered <- counts_log_filtered[, sample_vars > 0]

if(ncol(counts_log_filtered) < 2){
  stop("Not enough variable samples for PCA. Need at least 2 samples with variance > 0.")
}

if(nrow(counts_log_filtered) == 0){
  stop("No genes with variance > 0. Cannot perform PCA or heatmap.")
}



# =============================
# PCA Plot
# =============================
cat("\n# Generating PCA plot\n")
pca <- prcomp(t(counts_log_filtered), scale. = TRUE)
pca_df <- data.frame(
  PC1 = pca$x[,1],
  PC2 = pca$x[,2],
  group = design$group,
  sample = rownames(pca$x)
)
var_explained <- round(100 * (pca$sdev^2 / sum(pca$sdev^2)))

pca_pdf <- file.path(outdir, "pca.pdf")
pdf(pca_pdf, width = 7, height = 6)
ggplot(pca_df, aes(PC1, PC2, color=group, label=sample)) +
  geom_point(size=4) +
  geom_text(vjust=1.4, size=3) +
  xlab(paste0("PC1 (", var_explained[1], "% variance)")) +
  ylab(paste0("PC2 (", var_explained[2], "% variance)")) +
  theme_minimal() +
  ggtitle("PCA of RNA-seq samples")
dev.off()
cat("PCA plot saved to:", pca_pdf, "\n")

# =============================
# Heatmap (top 50 most variable genes)
# =============================
cat("\n# Generating heatmap\n")
vars <- rowVars(counts_log_filtered)
top_genes_idx <- order(vars, decreasing = TRUE)[1:min(50, length(vars))]
mat_top <- counts_log_filtered[top_genes_idx, ]

# Correct annotation
annotation_col <- data.frame(Group = design$group)
rownames(annotation_col) <- design$sample  # <-- MUST match columns of mat_top

pheatmap(
  mat_top,
  scale = "row",
  annotation_col = annotation_col,
  show_rownames = TRUE,
  show_colnames = TRUE,
  fontsize_row = 6,
  fontsize_col = 10,
  color = colorRampPalette(c("green", "black", "red"))(100),
  main = "Top Variable Genes"
)

# =============================
# Top 100 variable genes for enrichment
# =============================
cat("\n# Selecting top 100 most variable genes for enrichment\n")
top_genes <- rownames(counts)[order(vars, decreasing = TRUE)][1:100]
top_genes_csv <- file.path(outdir, "top_genes.csv")
write.csv(top_genes, top_genes_csv, row.names = FALSE, quote = FALSE)
cat("Top genes saved to:", top_genes_csv, "\n")

# =============================
# Functional enrichment: g:Profiler
# =============================
cat("\n# Running g:Profiler enrichment\n")
gp <- gprofiler2::gost(top_genes, organism = "hsapiens")
gp_csv <- file.path(outdir, "gprofiler.csv")
write.csv(gp$result, gp_csv, row.names = FALSE)
cat("g:Profiler results saved to:", gp_csv, "\n")

# =============================
# Functional enrichment: Enrichr
# =============================
cat("\n# Running Enrichr enrichment\n")
dbs <- c("GO_Biological_Process_2021", "KEGG_2021_Human")
enrich_results <- enrichr(top_genes, dbs)

for(db in dbs){
  enrich_csv <- file.path(outdir, paste0("enrichr_", db, ".csv"))
  write.csv(enrich_results[[db]], enrich_csv, row.names = FALSE)
  cat("Enrichr results for", db, "saved to:", enrich_csv, "\n")
}

cat("\n# RNA-seq analysis complete.\n")

```

## Which produced the following heatmaps, PCA plot, gprofiler, and top genes:
<img width="538" height="677" alt="image" src="https://github.com/user-attachments/assets/2f897506-4875-43f7-b274-538c2272a53e" />

<img width="697" height="600" alt="image" src="https://github.com/user-attachments/assets/282d47a2-2e78-4c46-b9b3-1f0900269f2e" />

## Top genes:
### SAUSA300_2255
SAUSA300_1706
SAUSA300_1702
SAUSA300_1703
SAUSA300_1705
SAUSA300_1701
SAUSA300_1704
SAUSA300_2254
SAUSA300_1584
SAUSA300_1029
SAUSA300_1868
SAUSA300_0250
SAUSA300_1300
SAUSA300_0251
SAUSA300_1034
SAUSA300_1666
SAUSA300_1859
SAUSA300_0667
SAUSA300_0666
SAUSA300_1863
SAUSA300_1668
SAUSA300_2314
SAUSA300_1665
SAUSA300_0735
SAUSA300_1667
SAUSA300_1299
SAUSA300_2312
SAUSA300_0679
SAUSA300_1924
SAUSA300_1923
SAUSA300_0797
SAUSA300_1425
SAUSA300_2530
SAUSA300_1532
SAUSA300_1860
SAUSA300_0760
SAUSA300_1270
SAUSA300_0757
SAUSA300_1199
SAUSA300_0024
SAUSA300_1862
SAUSA300_0883
SAUSA300_2261
SAUSA300_0268
SAUSA300_1298
SAUSA300_1197
SAUSA300_0072
SAUSA300_0665
SAUSA300_2390
SAUSA300_2396
SAUSA300_2049
SAUSA300_1301
SAUSA300_1198
SAUSA300_1344
SAUSA300_2315
SAUSA300_0585
SAUSA300_0755
SAUSA300_0737
SAUSA300_1205
SAUSA300_1515
SAUSA300_2529
SAUSA300_2488
SAUSA300_0372
SAUSA300_0675
SAUSA300_2490
SAUSA300_0930
SAUSA300_0736
SAUSA300_1726
SAUSA300_1514
SAUSA300_2202
SAUSA300_1051
SAUSA300_1891
SAUSA300_1345
SAUSA300_1049
SAUSA300_2099
SAUSA300_1137
SAUSA300_0913
SAUSA300_0269
SAUSA300_2473
SAUSA300_2278
SAUSA300_0094
SAUSA300_0578
SAUSA300_1953
SAUSA300_0337
SAUSA300_0739
SAUSA300_1866
SAUSA300_0820
SAUSA300_0029
SAUSA300_2304
SAUSA300_0113
SAUSA300_1248
SAUSA300_1209
SAUSA300_0328
SAUSA300_2340
SAUSA300_0255
SAUSA300_2426
SAUSA300_0489
SAUSA300_0199
SAUSA300_2407
SAUSA300_1944
