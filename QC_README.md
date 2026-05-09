# Quality Control Report Guide

## Overview

The QC Report (`QC_Report.Rmd`) generates a comprehensive HTML report with quality control checks for all Luminex MAGPIX data, including:

**For each plate (5-10):**
- Standard curves for all 47 analytes
- Bead count distributions for all 47 analytes
- QC summary tables

**Summary across all plates:**
- Overlaid standard curves showing all plates for each analyte
- Beeswarm plots showing count distributions by plate for each analyte
- Count summary statistics
- Standard curve quality metrics (R²)
- Executive summary with recommendations

## Prerequisites

Install required R packages:

```r
# Run this once
source("install_qc_packages.R")
```

This will install:
- `ggbeeswarm` - for beeswarm plots
- `patchwork` - for arranging multiple plots
- (plus tidyverse packages from the main pipeline)

## Generating the QC Report

### Option 1: Using the render script (Recommended)

```bash
cd "/path/to/Xstar_Arbovirus/Code"
Rscript render_qc_report.R
```

### Option 2: In RStudio

```r
# Open QC_Report.Rmd in RStudio
# Click "Knit" button or press Cmd/Ctrl + Shift + K
```

### Option 3: From R console

```r
library(rmarkdown)
render("QC_Report.Rmd", output_file = "outputs/QC_Report.html")
```

## Output

The report will be generated as: **`outputs/QC_Report.html`**

Open this file in your web browser to view the interactive report with:
- Table of contents navigation
- Collapsible code sections
- High-resolution plots
- Summary tables

## Report Sections

### 1. Quality Control Flags

- Identifies all measurements with bead counts < 30
- Creates flagged versions of datasets
- Summary tables by plate and analyte

**Output files created:**
- `count_long_all_plates_flagged.csv` - Long format with `LowCount_Flag` column
- `count_wide_all_plates_flagged.csv` - Wide format with `Has_LowCount` column

### 2. Individual Plate QC (Plates 5-10)

Each plate section contains:

**a) Standard Curves**
- 47 plots (one per analyte)
- Log-log scale (Dilution vs MFI)
- Points colored by count flag (blue = OK, red = low count)
- Connected by lines to show curve shape

**b) Count Distributions**
- 47 histograms (one per analyte)
- Red dashed line at count = 30 threshold
- Subtitle shows number and % flagged

**c) QC Summary Table**
- Top 20 analytes by low count percentage
- Min, median, max counts
- Number and % of flagged measurements

### 3. Summary Across All Plates

**a) Standard Curves by Analyte**
- 47 plots, each showing all 6 plates overlaid
- Different color for each plate
- Shows plate-to-plate consistency
- Identifies problematic plates or analytes

**b) Count Distributions by Analyte**
- 47 beeswarm plots
- Each plot shows all 6 plates side-by-side
- Points colored by flag status
- Shows count distribution and consistency

**c) Count Summary Statistics**
- Table with min, Q25, median, Q75, max, mean, SD
- Number and % flagged for each analyte
- Sorted by % flagged (worst first)

**d) Standard Curve Quality Metrics**
- R² values for each plate-analyte combination
- Identifies poor curves (R² < 0.95)
- Dynamic range calculations
- Summary by plate

### 4. Executive Summary

- Overall statistics
- Plate-level summary table
- Automated recommendations based on QC thresholds
- Flags for investigation

## QC Criteria

### Bead Counts
- **Threshold:** 30 beads per well-analyte
- **Good:** < 5% flagged overall
- **Warning:** 5-10% flagged
- **Poor:** > 10% flagged

### Standard Curves
- **Good:** R² ≥ 0.95
- **Acceptable:** R² ≥ 0.90
- **Poor:** R² < 0.90

## Rendering Time

- **Expected:** 5-10 minutes
- **Depends on:**
  - Number of plots (47 analytes × 6 plates × 2 types = ~600 plots)
  - Computer speed
  - Image resolution

## Troubleshooting

### Error: Package not found

```r
# Install missing packages
source("install_qc_packages.R")
```

### Error: Cannot find data files

Make sure you're in the `/Code` directory and have run the main pipeline:

```r
source("01_extract_and_merge_data.R")
```

### Memory issues

If R runs out of memory:
1. Close other applications
2. Restart R session
3. Try rendering again

### Report is too large

The HTML file can be 10-50 MB due to embedded plots. This is normal. To reduce size:
- Decrease `fig.width` and `fig.height` in YAML header
- Reduce number of plots per page (`ncol` in `wrap_plots()`)

## Interpreting Results

### Standard Curves

**Good curve:**
- Smooth, monotonic decrease with dilution
- High R² (> 0.95)
- All points blue (counts ≥ 30)
- Wide dynamic range (> 100-fold)

**Problematic curve:**
- Non-monotonic (points go up and down)
- Low R² (< 0.95)
- Many red points (low counts)
- Narrow dynamic range

### Count Distributions

**Good distribution:**
- Most counts > 50
- Narrow distribution (low CV)
- Consistent across plates
- Few or no red points

**Problematic distribution:**
- Many counts < 30
- Wide distribution (high CV)
- Plate-to-plate variation
- Many flagged points

## Next Steps After QC

1. **Review flagged samples**
   - Decide whether to exclude or accept with caution
   - Check if flags are systematic (specific analytes/plates)

2. **Investigate poor standard curves**
   - May need to exclude analyte from analysis
   - Check for pipetting errors
   - Consider re-running plate

3. **Calculate Net MFI**
   - Subtract blank background
   - Use only wells passing QC

4. **Set positivity cutoffs**
   - Based on standards and controls
   - Account for flagged data

5. **Screen for positive samples**
   - Apply cutoffs
   - Generate results table

## Files Generated

After running QC Report:

```
Code/
└── outputs/
    ├── processed_data/
    │   ├── count_long_all_plates_flagged.csv   ← NEW: With flags
    │   ├── count_wide_all_plates_flagged.csv   ← NEW: With flags
    │   └── ...
    ├── QC_Report.html                          ← NEW: Main report
    └── test_standard_curve.png                  ← Test plot
```

## Customization

To modify QC criteria, edit `QC_Report.Rmd`:

```r
# Change count threshold (line ~50)
count_threshold <- 30  # Change to 50 for stricter QC

# Change R² threshold (line ~XXX)
poor_curves <- standard_curve_quality %>%
  filter(R_squared < 0.95)  # Change to 0.90 for less strict
```

Then re-render the report.

---

**Questions or issues?** Check:
- `README.md` - Main pipeline documentation
- `IMPLEMENTATION_SUMMARY.md` - Technical details
- `example_usage.R` - Data usage examples
