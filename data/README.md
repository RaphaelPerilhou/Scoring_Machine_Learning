# Data folder

All paths below are relative to the project root. Notebooks expect this exact layout.

## Layout

```
data/
├── StockUniteLegale_utf8.csv        # SIRENE — national business register (NAF codes)
├── raw/
│   ├── BODACC_TAZ/                  # Raw BODACC XML archives (.taz), one subfolder per year
│   │   ├── 2017/  PCL_BXA2017*.taz
│   │   ├── 2018/  …
│   │   ├── 2019/  …
│   │   ├── 2020/  …
│   │   └── 2021/  …
│   ├── export-detail-bilan.parquet  # INPI — detailed financial statements (nested liasse)
│   └── defaillance.csv              # Banque de France — aggregate default series
├── processed/                       # Outputs of SF_DATA.qmd and SF_EDA_ANALYSIS.qmd
│   ├── bodacc_defaults.csv          # Filtered BODACC rows classified as default events
│   ├── bodacc_full.csv              # BODACC with cleaned SIREN (9-digit, leading zeros)
│   ├── bodacc_naf.parquet           # BODACC defaults joined with NAF sector codes
│   ├── NAF_siren.parquet            # SIRENE subset: siren ↔ NAF code
│   ├── INPI_resto.parquet           # INPI filtered to hotel/restaurant sector, 2017–2021
│   ├── INPI_resto_WIDE_full.parquet # INPI unnested (liasse pivoted to wide format)
│   ├── FINAL_DATABASE.parquet       # Panel dataset with default_event label (Bardos method)
│   ├── DF_Ratios.parquet            # FINAL_DATABASE + financial ratios, NAs imputed
│   └── DF_WINS.csv                  # DF_Ratios winsorised [5%, 95%] — model-ready dataset
└── sub_inpi_resto/                  # Intermediate chunked files produced during unnesting
    └── {year}/
        ├── inpi_{year}.parquet/     # Arrow dataset partition for the year
        ├── Nested/                  # Chunks of ~5 000 rows in nested liasse format
        └── Wide/                    # Same chunks after unnesting and pivot_wider
```

## What is in git

Large raw and intermediate files are excluded via `.gitignore`:

| Excluded | Reason |
|----------|--------|
| `data/raw/` | Multi-GB source files; download links in the root README |
| `data/sub_inpi_resto/` | Intermediate chunking artefacts; fully reproducible from `INPI_resto.parquet` |
| `data/StockUniteLegale_utf8.csv` | Very large SIRENE dump |

`data/processed/` files **are** tracked so collaborators can skip the heavy pipeline steps and jump directly to the EDA / modelling notebook.

## Reproducing from scratch

1. Download the four source files listed in the root README.
2. Place them at the paths shown above.
3. Run `quarto render SF_DATA.qmd` from the project root.

All `data/processed/` files are then regenerated. `data/sub_inpi_resto/` is also rebuilt as a side-effect.
