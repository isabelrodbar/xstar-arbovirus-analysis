# Xstar Arbovirus Serosurvey Data Processing and Analysis

[![GitHub Pages](https://img.shields.io/badge/docs-GitHub%20Pages-blue)](https://isabelrodbar.github.io/xstar-arbovirus-analysis/)

## Overview

This repository contains the complete data processing and analysis pipeline for the Xstar Arbovirus serosurvey conducted in Uganda. The pipeline extracts, processes, and merges Luminex MAGPIX multiplexed bead assay data across multiple waves, implements quality control measures, and generates comprehensive exploratory analyses.

**Live Reports:** [View Interactive Analyses](https://isabelrodbar.github.io/xstar-arbovirus-analysis/)

## Pipeline Structure

### Data Processing Scripts

1. **`01_process_wave3_data.R`** - Process original Wave 3 plates (1-8)
2. **`02_process_wave5_data.R`** - Process original Wave 5 plates (1-13)
3. **`02_process_new_data_w3_repeats.R`** - Process new Wave 3 plates (9-12) and Wave 5 repeat plates
4. **`04_merge_waves.R`** - Merge all waves with sample quality labeling
5. **`05_merge_metadata.R`** - Merge with LLINEUP participant metadata

### Analysis Scripts

- **`Exploratory_Analysis.Rmd`** - Comprehensive exploratory analysis (Median MFI)
- **`Exploratory_Analysis_NetMFI.Rmd`** - Exploratory analysis using Net MFI values
- **`analyze_repeat_samples.R`** - Correlation analysis for QC repeat measurements

### Output Files

```
outputs/
├── wave3_data/              # Wave 3 processed data
├── wave5_data/              # Wave 5 processed data
├── new_w3_repeats/          # New Wave 3 and repeat plates
├── repeat_analysis/         # QC repeat correlation results
└── overall/                 # Merged datasets
    ├── merged_mfi_long_with_metadata.csv
    ├── merged_count_long_with_metadata.csv
    ├── sample_quality_documentation.md
    ├── Exploratory_Analysis.html
    └── Exploratory_Analysis_NetMFI.html
```

## Data Structure

### Waves and Plates

| Wave | Plates | Samples | Collection Period |
|------|--------|---------|-------------------|
| Wave 3 | 1-12 | 782 | 2026-04-21 to 2026-05-07 |
| Wave 5 | 1-13 | 906 | 2026-03-30 to 2026-05-07 |

**Total:** 1,688 unique participant samples across 23 plates

### Sample Quality Labeling

All samples are assigned one of three quality flags in the `SampleQuality` column:

- **`good`** (1,688 samples): Standard quality samples for primary analyses
- **`bad`** (123 samples): Wave 5 samples from original plates with quality issues (flagged for exclusion)
- **`duplicate`** (20 samples): Wave 3 samples re-measured for QC (use for reproducibility assessment only)

See [`outputs/overall/sample_quality_documentation.md`](outputs/overall/sample_quality_documentation.md) for detailed documentation.

### Disease Panels (47 Analytes)

1. **Arboviruses (ARB_)**: CCHFV, CHIKV, DENV1-4, ONNV, RVFV, USUV, WNV, YFV, ZIKV + SNAP domain variants
2. **Malaria (MAL_)**: P. falciparum, P. vivax, P. malariae, P. ovale (MSP1)
3. **Neglected Tropical Diseases (NTD_)**: Schistosomiasis, Leishmaniasis, Chlamydia, Giardia, Strongyloides
4. **Cholera (CHO_)**: CtxB, Inaba/Ogawa OSP
5. **Hepatitis (HEP_)**: HEV ORF2
6. **Vaccine Preventable Diseases (VPD_)**: Measles, Pertussis, Diphtheria, Tetanus
7. **Human Herpesviruses (HHV_)**: EBV, CMV
8. **Controls (CTRL_)**: BSA, SNAP

## Quick Start

### Requirements

```r
# Install required packages
install.packages(c("tidyverse", "readr", "dplyr", "tidyr", "stringr",
                   "readxl", "ggbeeswarm", "patchwork", "rmarkdown"))
```

### Running the Complete Pipeline

```bash
cd Code/

# Process each wave
Rscript 01_process_wave3_data.R
Rscript 02_process_wave5_data.R
Rscript 02_process_new_data_w3_repeats.R

# Merge waves and add quality flags
Rscript 04_merge_waves.R

# Merge with participant metadata
Rscript 05_merge_metadata.R

# Generate exploratory analysis reports
Rscript -e 'rmarkdown::render("Exploratory_Analysis.Rmd")'
Rscript -e 'rmarkdown::render("Exploratory_Analysis_NetMFI.Rmd")'
```

### Filtering Data for Analysis

```r
library(tidyverse)

# Load merged data
mfi_data <- read_csv("outputs/overall/merged_mfi_long_with_metadata.csv")

# Filter to good quality samples only (recommended for primary analyses)
clean_data <- mfi_data %>%
  filter(SampleQuality == "good")

# Exclude standards and blanks
participant_data <- clean_data %>%
  filter(!grepl("^Standard", Sample, ignore.case = TRUE),
         !grepl("^Background|BLANK", Sample, ignore.case = TRUE))
```

## Key Features

### Data Processing
- **Multi-section CSV parsing**: Extracts Median MFI, Net MFI, and Count sections from xPONENT format
- **Plate layout integration**: Merges well positions with participant barcodes
- **Batch tracking**: Includes Date, Batch, and Operator metadata for all measurements
- **Quality control**: Automated flagging of problematic plates and duplicate measurements

### Sample Quality System
- Distinguishes original vs. repeat measurements using Batch identifiers
- Prevents duplicate sample counting in prevalence calculations
- Enables QC and reproducibility assessments

### Analysis Features
- Comprehensive exploratory visualizations by wave, site, district, and region
- Beeswarm plots for sample-level distributions
- SNAP antigen ratio calculations (Antigen/CTRL_SNAP)
- Correlation analysis for repeat measurements
- Low bead count flagging (< 20 beads)

## Recent Updates

**2026-05-08:**
- ✅ Fixed data duplication issue in Wave 5 Plate 03 repeat samples
- ✅ Added Batch column to count data files for proper repeat plate tracking
- ✅ Corrected CTRL_SNAP joins in metadata merge to prevent cartesian products
- ✅ Updated quality flag joins to include Batch as join key
- ✅ Recovered 12 missing samples from Row B in repeat plate layout parsing
- ✅ Regenerated all analysis reports with corrected data

**2026-05-07:**
- Implemented sample quality labeling system
- Generated comprehensive quality documentation
- Added repeat sample correlation analysis

## Data Quality Metrics

### Repeat Sample Correlations (QC)

| Wave | Median Correlation | Range |
|------|-------------------|-------|
| Wave 3 (n=20) | 0.94 | 0.73 - 1.00 |
| Wave 5 (n=99) | 0.91 | 0.23 - 1.00 |

Top performers (r > 0.99): ZIKV DIII, CHIKV VLP, RVFV, PfMSP1, HEV ORF2

See [`outputs/repeat_analysis/repeat_correlations_by_analyte.csv`](outputs/repeat_analysis/repeat_correlations_by_analyte.csv) for complete results.

### Sample Counts by Quality

| Wave | Good | Bad | Duplicate |
|------|------|-----|-----------|
| Wave 3 | 782 | 0 | 20 |
| Wave 5 | 906 | 123 | 0 |

## Interactive Reports

View the complete exploratory analyses online:

- **[Median MFI Analysis](https://isabelrodbar.github.io/xstar-arbovirus-analysis/Exploratory_Analysis.html)** - Comprehensive exploratory plots using Median MFI values
- **[Net MFI Analysis](https://isabelrodbar.github.io/xstar-arbovirus-analysis/Exploratory_Analysis_NetMFI.html)** - Analysis using background-subtracted Net MFI values

Both reports include:
- Sample distributions by wave, plate, site, and district
- Disease panel-specific visualizations
- Correlation matrices for related antigens
- SNAP antigen ratio analyses
- Interactive plots with low bead count flagging

## Repository Structure

```
xstar-arbovirus-analysis/
├── README.md                              # This file
├── index.html                             # GitHub Pages landing page
├── Exploratory_Analysis.html              # Published analysis report (Median MFI)
├── Exploratory_Analysis_NetMFI.html       # Published analysis report (Net MFI)
├── 01_process_wave3_data.R               # Wave 3 processing
├── 02_process_wave5_data.R               # Wave 5 processing
├── 02_process_new_data_w3_repeats.R      # New W3 + repeats processing
├── 04_merge_waves.R                      # Merge all waves
├── 05_merge_metadata.R                   # Merge with participant data
├── Exploratory_Analysis.Rmd              # Analysis source (Median MFI)
├── Exploratory_Analysis_NetMFI.Rmd       # Analysis source (Net MFI)
├── analyze_repeat_samples.R              # QC repeat analysis
└── outputs/                              # Generated data and reports
    ├── wave3_data/
    ├── wave5_data/
    ├── new_w3_repeats/
    ├── repeat_analysis/
    └── overall/
```

## Citation

If you use this pipeline or data, please cite:

> Rodriguez I, et al. (2026). Xstar Arbovirus Serosurvey Data Processing Pipeline.
> https://github.com/isabelrodbar/xstar-arbovirus-analysis

## Contact

For questions or issues:
- Open an [issue](https://github.com/isabelrodbar/xstar-arbovirus-analysis/issues)
- Contact: Isabel Rodriguez

---

**Last Updated:** 2026-05-08
**Pipeline Version:** 2.0
**R Version Required:** ≥ 4.0
