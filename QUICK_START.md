# Quick Start Guide

## Running the Pipeline

### Prerequisites

```r
# Install packages (if not already installed)
install.packages(c("tidyverse", "readr", "dplyr", "tidyr", "stringr"))
```

### Run Pipeline

```bash
# Navigate to Code directory
cd "/Users/isabelrodriguez/Library/CloudStorage/Box-Box/Xstar_Arbovirus/Code"

# Run main pipeline
Rscript 01_extract_and_merge_data.R

# Validate outputs
Rscript validate_outputs.R

# View example usage
Rscript example_usage.R
```

### Or in R/RStudio

```r
# Set working directory
setwd("/Users/isabelrodriguez/Library/CloudStorage/Box-Box/Xstar_Arbovirus/Code")

# Run pipeline
source("01_extract_and_merge_data.R")

# Validate
source("validate_outputs.R")

# Examples
source("example_usage.R")
```

## Expected Output

```
=== Processing Summary ===
Plates processed: 5, 6, 7, 8, 9, 10
Total wells: 576
Total analytes: 47
Date range: 2026-03-30 to 2026-03-31

=== Pipeline Complete! ===
All outputs saved to: outputs/processed_data

Generated files:
  1. mfi_long_all_plates.csv   - Median MFI in long format
  2. count_long_all_plates.csv - Count data in long format
  3. mfi_wide_all_plates.csv   - Median MFI in wide format
  4. count_wide_all_plates.csv - Count data in wide format
```

## Loading Processed Data

```r
library(tidyverse)

# Load long format (recommended for analysis)
mfi <- read_csv("outputs/processed_data/mfi_long_all_plates.csv")
counts <- read_csv("outputs/processed_data/count_long_all_plates.csv")

# Load wide format (for inspection)
mfi_wide <- read_csv("outputs/processed_data/mfi_wide_all_plates.csv")

# Quick summary
summary(mfi)
table(mfi$PlateNumber)
```

## Common Tasks

### View data for one plate

```r
plate5 <- mfi %>% filter(PlateNumber == 5)
```

### View one analyte across all plates

```r
zikv <- mfi %>% filter(Analyte == "ARB_ZIKV_NS1")
```

### Get standard curve data

```r
standards_p5 <- mfi %>%
  filter(PlateNumber == 5, grepl("^S_", TrueSampleID)) %>%
  arrange(TrueSampleID)
```

### View blanks

```r
blanks <- mfi %>%
  filter(grepl("BLANK", TrueSampleID, ignore.case = TRUE))
```

## Troubleshooting

### Issue: Files not found

**Solution:** Check that you're in the `/Code` directory and that input files exist in `../Data/`

### Issue: Package errors

**Solution:** Install required packages:
```r
install.packages(c("tidyverse", "readr", "dplyr", "tidyr", "stringr"))
```

### Issue: Wrong row counts

**Solution:** Re-run the pipeline. Expected counts:
- Long format: 27,072 rows (96 × 6 × 47)
- Wide format: 576 rows (96 × 6)

## File Structure

```
Code/
├── config.R                      # Configuration
├── functions_parsing.R           # Parsing functions
├── functions_processing.R        # Processing functions
├── 01_extract_and_merge_data.R  # Main pipeline
├── validate_outputs.R            # Validation
├── example_usage.R               # Usage examples
└── outputs/
    └── processed_data/
        ├── mfi_long_all_plates.csv
        ├── count_long_all_plates.csv
        ├── mfi_wide_all_plates.csv
        └── count_wide_all_plates.csv
```

## Next Steps

1. Run QC checks on bead counts
2. Generate standard curves
3. Calculate Net MFI (sample - blank)
4. Screen for positive samples
5. Create visualizations

## Help

For detailed information, see:
- `README.md` - Full documentation
- `IMPLEMENTATION_SUMMARY.md` - Technical details
- `example_usage.R` - Code examples
