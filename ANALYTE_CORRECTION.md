# Analyte List Correction

**Date:** 2026-04-08
**Issue:** ARB_MAYV_VLP was incorrectly included in analyte groupings
**Status:** ✅ Fixed and reports re-rendered

## Problem Identified

**User Observation:**
- Empty plot for ARB_MAYV_VLP in Exploratory Analysis Report
- ARB_MAYV_VLP not appearing in QC Report plots

**Root Cause:**
- ARB_MAYV_VLP was included in the analyte groupings in code
- This analyte does NOT exist in the actual xPONENT data files
- Error occurred when creating initial config.R from plan document without verifying against actual data

## Actual Analytes in Data

**Total:** 47 analytes (confirmed)

### Arboviruses (27 total)

**DENV (12 analytes):**
- ARB_DENV1_NS1, ARB_DENV1_VLP, ARB_SNAP_DENV1_DIII
- ARB_DENV2_NS1, ARB_DENV2_VLP, ARB_SNAP_DENV2_DIII
- ARB_DENV3_NS1, ARB_DENV3_VLP, ARB_SNAP_DENV3_DIII
- ARB_DENV4_NS1, ARB_DENV4_VLP, ARB_SNAP_DENV4_DIII

**Other Arboviruses (14 analytes):** ← Corrected from 15
- ARB_CCHFV_NP
- ARB_CHIKV_E2, ARB_CHIKV_VLP
- ARB_ONNV_E2
- ARB_RVFV
- ARB_USUV_NS1, ARB_SNAP_USUV_DIII
- ARB_WNV_NS1, ARB_SNAP_WNV_DIII
- ARB_YFV_NS1, ARB_SNAP_YFV_DIII
- ARB_ZIKV_NS1, ARB_ZIKV_VLP, ARB_SNAP_ZIKV_DIII
- ~~ARB_MAYV_VLP~~ ← **REMOVED**

### Other Panels (20 analytes)

**Malaria (4):**
- MAL_PfMSP1, MAL_PvMSP1, MAL_PmMSP1, MAL_PoMSP1

**NTD (5):**
- NTD_leishmania_K39, NTD_pgp3, NTD_cp23, NTD_VSP5, NTD_NIE

**Cholera (3):**
- CHO_CtxB, CHO_Inaba_OSP, CHO_Ogawa_OSP

**Hepatitis (1):**
- HEP_HEV_ORF2

**VPD (4):**
- VPD_B_pertussis_FHA, VPD_Diphteria_Tox, VPD_measles_NP, VPD_Tetanus_Toxin

**HHV (2):**
- HHV_EBV_gp125, HHV_CMV

**Controls (2):**
- CTRL_BSA, CTRL_SNAP

## Files Corrected

### 1. config.R
**Changes:**
- Removed ARB_MAYV_VLP from expected_analytes list
- Updated to match actual 47 analytes in data files
- Corrected all analyte names to match xPONENT files exactly

### 2. QC_Report.Rmd
**Changes:**
- Removed ARB_MAYV_VLP from Other_Arboviruses group
- Other_Arboviruses now has 14 analytes (was incorrectly 15)

### 3. Exploratory_Analysis.Rmd
**Changes:**
- Removed ARB_MAYV_VLP from Other_Arboviruses group
- Removed MAYV comment section
- Other_Arboviruses now has 14 analytes

## Reports Re-rendered

### Updated Files:
1. **QC_Report.html** (29 MB)
   - Re-rendered: 2026-04-08 22:19
   - Rendering time: 39.6 seconds
   - No longer includes empty MAYV plot

2. **Exploratory_Analysis.html** (15 MB)
   - Re-rendered: 2026-04-08 22:17
   - Rendering time: 8.0 seconds
   - No longer includes empty MAYV plot

## Verification

```r
# Verify actual analytes in data
library(readr)
mfi <- read_csv("outputs/processed_data/mfi_long_all_plates.csv")
analytes <- sort(unique(mfi$Analyte))
length(analytes)  # Should be 47

# Check for MAYV
"ARB_MAYV_VLP" %in% analytes  # Should be FALSE
```

## Lesson Learned

**Best Practice:**
- Always verify analyte lists against actual data files
- Don't assume plan documents are 100% accurate
- Use data-driven approach to determine analytes:

```r
# Good approach
actual_analytes <- unique(mfi_long$Analyte)

# Instead of
expected_analytes <- c(...)  # hardcoded list
```

## Updated Panel Counts

| Panel | Previous Count | Corrected Count |
|-------|----------------|-----------------|
| DENV | 12 | 12 ✓ |
| Other Arboviruses | 15 ❌ | 14 ✓ |
| Malaria | 4 | 4 ✓ |
| NTD | 5 | 5 ✓ |
| Hepatitis + Cholera | 4 | 4 ✓ |
| VPD | 4 | 4 ✓ |
| HHV | 2 | 2 ✓ |
| Controls | 2 | 2 ✓ |
| **Total** | **48** ❌ | **47** ✓ |

## Impact

**Before Fix:**
- Exploratory Analysis had 1 empty plot (ARB_MAYV_VLP)
- User confusion about missing data
- Mismatch between code and data

**After Fix:**
- All 47 plots show actual data
- No empty plots
- Code matches data exactly
- Other_Arboviruses panel: CCHFV → CHIKV → ONNV → RVFV → USUV → WNV → YFV → ZIKV

## Files to Review

Both reports have been updated and should be reviewed:

```bash
open outputs/QC_Report.html
open outputs/Exploratory_Analysis.html
```

Check specifically:
- ✓ No ARB_MAYV_VLP plot appears
- ✓ Other Arboviruses section has 14 plots
- ✓ All plots contain data (no empty plots)
- ✓ Total of 47 analyte plots across all panels

---

**Summary:** ARB_MAYV_VLP was removed from all analyte groupings as it does not exist in the actual data. Both QC and Exploratory reports have been re-rendered with the corrected 47-analyte list.
