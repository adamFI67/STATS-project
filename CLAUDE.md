# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project overview

This is a STAT 3355 (UTD) course project analyzing crime in Dallas. It is an R/R Markdown project, not a software application — there is no build system, package manifest, or test suite. The deliverable for each sub-question is a knitted PDF report.

The analysis is split into four independent sub-questions, each in its own top-level directory and largely self-contained:

- `Where Implementation/` — `where.rmd`: do crimes occur closer to or farther from highways? Joins Dallas crime data with Census ACS population data (via `tidycensus`) and TIGER highway/zip geometries (via `tigris`), normalizes incident counts per 1k residents by zip code, and runs t-tests/Wilcoxon tests comparing zip codes with 2+ highway intersections vs. 0–1.
- `Why Implementation/` — `why.rmd` (and `why_dataCleaning.rmd`, an earlier/alternate version): does crime correlate with crude oil prices (used as a proxy for economic stress)? Joins daily Dallas crime counts with `Crude_Oil_Prices.csv`, splits days into High/Low oil price at the median, removes outliers via IQR, and runs a t-test plus a violin/boxplot visualization.
- `Who Implementation/` — `Who_ Data Cleaning new version.Rmd`: who is involved in crimes (victim type/gender/race/ethnicity)? Mostly data-cleaning (blank → NA, recoding `H`/`NH` race codes) and before/after bar charts.
- `What Implementation/` — only a knitted PDF (`DallasCrimesProject-3.pdf`) is present; no `.rmd` source is checked into this directory.

## Data and secrets

- The primary crime dataset (`Dallas_Crimes_dallas_Website_For_Where.csv`, ~55MB) and `Crude_Oil_Prices.csv` are gitignored and must exist locally for any `.rmd` to knit. Source links are documented inline at the top of `where.rmd` and `why_dataCleaning.rmd` (Dallas Open Data police incidents, and FRED `DCOILWTICO`).
- `.env` (gitignored) holds `CENSUS_API_KEY`, loaded via `dotenv::load_dot_env()` in `where.rmd` for `tidycensus::census_api_key()`. A Census API key is required to knit that file.
- Relative paths to data files differ between scripts depending on where each `.rmd` expects to be run from (e.g. `why.rmd` reads `Crude_Oil_Prices.csv` from its own directory, while `why_dataCleaning.rmd` reads `../Crude_Oil_Prices.csv` from the parent). When editing a script, check its existing `read.csv()` calls rather than assuming the project root.

## Working with the R Markdown files

- Each `.rmd` knits to `pdf_document` with `latex_engine: xelatex` — a LaTeX/xelatex install is required to render PDFs.
- Key R packages used across the project: `dplyr`, `ggplot2`, `lubridate`, `dotenv`, `tidycensus`, `tigris`, `sf`, `ggimage`.
- `where.rmd` fetches live Census/TIGER geometry over the network (`get_acs`, `tigris::zctas`, `tigris::primary_secondary_roads`, `tigris::counties`) — knitting requires internet access and a valid Census API key, and can be slow.
- Highway shield images referenced in `where.rmd` (`highway_shields` data frame) are local PNGs in `Where Implementation/` — file names must match exactly (some have parenthetical suffixes like `I-35E_(TX).png`).
- Each report's narrative/methodology is written in prose directly between code chunks in the `.rmd` — read that prose for the reasoning behind statistical choices (e.g. why Wilcoxon was added alongside the t-test, why IQR-based outlier removal was used) before changing analysis code.
