# Exploratory Data Analysis Report Guide

## Overview

The Exploratory Analysis Report (`Exploratory_Analysis.html`) provides a comprehensive view of **sample MFI distributions** across all plates and analytes. This focuses on actual test samples, excluding standards, blanks, and controls.

**Key Features:**
- Sample MFI beeswarm plots for all 47 analytes
- Organized by disease panel tabs
- Sorted by pathogen/serotype within each tab
- Summary statistics and variability metrics
- Preliminary positivity screening

## Report Output

**File:** `outputs/Exploratory_Analysis.html` (15 MB)
**Rendering time:** ~7 seconds
**Samples included:** All test samples (excludes standards, blanks, PBT)

## Generating the Report

```bash
# From the Code directory
cd "/path/to/Xstar_Arbovirus/Code"

# Render the report
Rscript render_exploratory.R

# Open in browser
open "outputs/Exploratory_Analysis.html"
```

Or in R/RStudio:

```r
library(rmarkdown)
render("Exploratory_Analysis.Rmd", output_file = "outputs/Exploratory_Analysis.html")
```

## Report Structure

### 1. Sample MFI Distributions by Disease Panel (Tabbed)

Each tab contains beeswarm plots for one disease panel:

#### Tab 1: DENV (12 plots)
**Organization:**
- DENV1: NS1 → VLP → DIII
- DENV2: NS1 → VLP → DIII
- DENV3: NS1 → VLP → DIII
- DENV4: NS1 → VLP → DIII

**Use:** Compare serotype-specific responses, identify differential exposure

#### Tab 2: Other Arboviruses (15 plots)
**Organization (alphabetical by virus):**
- CCHFV: NP
- CHIKV: E2 → VLP
- MAYV: VLP
- ONNV: E2
- RVFV
- USUV: NS1 → DIII
- WNV: NS1 → DIII
- YFV: NS1 → DIII
- ZIKV: NS1 → VLP → DIII

**Use:** Identify which arboviruses show evidence of exposure

#### Tab 3: Malaria (4 plots)
**Organization:**
- Pf MSP1
- Pv MSP1
- Pm MSP1
- Po MSP1

**Use:** Compare species-specific antibody responses

#### Tab 4: NTD (5 plots)
**Organization:**
- Leishmania K39
- pgp3
- cp23
- VSP5
- NIE

**Use:** Assess NTD exposure patterns

#### Tab 5: Hepatitis & Cholera (4 plots)
**Organization:**
- HEV ORF2
- Cholera CtxB
- Cholera Inaba OSP
- Cholera Ogawa OSP

**Use:** Enteric pathogen exposure assessment

#### Tab 6: VPD (4 plots)
**Organization:**
- Pertussis FHA
- Diphtheria Toxin
- Measles NP
- Tetanus Toxin

**Use:** Vaccine-preventable disease exposure/vaccination status

#### Tab 7: HHV (2 plots)
**Organization:**
- EBV gp125
- CMV

**Use:** Herpesvirus seroprevalence

#### Tab 8: Controls (2 plots)
**Organization:**
- BSA
- SNAP

**Use:** Negative controls - should show low background

### 2. Summary Statistics

Three comprehensive tables:

#### Table 1: MFI Distribution Summary by Analyte
**Columns:**
- N Samples
- Min, Q25, Median, Q75, Max MFI
- Mean, SD
- N Flagged, % Flagged

**Sorted by:** Median MFI (descending)

**Use:** Identify analytes with high vs low overall responses

#### Table 2: Plate-to-Plate Variability
**Columns:**
- Mean Median MFI across plates
- SD and Coefficient of Variation (CV%)

**Sorted by:** CV% (descending)

**Interpretation:**
- **High CV (>50%)**: Substantial variation, consider plate effects
- **Moderate CV (25-50%)**: Some variation
- **Low CV (<25%)**: Consistent across plates

#### Table 3: High Responders by Analyte
**Shows:** Samples with MFI > Mean + 3×SD (potential positives)

**Columns:**
- N High Responders
- % High Responders
- Cutoff value used

**Note:** Preliminary screening only. Proper positivity requires validated cutoffs.

## Plot Features

### Beeswarm Plot Elements

Each plot shows:

```
MFI (log)
    |
10^4|        ●                      ●
    |      ●●●●        ●          ●●●
    |    ●●●●●●●     ●●●        ●●●●●
10^3|   ●●●●●●●●●   ●●●●●     ●●●●●●●
    |  ●●●●●●●●●●  ●●●●●●●   ●●●●●●●●
    |  ┌─────┐     ┌─────┐   ┌─────┐
10^2|  │     │     │     │   │     │
    |  └─────┘     └─────┘   └─────┘
    |________________________________
       Plate 5    Plate 6   Plate 7 ...

Legend:
● Blue = Count >= 30 (good)
● Red = Count < 30 (flagged)
□ Box = Median and IQR
```

### Plot Subtitle Information

Each plot includes:
- **N samples**: Number of unique samples tested
- **Median MFI**: Overall median across all plates
- **% flagged**: Percentage with low bead counts

**Example:**
```
N = 78 samples | Median MFI = 156 | 2.1% flagged
```

## Interpreting the Plots

### Distribution Patterns

**1. Low, Tight Distribution (Negative)**
```
MFI
100|  ●
   | ●●●
 50|●●●●●
   |●●●●●
  0|_____
```
- Most samples at low MFI
- Tight clustering
- **Interpretation:** Mostly negative, low exposure

**2. Bimodal Distribution (Mixed)**
```
MFI
1000|      ●●●
    |    ●●●●●●
 100| ●● ●●●●●●●
    |●●●●
   0|__________
```
- Two distinct groups
- **Interpretation:** Mix of negative and positive samples

**3. High, Variable Distribution (Positive)**
```
MFI
5000|  ●  ●
    | ●●●●●●
1000|●●●●●●●●
    |●●●●●●●●
   0|________
```
- Most samples at high MFI
- Wide range
- **Interpretation:** High exposure/seroprevalence

**4. Plate Effect Visible**
```
MFI      Plate 5  Plate 6
1000|      ●●●      ●
    |     ●●●●●    ●●●
 100|    ●●●●●●   ●●●●●
    |________________________________
```
- Systematic difference between plates
- **Interpretation:** Plate effect, may need normalization

### Red Points (Low Counts)

**Few red points (<5%):**
- Good data quality
- Trust the results

**Many red points (>10%):**
- Caution in interpretation
- May have unreliable measurements
- Check if red points are outliers or part of main distribution

### Comparison Strategies

#### Within DENV Panel:
Compare serotypes to identify:
- **Uniform high response** → Recent DENV exposure (any serotype)
- **One serotype elevated** → Serotype-specific exposure
- **NS1 high, VLP/DIII low** → Antigen-type differences

#### Within Other Arboviruses:
Look for:
- **Flaviviruses (DENV, ZIKV, YFV, WNV)** → Cross-reactivity patterns
- **Alphaviruses (CHIKV, MAYV, ONNV)** → Independent responses
- **Single virus elevated** → Specific exposure

#### Across Malaria Species:
- **Pf dominant** → P. falciparum is most common
- **Multiple species** → Co-infections or sequential exposures
- **Similar patterns** → Cross-reactive antibodies

## Common Analysis Questions

### Q: How do I identify positive samples?

**A: Multiple approaches:**

1. **Visual inspection**
   - Samples in upper portion of distribution
   - Clear separation from background cluster

2. **Statistical cutoff (Mean + 3×SD)**
   - See "High Responders by Analyte" table
   - Preliminary screening only

3. **Standard curve-based**
   - Compare to positive controls
   - Use manufacturer's cutoffs (if available)

4. **ROC analysis** (advanced)
   - Requires gold standard reference
   - Determines optimal sensitivity/specificity

### Q: What if I see plate effects?

**A: Options:**

1. **Normalize by plate median**
   ```r
   data %>%
     group_by(PlateNumber) %>%
     mutate(MFI_normalized = MFI_Median / median(MFI_Median))
   ```

2. **Include plate as covariate** in statistical models

3. **Use plate-specific cutoffs**

### Q: How do I compare analyte performance?

**A: Check:**
- **Median MFI** (higher = more sensitive)
- **CV%** (lower = more reproducible)
- **% Flagged** (lower = better data quality)
- **Dynamic range** from QC report

### Q: What about cross-reactivity?

**A: Look for:**
- Similar patterns across related viruses (e.g., all flaviviruses high)
- Correlated responses (use correlation analysis)
- Check if SNAP antigens (DIII domains) are more specific

## Next Steps After Exploratory Analysis

### 1. Define Positivity Cutoffs
- Use mixture models (mclust package)
- ROC analysis with known positives/negatives
- Literature-based cutoffs

### 2. Calculate Seroprevalence
- % positive by analyte
- By plate, age group, geographic location

### 3. Multiplex Analysis
- Co-positivity patterns
- Principal component analysis
- Cluster analysis

### 4. Statistical Modeling
- Logistic regression for predictors of seropositivity
- Mixed effects models for plate effects
- Survival analysis for age-seroprevalence

## Customization

### Change Disease Panel Groupings

Edit `analyte_groups` in `Exploratory_Analysis.Rmd`:

```r
analyte_groups <- list(
  My_Custom_Panel = c("ARB_ZIKV_NS1", "ARB_DENV1_NS1", ...),
  # ... other panels
)
```

### Modify Plot Appearance

In `plot_sample_mfi_beeswarm()` function:

```r
# Change colors
scale_color_manual(values = c('FALSE' = 'darkgreen', 'TRUE' = 'orange'))

# Change beeswarm density
geom_beeswarm(..., cex = 3.0)  # default is 2.5

# Change point size
geom_beeswarm(..., size = 2.0)  # default is 1.5

# Linear scale instead of log
scale_y_continuous()  # instead of scale_y_log10()
```

### Add Additional Summary Tables

Add new code chunks in the Summary Statistics section:

```r
# Example: Correlation matrix
cor_matrix <- sample_mfi %>%
  select(Analyte, TrueSampleID, MFI_Median) %>%
  pivot_wider(names_from = Analyte, values_from = MFI_Median) %>%
  select(-TrueSampleID) %>%
  cor(use = "pairwise.complete.obs")
```

## Files Generated

After rendering:

```
Code/
└── outputs/
    ├── Exploratory_Analysis.html     ← Main exploratory report
    ├── QC_Report.html                ← QC report
    └── processed_data/
        └── ...
```

## Comparing to QC Report

| Feature | QC Report | Exploratory Report |
|---------|-----------|-------------------|
| **Focus** | Standards & QC metrics | Sample distributions |
| **Samples** | Standards, blanks, samples | Samples only |
| **Y-axis** | Standard curve MFI | Sample MFI |
| **Purpose** | Assay performance | Antibody responses |
| **Plots** | Standard curves + counts | Sample beeswarms |
| **Statistics** | R², counts, flags | Distributions, CV% |

**Use both together:**
- QC Report → Assess data quality first
- Exploratory Report → Analyze sample responses

## Troubleshooting

### Issue: All plots look similar

**Cause:** May be on linear scale or narrow MFI range

**Solution:**
- Check if log scale is applied
- Verify sample data was loaded correctly

### Issue: Too many red points

**Cause:** Many low bead counts

**Solution:**
- Review QC Report for systemic issues
- Consider filtering flagged measurements
- May need to re-run plates

### Issue: Large plate effects

**Cause:** Technical variation between plates

**Solution:**
- Normalize by plate median
- Use plate-specific cutoffs
- Consider in statistical models

## References

- **Main Pipeline:** See `README.md`
- **QC Metrics:** See `QC_README.md` and `QC_METRICS_EXPLAINED.md`
- **Latest QC Updates:** See `QC_LATEST_UPDATES.md`

---

**Summary:** The Exploratory Analysis Report provides comprehensive visualization of sample MFI distributions across all analytes, organized by disease panels and sorted by pathogen/serotype for easy interpretation and comparison.
