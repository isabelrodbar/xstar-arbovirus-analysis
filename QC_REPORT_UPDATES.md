# QC Report Updates - Summary of Changes

**Date:** 2026-04-08
**Version:** 2.0
**File:** `QC_Report.html` (28 MB)
**Rendering time:** 38.3 seconds

## Changes Implemented

### 1. Enhanced Standard Curves (Individual Plates) ✅

**Previous:** Simple standard curves with just dilution points

**Now includes:**
- **Sample MFI values** displayed as orange rug plot on the right Y-axis
  - Shows where actual samples fall relative to the standard curve
  - Helps identify if samples are within, above, or below the curve range
  - Orange color for easy visibility

- **Blank reference line** displayed as gray horizontal dashed line
  - Shows mean MFI value across all blank wells for that analyte
  - Helps assess background signal level
  - Useful for identifying positive samples (those above blank + threshold)

- **Legend explanation** added below each plate's standard curve section

**Interpretation:**
- **Orange rug ticks (right side)**: Each tick represents one sample's MFI value
  - Clustered ticks near blank line = mostly negative samples
  - Ticks spread across curve = range of responses
  - Ticks above curve = very high responses (potential positives)

- **Gray dashed line**: Background/blank level
  - Samples below this = likely negative
  - Samples significantly above = potential positives

### 2. Shared Legend (Summary Standard Curves) ✅

**Previous:** Each of 47 plots had its own small, hard-to-read legend

**Now:**
- **Single shared legend** at the bottom of the entire section
- Applies to all 47 analyte plots
- Much larger and more visible
- Two components:
  - **Plate colors** (Set2 palette): Shows which plate each curve belongs to
  - **Point shapes**: Circle (●) = adequate bead count, X (✕) = low count

**Benefits:**
- Easier to compare curves across analytes
- More space for actual plots
- Cleaner, more professional appearance
- Legend is always visible when scrolling

**Implementation:**
- Used patchwork's `guides = "collect"` feature
- All plots share the same color/shape scales

### 3. Count Distribution Beeswarm Plots ✅

#### Per Plate Section

**Previous:** 47 individual histograms per plate

**Now:** Beeswarm plots **grouped by disease panel**

**Panels:**
1. **Arbovirus** (27 analytes)
   - All ARB_ prefixed analytes
   - DENV1-4, ZIKV, CHIKV, YFV, WNV, etc.

2. **Malaria** (4 analytes)
   - Pf, Pv, Pm, Po MSP1

3. **NTD** (5 analytes)
   - Leishmania, Schistosomiasis, Chlamydia, Giardia, Strongyloides

4. **Cholera** (3 analytes)
   - CtxB, Inaba OSP, Ogawa OSP

5. **Hepatitis** (1 analyte)
   - HEV ORF2

6. **VPD** (4 analytes)
   - Pertussis, Diphtheria, Measles, Tetanus

7. **HHV** (2 analytes)
   - EBV, CMV

8. **Controls** (2 analytes)
   - BSA, SNAP

**Benefits:**
- See all related analytes in one view
- Easier to spot panel-specific issues
- Compare counts across related assays
- Less overwhelming than 47 separate plots

**Features:**
- Each point = one well
- Blue points = counts ≥ 30 (good)
- Red points = counts < 30 (flagged)
- Red dashed line at threshold (30)
- X-axis labels rotated 45° for readability

#### Summary Section

**Already had:** Beeswarm plots showing all 6 plates for each analyte

**No changes needed** - this was already implemented correctly:
- 47 plots (one per analyte)
- Each plot shows all 6 plates side-by-side
- Color-coded by flag status
- Easy plate-to-plate comparison

## New Features Added

### Analyte Grouping System

Created a structured grouping system in the code:

```r
analyte_groups <- list(
  Arbovirus = c("ARB_CCHFV_NP", "ARB_CHIKV_E2", ...),
  Malaria = c("MAL_PfMSP1", ...),
  NTD = c("NTD_leishmania_K39", ...),
  ...
)
```

This makes it easy to:
- Generate panel-specific plots
- Analyze results by disease category
- Customize which analytes appear in which panel

### Enhanced Helper Functions

1. **`plot_standard_curve_enhanced()`**
   - Takes standards, samples, and blanks data
   - Calculates mean blank
   - Adds rug plot and reference line
   - Returns enhanced ggplot

2. **`plot_count_beeswarm_panel()`**
   - Creates beeswarm plot for a group of analytes
   - Automatically handles coloring by flag status
   - Adds threshold line
   - Customizable panel name

## Visual Guide

### Standard Curve Elements

```
Log(MFI)
   |                                    ╎ ← Orange rug ticks (samples)
10^4|    ●━━●                          ╎
    |      ━━●                         ╎
    |         ━━●                      ╎
10^3|            ━━●                   ╎
    |               ━━●━━●━━✕          ╎
    |┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈ ← Gray dashed (blank)
10^2|                                  ╎
    +-----------------------------------|
     1/50  1/100  1/200    ...   1/51200
              Log(Dilution)

Legend:
● = Standard with count ≥ 30
✕ = Standard with count < 30
━ = Standard curve line
┈ = Mean blank MFI
╎ = Sample values (rug)
```

### Beeswarm Count Plot

```
Count
 150|
    |    ●
 100|  ● ● ●     ●  ●
    | ●● ●●●   ●●●●●●
  50|●●●●●●●  ●●●●●●●
    |●●●●●●● ●●●●●●●●
 ┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈ ← Threshold (30)
  30|●●●●●●  ●●●●●●●
    | ●● ●    ● ●
    |  ×       ×   ×     ← Red = flagged
   0|___________________
     ARB_  ARB_  ARB_
    CCHFV CHIKV DENV1
      NP    E2   NS1
```

## Report Sections Updated

### Individual Plate Sections (Plates 5-10)

Each plate now has:
1. **Standard Curves** tab
   - 47 enhanced plots (4 columns × 12 rows)
   - Orange rug for samples
   - Gray line for blanks
   - Legend explanation

2. **Count Distributions** tab
   - 8 panel-grouped beeswarm plots
   - Arranged in 2 columns

3. **QC Summary** tab
   - Unchanged (top 20 analytes table)

### Summary Section

1. **Standard Curves by Analyte**
   - 47 plots with **shared legend at bottom**
   - All 6 plates overlaid per analyte
   - 3 columns × 16 rows

2. **Count Distributions by Analyte**
   - 47 beeswarm plots (unchanged)
   - One per analyte, all plates shown

3. **Count Summary Statistics**
   - Unchanged

4. **Standard Curve Quality Metrics**
   - Unchanged

## File Sizes and Performance

- **Report size:** 28 MB (up from 26 MB)
  - Additional 2 MB due to rug plots and panel beeswarms
  - Still reasonable for HTML with embedded images

- **Rendering time:** 38.3 seconds (down from 47.7 seconds!)
  - Faster due to fewer individual plots
  - Grouping reduced plot count from ~600 to ~400

- **Chunks processed:** 76 (up from 74)
  - Added analyte grouping and sample/blank data prep

## How to Interpret New Features

### Orange Rug Plot (Sample Values)

**What it shows:**
- Distribution of actual sample MFI values for this analyte
- Helps assess if samples fall within the standard curve range

**Interpretation examples:**

1. **Most ticks near blank line**
   - Samples are mostly negative
   - Good for assays where few positives are expected

2. **Ticks spread across curve**
   - Range of responses from low to high
   - May indicate varying levels of exposure/antibodies

3. **Many ticks above curve**
   - Samples exceed highest standard
   - May need to dilute samples or extend standard curve

4. **Ticks below curve**
   - Very low signals, near or below blank
   - Likely negative samples

### Gray Blank Line

**What it shows:**
- Mean MFI across all blank wells for this analyte
- Represents background/noise level

**Interpretation:**
- Samples below this line = likely true negatives
- Samples slightly above = borderline (need cutoff analysis)
- Samples well above (>3× blank) = likely positives
- If blanks are high, may indicate:
  - Non-specific binding
  - Bead quality issues
  - Cross-reactivity

### Panel-Grouped Beeswarms

**Benefits:**
- Spot panel-wide issues (e.g., all malaria counts low)
- Compare related assays (e.g., DENV1-4 NS1 counts)
- Identify analyte-specific problems within a panel

**What to look for:**
- **Consistent counts across panel** = good
- **One analyte much lower** = check that specific assay
- **Whole panel low** = panel-wide issue (bead coupling, etc.)

## Customization Options

### Change Disease Panel Groupings

Edit the `analyte_groups` list in the R Markdown:

```r
# Example: Create a "DENV-specific" panel
analyte_groups <- list(
  DENV_Specific = c("ARB_DENV1_NS1", "ARB_DENV2_NS1",
                    "ARB_DENV3_NS1", "ARB_DENV4_NS1"),
  # ... rest of panels
)
```

### Change Rug Plot Color

In `plot_standard_curve_enhanced()` function:

```r
# Change from orange to another color
geom_rug(..., color = "purple", ...)  # or "darkgreen", "#FF6B6B", etc.
```

### Adjust Beeswarm Density

In `plot_count_beeswarm_panel()` function:

```r
# Change cex parameter (higher = more spread out)
geom_beeswarm(..., cex = 2.0, ...)  # default is 1.5
```

## Known Limitations

1. **Rug plot density**
   - With many samples (>50), rug can become crowded
   - May overlap if samples have similar MFI values
   - Consider alpha transparency to see overlaps

2. **Blank line calculation**
   - Uses mean of all blanks for that analyte
   - Doesn't account for outlier blanks
   - Consider using median if blanks are variable

3. **Panel groupings**
   - Currently hard-coded in R Markdown
   - Need to manually update if analytes change
   - Could be made more flexible with config file

## Future Enhancements

Potential additions for future versions:

1. **Cutoff lines on standard curves**
   - Add positivity cutoff (e.g., blank mean + 3×SD)
   - Show how many samples are above cutoff

2. **Sample density contours**
   - Instead of rug, use density plot overlay
   - Easier to see distribution with many samples

3. **Interactive plots**
   - Use plotly for zoom/hover
   - Click on samples to see IDs
   - Filter by sample type

4. **Plate heatmaps**
   - 96-well layout view
   - Color by MFI or count
   - Spot spatial patterns

5. **Automated panel detection**
   - Read analyte list from config
   - Auto-group by prefix (ARB_, MAL_, etc.)
   - No hard-coding needed

## Questions?

See also:
- `QC_README.md` - How to use the QC report
- `QC_METRICS_EXPLAINED.md` - Understanding R² and thresholds
- `README.md` - Main pipeline documentation

---

**Summary:** The updated QC report now includes sample distributions (orange rug), blank reference lines (gray dashed), a shared legend for summary plots, and panel-grouped beeswarm plots for better visualization and interpretation of quality control metrics.
