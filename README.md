# Flexible Damage Functions - LaTeX Report Template (v2)

A modular, scientific LaTeX template for presenting statistical diagnostics and climate scenario results at the Impact Region (IR) level. Designed for Overleaf compatibility.

## Changes in v2

- **Compact title** (no separate cover page)
- **Last modified date** displayed near title
- **Methodology attribution** to James Rising with Dropbox link
- **Model Evaluation section removed**
- **Summary placeholder** with Lorem Ipsum for each crop
- **New diagnostic layouts**:
  - Page 1: Parameter distributions (large), polynomial summary, predicted vs reported
  - Page 2: Slope analysis with manual questions template
  - Page 3: Worst performance IR
- **Removed**: `global_residuals_time.png`
- **Added**: `tempcrossing_0.png` (placeholder for now)
- **Scenario pages**: 3×2 grid with maps and colored tables
- **Year**: 2098 (configurable)

## Project Structure

```
.
├── main_report.tex              # Main document (compile this)
├── sections/
│   ├── commands.tex             # All macros and custom commands
│   ├── general.tex              # Methodology (from James's doc)
│   └── agriculture.tex          # All 7 crop subsections
├── assets/
│   └── agriculture/
│       ├── <crop>/
│       │   ├── diagnostics/     # PNG diagnostic images
│       │   └── maps/            # PNG maps + CSV tables
│       └── ...
└── README.md
```

## Required Files Per Crop

### Diagnostics (`assets/agriculture/<crop>/diagnostics/`)

| File | Description |
|------|-------------|
| `param_distributions.png` | Parameter distribution plots |
| `polynomial_summary.png` | Summary of polynomial fits |
| `predicted_vs_reported.png` | Model validation scatter |
| `maxslope_hist.png` | Maximum slope histogram |
| `tempcrossing_0.png` | Temperature crossing zero analysis |
| `worst_offenders_by_error.png` | Regions with largest errors |
| `worst_offenders_detail.png` | Detailed worst-performer view |

**Note**: `global_residuals_time.png` is no longer used.

### Maps (`assets/agriculture/<crop>/maps/`)

**Naming convention**: `{type}_{model}_{rcp}_{ssp}_{year}.png`

Example files for one scenario (SSP3, RCP4.5, Low, 2098):
```
raw_low_rcp45_SSP3_2098.png
flex_low_rcp45_SSP3_2098.png
diff_low_rcp45_SSP3_2098.png
```

**CSV files** (same naming, with `_stats.csv` suffix):
```
raw_low_rcp45_SSP3_2098_stats.csv
flex_low_rcp45_SSP3_2098_stats.csv
diff_low_rcp45_SSP3_2098_stats.csv
```

### CSV Column Requirements

**Raw/Flex stats CSV**:
```csv
hierid,rawtotal,Category
USA.1.23,0.0123,Top10
BRA.5.67,-0.0456,Bot10
```

**Diff stats CSV**:
```csv
hierid,sign_match,diff_magnitude,Category
USA.1.23,True,0.0005,Top10
BRA.5.67,False,0.0089,Bot10
```

## Scenario Combinations

The template generates pages for 12 scenario combinations per crop:

| SSP | Model | RCP |
|-----|-------|-----|
| SSP2, SSP3, SSP4 | High, Low | rcp45, rcp85 |

Year is set to **2098** by default. To change, edit `agriculture.tex`:
```latex
\scenariopage{corn}{high}{rcp45}{SSP2}{2100}  % Change 2098 to 2100
```

## Customization

### Changing the Year Globally

In `agriculture.tex`, find and replace all instances of `2098` with your desired year.

### Adding a New Crop

1. Create folders:
   ```
   assets/agriculture/newcrop/diagnostics/
   assets/agriculture/newcrop/maps/
   ```

2. Add to `agriculture.tex`:
   ```latex
   \subsection{New Crop}
   \label{sec:newcrop}
   
   \summaryplaceholder{New Crop}
   \clearpage
   \diagnosticsPageOne{New Crop}{newcrop}
   \diagnosticsPageTwo{New Crop}{newcrop}
   \diagnosticsPageThree{New Crop}{newcrop}
   
   \subsubsection*{Scenario-Based Results}
   \scenariopage{newcrop}{high}{rcp45}{SSP2}{2098}
   % ... add all 12 scenarios
   ```

### Filling in the Analysis Questions

In `commands.tex`, the `\diagnosticsPageTwo` macro creates a box with blanks:
```latex
\item $\beta = 0$ (no crossing): \underline{\hspace{2cm}}\%
```

To fill in values, replace `\underline{\hspace{2cm}}` with your number:
```latex
\item $\beta = 0$ (no crossing): 15.3\%
```

### Enabling CSV Auto-Loading

The template includes placeholder tables. To load from CSV files:

1. Ensure CSV files exist with correct naming
2. In `commands.tex`, uncomment the `\csvreader` blocks in `\rawtable`, `\flextable`, `\difftable`
3. Comment out the placeholder rows

### Adding Row Colors for Diff Tables

The placeholder uses static colors. For dynamic coloring based on CSV data, you'll need a preprocessing step (Python/Lua) to generate colored LaTeX, or use conditional coloring in the CSV reader.

## Diagnostic Page Layouts

### Page 1: Parameter Distributions
```
┌─────────────────────────────┐
│   param_distributions.png   │  (large, centered)
│         (65% width)         │
└─────────────────────────────┘
┌──────────────┬──────────────┐
│  polynomial  │  predicted   │
│  _summary    │  _vs_reported│
└──────────────┴──────────────┘
```

### Page 2: Slope Analysis
```
┌──────────────┬──────────────┐
│  maxslope    │  tempcrossing│
│  _hist       │  _0          │
└──────────────┴──────────────┘
┌─────────────────────────────┐
│   Analysis Questions Box    │
│   (to be filled manually)   │
└─────────────────────────────┘
```

### Page 3: Worst Performance
```
┌─────────────────────────────┐
│  worst_offenders_by_error   │
└─────────────────────────────┘
┌─────────────────────────────┐
│  worst_offenders_detail     │
└─────────────────────────────┘
```

## Scenario Page Layout

```
┌──────────┬──────────┬──────────┐
│   Raw    │   Flex   │   Diff   │  Row 1: Maps
│   Map    │   Map    │   Map    │
├──────────┼──────────┼──────────┤
│   Raw    │   Flex   │   Diff   │  Row 2: Tables
│  Table   │  Table   │  Table   │  (Top10/Bot10)
└──────────┴──────────┴──────────┘
```

**Table features**:
- IR names truncated to 7 characters
- Values rounded to 4 decimal places
- Diff table: green rows (match), red rows (mismatch)
- Horizontal line separating Top 10 / Bottom 10

## Key Macros

| Macro | Usage |
|-------|-------|
| `\summaryplaceholder{Name}` | Lorem ipsum placeholder page |
| `\diagnosticsPageOne{Name}{folder}` | First diagnostics layout |
| `\diagnosticsPageTwo{Name}{folder}` | Second diagnostics + questions |
| `\diagnosticsPageThree{Name}{folder}` | Worst performance page |
| `\diagnosticpanel{Name}{folder}` | All three pages combined |
| `\scenariopage{crop}{model}{rcp}{ssp}{year}` | Full scenario page |
| `\allscenarios{crop}{year}` | All 12 scenarios |
| `\trunc[n]{text}` | Truncate text to n chars (default 7) |

## Packages Used

All standard Overleaf packages:
- `geometry`, `graphicx`, `subcaption`, `float`, `placeins`
- `booktabs`, `siunitx`, `csvsimple`, `colortbl`
- `amsmath`, `hyperref`, `fancyhdr`, `xcolor`
- `lipsum` (for Lorem Ipsum placeholders)
- `xstring` (for text truncation)

## Troubleshooting

**Missing figures**: Check path format matches `{crop}/diagnostics/{file}.png`

**Tables not rendering**: Ensure CSV uses comma separator and has required columns

**Colors not showing**: Make sure `colortbl` package is loaded (included by default)

**Truncation not working**: Ensure `xstring` package is loaded

## License

Template provided for scientific documentation. Modify freely.
