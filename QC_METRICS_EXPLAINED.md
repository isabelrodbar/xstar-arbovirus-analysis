# QC Metrics Explained

## Standard Curve Quality Definitions

### How R² is Calculated

For each **plate-analyte combination**, the QC report calculates:

```r
R² = cor(log10(Dilution), log10(MFI_Median + 1))^2
```

**Breakdown:**
1. Takes the standard curve dilutions (e.g., 1/50, 1/100, 1/200, etc.)
2. Takes the corresponding MFI values for those standards
3. Log-transforms both (base 10)
4. Calculates Pearson correlation coefficient
5. Squares it to get R²

**Note:** The `+ 1` is added to MFI to avoid taking log of zero.

### What is R²?

**R² (R-squared)** represents the **proportion of variance explained** by the linear relationship between log(Dilution) and log(MFI).

- **R² = 1.0**: Perfect linear relationship (all points fall exactly on a line)
- **R² = 0.95**: 95% of variance explained (very good fit)
- **R² = 0.90**: 90% of variance explained (acceptable fit)
- **R² < 0.90**: Poor fit (curve may be non-monotonic, noisy, or unreliable)

### Important Clarifications

**This is NOT a formal regression R²**, it's calculated as:
- Correlation coefficient squared
- No actual regression model is fit
- No slope, intercept, or residuals are calculated

**Why log-log scale?**
- Standard curves typically follow a sigmoidal (S-shaped) relationship
- On log-log scale, the linear portion of the curve should be approximately linear
- Makes it easier to assess linearity across the dynamic range

## Definitions Used in QC Report

### Individual Analyte Level

**"Poor Standard Curve"** = Any plate-analyte combination where:
- R² < 0.95, OR
- R² is NA (couldn't be calculated)

**Reasons for NA R²:**
- Fewer than 3 data points
- No variance in dilutions (all the same)
- Missing MFI values

### Plate Level

A **"Plate with Poor Standard Curves"** is flagged if:
- The plate has **>5 analytes** with R² < 0.95

**Example:**
- Plate 5 has 47 analytes
- If 6 or more analytes have R² < 0.95, the plate gets flagged
- This represents ~13% of analytes failing QC

## QC Thresholds Summary

| Metric | Threshold | Meaning |
|--------|-----------|---------|
| **Individual curve R²** | < 0.95 | Poor quality curve |
| **Individual curve R²** | ≥ 0.95 | Acceptable quality |
| **Plate-level poor curves** | > 5 analytes | Plate flagged for review |
| **Bead count** | < 30 | Low count flag |
| **Overall flag rate** | > 5% | High flag rate warning |
| **Plate flag rate** | > 10% | Plate flagged for review |

## How to Interpret R² Values

### Excellent (R² ≥ 0.98)
- Very tight correlation
- Points closely follow expected curve
- High confidence in quantification

### Good (0.95 ≤ R² < 0.98)
- Acceptable correlation
- Some scatter but overall good trend
- Suitable for most analyses

### Marginal (0.90 ≤ R² < 0.95)
- Noticeable scatter
- May still be usable with caution
- Consider excluding from sensitive analyses

### Poor (R² < 0.90)
- Substantial deviation from linearity
- May indicate technical issues:
  - Pipetting errors
  - Bead aggregation
  - Low bead counts
  - Non-specific binding
- Recommend excluding from analysis

## Visual Inspection is Critical

**R² alone doesn't tell the whole story!** Always visually inspect standard curves for:

1. **Monotonicity**: MFI should consistently decrease with dilution
2. **Dynamic range**: Should span at least 2-3 orders of magnitude
3. **Low count points**: Points with <30 beads (shown in red) are less reliable
4. **Outliers**: Single aberrant points can lower R² but may not invalidate the curve
5. **Plateau effects**: Very high or very low MFI values may plateau

## Customizing Thresholds

If you want to change the QC criteria, edit these lines in `QC_Report.Rmd`:

### Change R² threshold for "poor" curves

**Current (line 670, 682):**
```r
N_Poor_Curves = sum(R_squared < 0.95, na.rm = TRUE)
poor_curves <- standard_curve_quality %>%
  filter(R_squared < 0.95 | is.na(R_squared))
```

**Example - More strict (R² < 0.98):**
```r
N_Poor_Curves = sum(R_squared < 0.98, na.rm = TRUE)
poor_curves <- standard_curve_quality %>%
  filter(R_squared < 0.98 | is.na(R_squared))
```

**Example - Less strict (R² < 0.90):**
```r
N_Poor_Curves = sum(R_squared < 0.90, na.rm = TRUE)
poor_curves <- standard_curve_quality %>%
  filter(R_squared < 0.90 | is.na(R_squared))
```

### Change plate-level threshold

**Current (line 760):**
```r
poor_curve_plates <- summary_table %>% filter(N_Poor > 5)
```

**Example - More strict (>3 poor curves):**
```r
poor_curve_plates <- summary_table %>% filter(N_Poor > 3)
```

**Example - Less strict (>10 poor curves):**
```r
poor_curve_plates <- summary_table %>% filter(N_Poor > 10)
```

Then re-render the report:
```r
source("render_qc_report.R")
```

## Alternative Metrics (Not Currently Implemented)

You may want to consider implementing additional metrics:

### 1. Formal Linear Regression R²
```r
# Fit linear model and extract R²
model <- lm(log10(MFI_Median + 1) ~ log10(Dilution), data = standards)
R_squared <- summary(model)$r.squared
```

### 2. Coefficient of Variation (CV)
- Calculate %CV for replicate standards (if you have replicates)
- Flag standards with CV > 20% or 25%

### 3. 4-Parameter Logistic (4PL) Fit
- More appropriate for full sigmoidal curves
- Requires fitting package like `drc`
- Provides EC50, hillslope, asymptotes

### 4. Dynamic Range
- Already calculated in report
- Ratio of highest to lowest MFI
- Good curves typically have >100-fold dynamic range

### 5. Parallelism Testing
- Compare slopes across plates
- Identify plate-to-plate differences
- Requires formal regression models

## Recommendations for Your Data

Based on typical Luminex assay standards:

**Recommended R² threshold: 0.95**
- Standard used in many multiplex assay papers
- Balances sensitivity and specificity
- Flags truly problematic curves while accepting minor scatter

**For publication-quality data:**
- Consider R² ≥ 0.98
- Require dynamic range ≥ 100
- Exclude curves with >2 low-count points

**For exploratory screening:**
- R² ≥ 0.90 may be acceptable
- Focus on positive/negative classification rather than absolute quantification

## Where to Find Metrics in QC Report

1. **Standard Curve Quality Metrics section**
   - Table showing R² for each plate-analyte combination
   - Lists all curves with R² < 0.95

2. **Executive Summary**
   - Plate-level summary table
   - "N Poor Curves" column shows count per plate
   - "Median R²" shows overall curve quality per plate

3. **Standard Curves plots**
   - Visual inspection (most important!)
   - Look for smooth monotonic decrease
   - Check for red points (low counts)

## Questions to Ask When Reviewing

1. **Are poor curves clustered?**
   - Same plate? → Plate-level technical issue
   - Same analyte? → Analyte-specific problem
   - Random? → May be acceptable

2. **Do poor curves still look reasonable?**
   - Some curves may have lower R² due to one outlier
   - Visual inspection may reveal curve is still usable

3. **Are there many low-count points?**
   - Curves with many <30 bead points are less reliable
   - May need to optimize bead coupling or dilution

4. **Is the dynamic range adequate?**
   - Need sufficient range to bracket your samples
   - Narrow range → poor discrimination

## Further Reading

- Luminex xMAP Technology: Best Practices
- FDA Guidance: Bioanalytical Method Validation
- CLSI EP06: Evaluation of Linearity of Quantitative Measurement Procedures

---

**Summary:** A standard curve is flagged as "poor" if R² < 0.95, where R² measures the correlation between log(Dilution) and log(MFI). A plate is flagged if >5 analytes have poor curves. These thresholds are customizable and should be combined with visual inspection of the curves.
