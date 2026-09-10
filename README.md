                                     _ _        ____      _
                            /\      | (_)      / __ \    | |
                           /  \   __| |_ _ __ | |  | | __| |_   _ ___ ___  ___ _   _
                          / /\ \ / _` | | '_ \| |  | |/ _` | | | / __/ __|/ _ \ | | |
                         / ____ \ (_| | | |_) | |__| | (_| | |_| \__ \__ \  __/ |_| |
                        /_/    \_\__,_|_| .__/ \____/ \__,_|\__, |___/___/\___|\__, |
                                        | |                  __/ |              __/ |
                                        |_|                 |___/              |___/
                        
                                                      /\
                                                     /  \
                                                    /____\
                                                       ||
                                                __     ||     __
                                             __/  \___/\/\___/  \__
                           2D               (____________________)               3D
                          Day 0    ~╮  ╭~╮  ╭~╮   ~╮  ╭~╮  ╭~╮   ╭~  ╭~╮   ~╮  Day 12
                          ~~~~~~~~~~╯╰~~╯ ╰~~╯ ╰~~~╯╰~~╯ ╰~~╯ ╰~~╯ ╰~~╯ ╰~~╯ ╰~~~~~~~~
                           ≈≈≈  ~~~  ≈≈≈≈  ~~~~  ≈≈≈  ~~~~~  ≈≈≈  ~~~  ≈≈≈≈  ~~~  ≈≈≈
                                  A long voyage through fat, form, and time

# AdipOdyssey

Bulk RNA-seq DESeq2 pipelines for the PCOS adipogenesis project (2D monolayer, 3D spheroid, and a cross-method comparison).

## Files

### `QuTE_tools_V17.R`
Shared helper-function library sourced by all three pipeline scripts. Covers:
- Count matrix loading and mitochondrial QC
- DESeq2 object setup, gene ID conversion, low-count/ncRNA filtering
- PCA, Euclidean distance, and Spearman correlation QC plots
- DEG extraction (Wald test, with/without LFC shrinkage), volcano plots, and heatmaps
- GO enrichment
- LRT (likelihood ratio test) clustering across timepoints/treatments
- Jaccard overlap, clinical correlation, and gene-module/signature scoring

Trimmed to only the functions actually used by the three pipelines below (KEGG/Reactome/GSEA/STRING enrichment, WGCNA, and a few superseded helpers were removed as unused).

### `2D_Adipogenesis_Quick_Time-Series_DESeq2_V17.R`
2D monolayer adipogenesis time course: PCOS vs Control across Day 0/6/12 of differentiation. Runs QC, a Wald-test DESeq2 model on Group (Condition × Timepoint) with pairwise contrasts (PCOS vs Control at each day, and each condition's own D0→D6→D12 trajectory), GO enrichment on the resulting DEGs, and an LRT to find genes whose expression pattern differs between PCOS and Control over time.

### `Spheroid_Quick_Time-Series_DESeq2_V17.R`
3D spheroid adipose experiment: PCOS vs Control across three metabolic-stress treatments (LI, HIFFA, HIHG), partially paired by patient. Same overall structure as the 2D script (QC → Wald DEGs → GO → LRT), plus a clinical-correlation section linking DEG/module scores to patient clinical variables. WGCNA is omitted here due to low sample size per group.

### `2D_3D_Method_Comparison_DESeq2_V1.R`
Companion script that compares the 2D and 3D culture systems directly, using a merged design table so both sample sets share one DESeq2 model. It re-runs the within-method contrasts for completeness/QC and adds new cross-method (2D vs 3D) contrasts, gene-module scoring across all 12 groups, and a final section comparing externally-generated PCOS-vs-Control DEG tables from the two standalone pipelines above (Venn overlap, hypergeometric tests, log2FC correlation scatters). Run this *after* the two scripts above, not instead of them. Important caveat: the 2D and 3D sample sets come from entirely different donors, so culture Method is fully confounded with donor — treat the Method comparison as descriptive/hypothesis-generating, not a clean causal test (see the script header for details).

## Usage

1. Set input paths and variables in the top "Variable section" of the pipeline script you want to run.
2. Source `QuTE_tools_V17.R` (done automatically at the top of each pipeline script).
3. Run the script top to bottom. Outputs are written to `Output_<run.metrics>/` with subfolders for QC, DEGs, GO enrichment, and LRT results (plus correlation/overlap folders where relevant).

## Requirements

R with DESeq2, tximport, ggplot2, openxlsx, clusterProfiler, DEGreport, and related Bioconductor/CRAN packages (see `library()` calls at the top of `QuTE_tools_V17.R`).
