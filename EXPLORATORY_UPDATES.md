# Exploratory Analysis Updates - Pairs Plots

**Date:** 2026-04-08
**Version:** 3.0
**File:** `Exploratory_Analysis.html` (22 MB)
**Rendering time:** ~12 seconds

## Changes Implemented

### Version 3.0 Updates (2026-04-08)

#### ✅ Enhanced Pairs Plots with Log Scale and Comprehensive Antigen Coverage

**What changed:**

**1. Log10 Transformation Added**
- All pairs plots now use log10-transformed MFI values
- Transformation: `log10(MFI + 1)` applied before plotting
- Axes show log-scale values for better visualization of wide dynamic range
- More appropriate for MFI data which is typically log-normally distributed

**2. DENV Pairs Plot - Expanded to ALL 12 Antigens**
- **Previous**: Only 4 NS1 antigens (DENV1-4 NS1)
- **Current**: All 12 DENV antigens:
  - DENV1: NS1, VLP, DIII
  - DENV2: NS1, VLP, DIII
  - DENV3: NS1, VLP, DIII
  - DENV4: NS1, VLP, DIII
- **Purpose**: Compare all antigen types (NS1, VLP, DIII) across serotypes
- **Plot size**: 12×12 grid (increased from 4×4)
- **Figure dimensions**: 14×14 inches

**3. Flavivirus Pairs - Split into Two Separate Plots**
- **Previous**: Single plot with 5 NS1 antigens (DENV1 only)
- **Current**: Two separate plots with ALL 4 DENV serotypes:

  **Plot A: Flavivirus NS1 Antigens (8 total)**
  - DENV1 NS1, DENV2 NS1, DENV3 NS1, DENV4 NS1
  - ZIKV NS1
  - YFV NS1
  - WNV NS1
  - USUV NS1
  - 8×8 grid, 14×14 inches

  **Plot B: Flavivirus DIII Antigens (8 total)**
  - DENV1 DIII, DENV2 DIII, DENV3 DIII, DENV4 DIII
  - ZIKV DIII
  - YFV DIII
  - WNV DIII
  - USUV DIII
  - 8×8 grid, 14×14 inches

- **Purpose**: Compare NS1 vs DIII antibody responses separately
- Includes all 4 DENV serotypes (not just representative DENV1)
- DIII contains receptor binding domains and may show more virus-specific responses
- NS1 is secreted non-structural protein with potential for broader cross-reactivity

**4. Other Arbovirus Pairs - Expanded to ALL 14 Antigens**
- **Previous**: Only 4 antigens (CHIKV E2, ONNV E2, CCHFV NP, RVFV)
- **Current**: All 14 non-DENV arbovirus antigens:
  - CCHFV: NP
  - CHIKV: E2, VLP
  - ONNV: E2
  - RVFV: (single antigen)
  - USUV: NS1, DIII
  - WNV: NS1, DIII
  - YFV: NS1, DIII
  - ZIKV: NS1, VLP, DIII
- **Plot size**: 14×14 grid (increased from 4×4)
- **Figure dimensions**: 16×16 inches
- **Purpose**: Comprehensive view of all non-DENV arboviruses including flaviviruses, alphaviruses, and other families

**Technical Implementation:**
```r
# Log transformation applied to data
denv_data <- sample_wide %>%
  select(all_of(denv_analytes)) %>%
  mutate(across(everything(), ~ log10(.x + 1)))

# Updated ggpairs parameters for larger plots
ggpairs(
  denv_data,
  upper = list(continuous = wrap("cor", size = 3.5, color = "blue")),
  lower = list(continuous = wrap("points", alpha = 0.4, size = 0.6)),
  diag = list(continuous = wrap("densityDiag", alpha = 0.5, fill = "steelblue")),
  title = "DENV Serotype Correlations - All Antigens (Log10 Scale)"
) + theme_bw(base_size = 9)
```

**Why these changes:**
- User requested log scale for better visualization of MFI distributions
- User wanted comprehensive antigen coverage for DENV and Other Arboviruses (not just single representatives)
- User wanted flavivirus NS1 and DIII analyzed separately to understand different antibody populations

**Impact:**
- Report size increased from 16 MB to 21 MB (due to larger pairs plots)
- More comprehensive correlation analysis
- Better statistical approach using log-transformed data
- Clearer separation of NS1 vs DIII flavivirus responses

---

### Version 2.0 Updates (2026-04-08)

### 1. ✅ Removed Summary Statistics Section

**What was removed:**
- MFI Distribution Summary table (min, Q25, median, etc.)
- Plate-to-Plate Variability table (CV%)
- High Responders table (Mean + 3×SD)

**Why removed:**
- User found these tables not useful at this stage
- Can be regenerated later if needed
- Makes report more focused on visualizations

### 2. ✅ Added Correlation Analysis - Pairs Plots

**New section:** "Correlation Analysis - Pairs Plots"

Three pairs plots added to visualize correlations between related viruses:

#### A. DENV Serotype Correlations

**Antigens included (12 total):**
- DENV1: NS1, VLP, DIII
- DENV2: NS1, VLP, DIII
- DENV3: NS1, VLP, DIII
- DENV4: NS1, VLP, DIII

**Purpose:**
- Identify cross-reactive antibodies between DENV serotypes
- Assess co-infection patterns
- Distinguish serotype-specific vs pan-DENV responses
- Compare antibody responses across antigen types (NS1, VLP, DIII)

**Plot features:**
- 12×12 grid (log scale)
- Upper diagonal: Correlation coefficients (Pearson r on log-transformed data)
- Lower diagonal: Scatterplots (log10 MFI)
- Diagonal: Density distributions (log10 MFI)

#### B. Flavivirus Correlations - NS1 Antigens

**Antigens included (8 total):**
- DENV1 NS1, DENV2 NS1, DENV3 NS1, DENV4 NS1
- ZIKV NS1
- YFV NS1
- WNV NS1
- USUV NS1

**Purpose:**
- Assess flavivirus NS1 cross-reactivity (well-known phenomenon)
- Identify pan-flavivirus vs virus-specific NS1 antibodies
- Compare all 4 DENV serotypes with other flaviviruses
- NS1 is a secreted non-structural protein with conserved epitopes

**Plot features:**
- 8×8 grid (log scale)
- Shows NS1 relationships among all major flaviviruses
- Includes all DENV serotypes (not just representative)

#### C. Flavivirus Correlations - DIII Antigens

**Antigens included (8 total):**
- DENV1 DIII, DENV2 DIII, DENV3 DIII, DENV4 DIII
- ZIKV DIII
- YFV DIII
- WNV DIII
- USUV DIII

**Purpose:**
- Assess flavivirus DIII cross-reactivity patterns
- DIII contains receptor binding domains and neutralizing epitopes
- May show more virus-specific responses compared to NS1
- Compare all 4 DENV serotypes with other flaviviruses
- Compare NS1 vs DIII antibody populations

**Plot features:**
- 8×8 grid (log scale)
- Shows DIII relationships among all major flaviviruses
- Includes all DENV serotypes (not just representative)

#### D. Other Arbovirus Correlations

**Antigens included (14 total):**
- CCHFV: NP (Bunyavirus)
- CHIKV: E2, VLP (Alphavirus)
- ONNV: E2 (Alphavirus)
- RVFV (Phlebovirus)
- USUV: NS1, DIII (Flavivirus)
- WNV: NS1, DIII (Flavivirus)
- YFV: NS1, DIII (Flavivirus)
- ZIKV: NS1, VLP, DIII (Flavivirus)

**Purpose:**
- Comprehensive view of all non-DENV arboviruses
- Assess alphavirus cross-reactivity (CHIKV vs ONNV)
- Assess flavivirus cross-reactivity (USUV, WNV, YFV, ZIKV)
- Compare antigen types within viruses (NS1, VLP, DIII)
- Identify independent exposure patterns across virus families

**Plot features:**
- 14×14 grid (log scale)
- Multiple virus families represented
- Includes flaviviruses, alphaviruses, bunyavirus, phlebovirus
- Higher correlations expected among flaviviruses, lower between families

## Pairs Plot Interpretation Guide

### Elements of Each Pairs Plot

```
        DENV1    DENV2    DENV3    DENV4
DENV1   [Density] r=0.85   r=0.72   r=0.68

DENV2   [Scatter] [Density] r=0.79  r=0.71

DENV3   [Scatter] [Scatter] [Density] r=0.82

DENV4   [Scatter] [Scatter] [Scatter] [Density]

Legend:
- Upper right: Correlation coefficients (Pearson r on log-transformed data)
- Lower left: Scatterplots (log10 MFI vs log10 MFI)
- Diagonal: Density distributions of log10 MFI
```

**Important:** All pairs plots use **log10-transformed MFI values**: `log10(MFI + 1)`
- Axes show log-scale values
- Correlations calculated on log-transformed data
- Better approach for MFI data which is typically log-normally distributed

### Correlation Coefficient Interpretation

| Correlation (r) | Interpretation | Biological Meaning |
|----------------|----------------|-------------------|
| **> 0.7** | Strong positive | Likely cross-reactive antibodies or co-infections |
| **0.4 - 0.7** | Moderate | Some shared exposure or cross-reactivity |
| **0.1 - 0.4** | Weak | Minimal cross-reactivity, independent exposures |
| **< 0.1** | Very weak | Independent antibody responses |

### What to Look For

**1. High correlations within virus families:**
- **DENV serotypes**: High correlations (>0.7) expected due to:
  - Cross-reactive antibodies
  - Sequential infections
  - Co-circulation in endemic areas

- **Flaviviruses**: Moderate to high correlations expected
  - Known cross-reactivity (DENV, ZIKV, YFV, WNV)
  - Shared epitopes
  - Vaccine-induced cross-protection

- **Alphaviruses**: CHIKV and ONNV moderate correlation
  - Related viruses
  - Some epitope sharing

**2. Low correlations between virus families:**
- CCHFV (bunyavirus) vs alphaviruses → expect low
- RVFV (phlebovirus) vs others → expect low
- Independent transmission cycles

**3. Outliers in scatterplots:**
- Points far from regression line
- May indicate:
  - Unusual exposure patterns
  - Sample quality issues
  - Interesting biological phenomena

**4. Bimodal distributions (diagonal):**
- Two peaks in density plot
- Suggests:
  - Positive vs negative populations
  - Different exposure groups

## Use Cases

### Research Questions Pairs Plots Can Answer:

**1. Cross-reactivity Assessment**
```
Q: Do ZIKV antibodies cross-react with DENV?
A: Check ZIKV vs DENV1-4 correlations in Flavivirus plot
```

**2. Co-infection Patterns**
```
Q: Are DENV1 and DENV2 co-circulating?
A: High correlation in DENV pairs plot suggests co-circulation
```

**3. Vaccine Impact**
```
Q: Does YFV vaccine induce cross-reactive antibodies?
A: Check YFV correlations with other flaviviruses
```

**4. Alphavirus Relationships**
```
Q: Are CHIKV and ONNV exposures related?
A: Check correlation in Other Arboviruses plot
```

**5. Independent Transmission**
```
Q: Are CCHFV and CHIKV exposures independent?
A: Low correlation suggests independent transmission
```

## Technical Details

### Data Preparation

```r
# Wide format created: one row per sample
sample_wide <- sample_mfi %>%
  pivot_wider(
    names_from = Analyte,
    values_from = MFI_Median,
    values_fn = mean  # Average if sample on multiple plates
  )
```

**Handling:**
- Samples appearing on multiple plates are averaged
- One MFI value per sample per analyte
- NA values automatically handled by ggpairs

### GGally Package

Uses `ggpairs()` function from GGally package:

```r
ggpairs(
  data,
  upper = list(continuous = wrap("cor", size = 5)),      # Correlations
  lower = list(continuous = wrap("points", alpha = 0.5)), # Scatterplots
  diag = list(continuous = wrap("densityDiag"))           # Density plots
)
```

**Features:**
- Automatic correlation calculation (Pearson)
- Customizable plot elements
- Professional appearance
- Handles missing data

## Report Structure Updated

### New Table of Contents:

1. Setup
2. **Sample MFI Distributions by Disease Panel** (8 tabs)
   - DENV
   - Other Arboviruses
   - Malaria
   - NTD
   - Hepatitis & Cholera
   - VPD
   - HHV
   - Controls

3. **Correlation Analysis - Pairs Plots** (NEW)
   - DENV Serotype Correlations (12 antigens, log scale)
   - Flavivirus NS1 Correlations (8 antigens: DENV1-4 + 4 other flaviviruses, log scale)
   - Flavivirus DIII Correlations (8 antigens: DENV1-4 + 4 other flaviviruses, log scale)
   - Other Arbovirus Correlations (14 antigens, log scale)

4. Session Info

### What Was Removed:
- ~~Summary Statistics~~
- ~~MFI Distribution Summary table~~
- ~~Plate-to-Plate Variability table~~
- ~~High Responders table~~

## File Comparison

| Aspect | Version 1.0 | Version 2.0 | Version 3.0 (Current) |
|--------|-------------|-------------|----------------------|
| **Size** | 15 MB | 16 MB | 22 MB |
| **Rendering** | 8.0 sec | 10.3 sec | ~12 sec |
| **Chunks** | 36 | 38 | 40 |
| **Sections** | 9 | 4 | 4 |
| **Summary tables** | 3 | 0 | 0 |
| **Pairs plots** | 0 | 3 (linear) | 4 (log scale) |
| **DENV pairs antigens** | - | 4 NS1 | 12 (all) |
| **Flavivirus pairs** | - | 1 plot (5 NS1) | 2 plots (8 each: NS1 + DIII, all DENV serotypes) |
| **Other arbo pairs antigens** | - | 4 | 14 (all) |
| **Focus** | Distributions + Stats | Distributions + Correlations | Distributions + Comprehensive Correlations |

## Customization Options

### Add More Pairs Plots

```r
# Example: Add Malaria species pairs plot
malaria_analytes <- c("MAL_PfMSP1", "MAL_PvMSP1", "MAL_PmMSP1", "MAL_PoMSP1")

malaria_data <- sample_wide %>%
  select(all_of(malaria_analytes))

ggpairs(malaria_data, title = "Malaria Species Correlations")
```

### Change Correlation Method

```r
# Use Spearman instead of Pearson
ggpairs(
  data,
  upper = list(continuous = wrap("cor", method = "spearman"))
)
```

### Adjust Point Transparency

```r
# Make points more/less transparent
lower = list(continuous = wrap("points", alpha = 0.8))  # Less transparent
lower = list(continuous = wrap("points", alpha = 0.3))  # More transparent
```

### Color by Group

```r
# Color points by plate (requires adding PlateNumber)
ggpairs(
  data,
  mapping = aes(color = factor(PlateNumber)),
  lower = list(continuous = wrap("points", alpha = 0.5))
)
```

## Statistical Notes

### Pearson Correlation Assumptions

The pairs plots use **Pearson correlation** which assumes:
- Linear relationship
- Continuous variables
- Normal distribution (approximately)

**MFI data characteristics:**
- Often log-normally distributed
- Pairs plots use raw MFI values
- May want to log-transform for correlation analysis

### Alternative Approaches

If assumptions violated, consider:
- **Spearman correlation**: Rank-based, non-parametric
- **Log-transformation**: For skewed data
- **Robust correlation**: Less sensitive to outliers

## Example Interpretations

### Scenario 1: High DENV Correlations (r > 0.8)

**Observation:** All DENV serotypes highly correlated

**Possible explanations:**
- Co-circulation of multiple serotypes
- Sequential infections common
- Cross-reactive antibodies dominant
- Hyperendemic transmission

**Next steps:**
- Look at individual distributions
- Check for bimodal patterns
- Consider serotype-specific antigens (DIII)

### Scenario 2: Low Flavivirus Correlations (r < 0.3)

**Observation:** ZIKV not correlated with DENV

**Possible explanations:**
- Limited ZIKV circulation in this population
- Different transmission patterns
- Geographic separation
- Temporal separation of outbreaks

**Next steps:**
- Check ZIKV seroprevalence
- Review outbreak history
- Examine spatial patterns

### Scenario 3: Unexpected High Correlation

**Observation:** CCHFV highly correlated with CHIKV

**Possible explanations:**
- Shared risk factors (livestock, rural areas)
- Co-endemic regions
- NOT cross-reactivity (different families)
- Common exposure pathways

**Next steps:**
- Investigate epidemiological links
- Check geographic distribution
- Review transmission patterns

## Future Enhancements

Potential additions:

1. **Interactive plots** (plotly)
   - Zoom functionality
   - Hover to see sample IDs
   - Click to highlight

2. **Hierarchical clustering**
   - Dendrogram of virus relationships
   - Heat map of correlations

3. **Network analysis**
   - Correlation network graphs
   - Identify clusters

4. **Time series** (if multiple timepoints)
   - Correlation changes over time
   - Seasonal patterns

5. **Geographic stratification**
   - Pairs plots by region
   - Spatial correlation patterns

## Package Requirements

**New dependency:**
- `GGally` - For pairs plots

**Install if needed:**
```r
install.packages("GGally")
```

Or use:
```bash
Rscript install_ggally.R
```

---

## Version History Summary

**Version 3.0 (2026-04-08):**
- Enhanced pairs plots with **log10 transformation** for all MFI values
- Expanded **DENV pairs plot** from 4 to **12 antigens** (all NS1, VLP, DIII for serotypes 1-4)
- Split **Flavivirus pairs** into **two separate plots**:
  - NS1 antigens (8 total: DENV1-4, ZIKV, YFV, WNV, USUV)
  - DIII antigens (8 total: DENV1-4, ZIKV, YFV, WNV, USUV)
- Expanded **Other Arbovirus pairs** from 4 to **14 antigens** (comprehensive coverage)
- Report size: 22 MB, 4 pairs plots total

**Version 2.0 (2026-04-08):**
- Removed summary statistics tables
- Added three correlation pairs plots (DENV serotypes, Flaviviruses, Other Arboviruses)
- Initial implementation of correlation analysis
- Report size: 16 MB, 3 pairs plots

**Version 1.0 (Original):**
- Sample MFI distributions by disease panels (8 tabs)
- Summary statistics tables
- No correlation analysis
- Report size: 15 MB
