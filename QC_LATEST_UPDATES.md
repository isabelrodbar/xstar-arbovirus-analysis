# QC Report - Latest Updates

**Date:** 2026-04-08 (Update 2)
**Version:** 2.1
**File:** `QC_Report.html` (29 MB)
**Rendering time:** 37.6 seconds

## Changes Implemented

### 1. ✅ Standard Curve Quality Summary by Analyte

**New Feature:** Added a comprehensive table showing standard curve quality **by analyte** (in addition to the existing by-plate summary).

**Location in Report:**
- Summary Across All Plates → Standard Curve Quality Metrics section
- Appears after "Standard Curve Quality by Plate" table

**Table Columns:**

| Column | Description |
|--------|-------------|
| **Analyte** | Analyte name |
| **N Plates** | Number of plates (should be 6) |
| **Median R²** | Median R² across all plates |
| **Min R²** | Worst (lowest) R² across plates |
| **Max R²** | Best (highest) R² across plates |
| **N Poor Plates** | Number of plates where R² < 0.95 |
| **% Poor Plates** | Percentage of plates with poor curves |
| **Mean Dynamic Range** | Average fold-change (max/min MFI) |

**Sorting:**
- Primary: % Poor Plates (descending) - **most problematic first**
- Secondary: Median R² (ascending) - worst curves first within same %

**Interpretation:**

**High % Poor Plates (e.g., 50-100%):**
- Analyte has **systemic issues** across multiple plates
- Likely an **assay-specific problem**, not plate-specific
- Possible causes:
  - Poor standard quality for this analyte
  - Problematic bead coupling
  - Non-specific binding
  - Intrinsic assay performance issues
- **Action:** Consider excluding this analyte or re-optimizing the assay

**Low % Poor Plates (e.g., 0-17%):**
- Most plates have good curves
- Poor curves are **plate-specific**, not assay-specific
- Isolated technical issues
- **Action:** Review specific problematic plates

**Example Interpretation:**

```
Analyte           N Plates  Median R²  Min R²  Max R²  N Poor  % Poor  Dynamic Range
ARB_PROBLEMATIC   6         0.88       0.75    0.94    5       83.3%   450
VPD_GOOD          6         0.98       0.96    0.99    0       0.0%    2500
MAL_MODERATE      6         0.94       0.91    0.97    2       33.3%   1800
```

- **ARB_PROBLEMATIC**: 83% of plates have poor curves → systemic analyte issue
- **VPD_GOOD**: Perfect across all plates → excellent assay
- **MAL_MODERATE**: 2/6 plates poor → check those specific plates

**Benefits:**
- **Identify problematic analytes** that need assay optimization
- **Distinguish assay issues** from plate-specific technical problems
- **Prioritize troubleshooting** efforts
- **Compare analyte performance** across the panel

### 2. ✅ Reorganized Disease Panel Groupings

**Updated Groupings for Count Beeswarm Plots:**

#### Previous Organization (7 panels):
1. Arbovirus (27 analytes) - too crowded
2. Malaria (4 analytes)
3. NTD (5 analytes)
4. Cholera (3 analytes) - small panel
5. Hepatitis (1 analyte) - very small panel
6. VPD (4 analytes)
7. HHV (2 analytes)
8. Controls (2 analytes)

#### New Organization (8 panels):

**1. DENV (12 analytes)**
- ARB_DENV1_NS1, ARB_DENV1_VLP, ARB_SNAP_DENV1_DIII
- ARB_DENV2_NS1, ARB_DENV2_VLP, ARB_SNAP_DENV2_DIII
- ARB_DENV3_NS1, ARB_DENV3_VLP, ARB_SNAP_DENV3_DIII
- ARB_DENV4_NS1, ARB_DENV4_VLP, ARB_SNAP_DENV4_DIII

**Why separate:** DENV is a major focus with multiple serotypes and antigen types (NS1, VLP, DIII)

**2. Other Arboviruses (15 analytes)**
- ARB_CCHFV_NP
- ARB_CHIKV_E2, ARB_CHIKV_VLP
- ARB_ONNV_E2
- ARB_RVFV
- ARB_USUV_NS1, ARB_SNAP_USUV_DIII
- ARB_WNV_NS1, ARB_SNAP_WNV_DIII
- ARB_YFV_NS1, ARB_SNAP_YFV_DIII
- ARB_ZIKV_NS1, ARB_ZIKV_VLP, ARB_SNAP_ZIKV_DIII
- ARB_MAYV_VLP

**Why separate:** Each arbovirus has unique characteristics, easier to compare with DENV separate

**3. Malaria (4 analytes)** - unchanged
- MAL_PfMSP1, MAL_PvMSP1, MAL_PmMSP1, MAL_PoMSP1

**4. NTD (5 analytes)** - unchanged
- NTD_leishmania_K39, NTD_pgp3, NTD_cp23, NTD_VSP5, NTD_NIE

**5. Hepatitis + Cholera (4 analytes)** - **combined**
- HEP_HEV_ORF2
- CHO_CtxB, CHO_Inaba_OSP, CHO_Ogawa_OSP

**Why combined:** Both are enteric/waterborne pathogens, similar exposure routes

**6. VPD (4 analytes)** - unchanged
- VPD_B_pertussis_FHA, VPD_Diphteria_Tox, VPD_measles_NP, VPD_Tetanus_Toxin

**7. HHV (2 analytes)** - unchanged
- HHV_EBV_gp125, HHV_CMV

**8. Controls (2 analytes)** - unchanged
- CTRL_BSA, CTRL_SNAP

**Benefits of New Grouping:**

1. **DENV panel more manageable**
   - Compare all 4 serotypes side-by-side
   - Easier to spot serotype-specific issues
   - See if NS1/VLP/DIII have different count patterns

2. **Other arboviruses less crowded**
   - Better x-axis label readability
   - Easier to identify individual virus issues
   - Compare flaviviruses (ZIKV, YFV, WNV) vs alphaviruses (CHIKV, MAYV)

3. **Hepatitis + Cholera logical**
   - Both enteric/fecal-oral transmission
   - Similar epidemiology in study context
   - Efficient use of plot space

4. **Better visual balance**
   - No panel has >15 analytes
   - More consistent panel sizes
   - Easier to read x-axis labels

### Updated Report Structure

**Standard Curve Quality Metrics Section Now Contains:**

1. **Standard Curve Quality by Plate** (existing)
   - Shows which plates have issues
   - 6 rows (one per plate)

2. **Standard Curve Quality by Analyte** (NEW)
   - Shows which analytes have issues
   - 47 rows (one per analyte)
   - Sorted by % Poor Plates (worst first)

3. **Individual Poor Quality Curves** (updated)
   - Detailed list of specific plate-analyte combinations with R² < 0.95
   - Now shows top 30 instead of top 20
   - Helps identify specific curves to review

**Each Plate Count Distributions Now Show:**

8 beeswarm plots (instead of 7):
- DENV (12 analytes)
- Other Arboviruses (15 analytes)
- Malaria (4)
- NTD (5)
- Hepatitis + Cholera (4)
- VPD (4)
- HHV (2)
- Controls (2)

## Visual Comparison

### DENV Panel (New)
```
Count
 150|
    |    ●  ●     ●  ●      ●  ●     ●
 100|  ● ●●●●   ●●●●●●   ●●●●●   ●●●●
    | ●●●●●●●  ●●●●●●●  ●●●●●●  ●●●●●
  50|●●●●●●●● ●●●●●●●● ●●●●●●● ●●●●●
    |●●●●●●●● ●●●●●●●● ●●●●●●● ●●●●●
   0|_________________________________
     D1   D1   D1   D2   D2   D2   ...
     NS1  VLP  DIII NS1  VLP  DIII
```

All DENV serotypes and antigen types visible in one view!

### Other Arboviruses Panel (New)
```
Count
 150|
    |      ●       ●      ●      ●
 100|    ●●●     ●●●    ●●●    ●●●
    |   ●●●●●   ●●●●   ●●●●   ●●●●
  50|  ●●●●●●  ●●●●●  ●●●●●  ●●●●●
    | ●●●●●●●  ●●●●●  ●●●●●  ●●●●●
   0|_________________________________
     CCHFV CHIKV CHIKV ONNV RVFV ...
       NP    E2   VLP   E2
```

Each non-DENV arbovirus clearly labeled!

## Quick Reference

### Finding Problematic Analytes

**Step 1:** Go to "Standard Curve Quality Metrics" section

**Step 2:** Look at "Standard Curve Quality by Analyte" table

**Step 3:** Check "% Poor Plates" column
- **>50%**: Serious analyte-specific issue
- **17-50%**: Moderate concern, check patterns
- **<17%**: Likely isolated plate issues

**Step 4:** Cross-reference with "Min R²" column
- If Min R² is very low (<0.80), some plates are very problematic

**Step 5:** Review individual curves in plots to understand why

### Interpreting DENV vs Other Arboviruses Split

**Compare within DENV panel:**
- Are all 4 serotypes similar? → Good assay consistency
- Is one serotype different? → Check that standard preparation
- Are NS1 counts different from VLP/DIII? → Expected (different antigen types)

**Compare within Other Arboviruses:**
- Are flaviviruses (ZIKV, YFV, WNV, USUV) similar? → Good panel consistency
- Are alphaviruses (CHIKV, MAYV, ONNV) similar? → Good panel consistency
- Big differences? → Check bead coupling or standard quality

## Customization

### Modify Panel Groupings

Edit the `analyte_groups` list in `QC_Report.Rmd`:

```r
analyte_groups <- list(
  # Example: Separate flaviviruses from alphaviruses
  Flaviviruses = c("ARB_DENV1_NS1", ..., "ARB_ZIKV_NS1", ...),
  Alphaviruses = c("ARB_CHIKV_E2", "ARB_MAYV_VLP", ...),
  # ... other panels
)
```

### Adjust R² Threshold for "Poor"

Currently set to 0.95. To change:

**Line ~695-700:**
```r
# Change 0.95 to your preferred threshold (e.g., 0.90)
N_Poor_Plates = sum(R_squared < 0.90, na.rm = TRUE)
```

## Files Updated

- `QC_Report.Rmd` - Main R Markdown file
- `outputs/QC_Report.html` - Updated HTML report (29 MB)

## Changes Summary

✅ **Added:** Standard curve quality summary table by analyte
✅ **Modified:** Disease panel groupings (DENV separate, Hep+Cholera combined)
✅ **Updated:** Individual poor curves table (now shows top 30)
✅ **Improved:** Interpretability and identification of problematic analytes

## What to Look For in Your Data

When you open the updated report, check:

1. **By Analyte table:** Which analytes have >50% poor plates?
   - These need assay optimization
   - May need to be excluded from analysis

2. **DENV panel:** Are all serotypes performing similarly?
   - Should see consistent patterns across DENV1-4

3. **Hepatitis + Cholera panel:** Are counts adequate?
   - These were previously in tiny separate panels

4. **Dynamic Range column:** Are poor analytes also low dynamic range?
   - Low R² + low dynamic range = serious assay issue

---

**Next time you render:** Just run `Rscript render_qc_report.R` - all changes are incorporated!
