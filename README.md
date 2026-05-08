# Wine Weather Data Acquisition Pipeline

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

## Overview

This pipeline enriches Vivino wine rating data with historical growing-season weather data from the Open-Meteo Archive API.

## Features

-  Geocodes wine regions using Open-Meteo Geocoding API (with country filtering)
-  Fetches daily temperature, precipitation, and humidity from 1940–present
-  Calculates growing-season aggregates (Apr–Oct Northern, Oct–Apr Southern)
-  Handles API rate limits with exponential backoff
-  Checkpointing for resumable runs
-  Hemisphere-aware date logic

## Data Sources

| Source | Description | License |
|--------|-------------|---------|
| [Vivino Wine Dataset](https://www.kaggle.com/datasets/joshuakalobbowles/vivino-wine-data-top-10-countries-exchina) | 12k+ wines, ratings, prices, regions | Kaggle terms |
| [Open-Meteo Archive API](https://open-meteo.com/) | Historical weather data (1940–present) | **CC BY 4.0** |

## Output

The pipeline produces `vivino_top_ten_with_region_weather_filled.csv` with these additional columns:

| Column | Description |
|--------|-------------|
| `growing_season_avg_temp_c` | Mean temperature during growing season |
| `growing_season_total_precip_mm` | Total precipitation during growing season |
| `growing_season_avg_humidity_percent` | Mean relative humidity during growing season |
| `Latitude` / `Longitude` | Geocoded region coordinates |
| `Hemisphere` | Northern or Southern (derived from country) |

## Rate Limit Compliance

Open-Meteo free tier allows **10,000 requests/day**. This pipeline:
- Implements 1.5s delays between requests
- Uses exponential backoff (2^attempt seconds) on 429 errors
- Caches results locally with checkpoints to avoid re-fetching

## Requirements

```bash
pip install -r requirements.txt
