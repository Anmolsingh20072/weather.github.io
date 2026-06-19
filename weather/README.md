# Mini Weather Data Analyzer

A small toolkit that loads a daily weather CSV, cleans it with **Pandas**,
aggregates it with **NumPy**, and plots monthly trends with **Matplotlib**.

## Files

- `weather_analyzer.py` — the main script (load → clean → aggregate → plot)
- `sample_weather_data.csv` — synthetic 2-year demo dataset (intentionally
  messy: missing values, duplicates, out-of-range sensor errors, inconsistent
  text) so you can see the cleaning step do real work
- `generate_sample_data.py` — regenerates the sample CSV if you want a fresh
  (or differently-seeded) demo dataset
- `monthly_summary.csv` — output: tidy monthly aggregate stats
- `monthly_trends.png` — output: 4-panel chart (temperature, precipitation,
  humidity, wind speed)
- `weather_dashboard.html` — a self-contained static webpage version of the
  same monthly data (see below)
- `build_dashboard.py` — regenerates `weather_dashboard.html` from a new
  `monthly_summary.csv`

## The static webpage

Open `weather_dashboard.html` directly in any browser — no server needed.
It shows:

- a "year strip" of every month colored by mean temperature (hover or tap a
  swatch for that month's stats)
- headline stats (hottest / coldest / wettest / windiest month)
- interactive charts for temperature, precipitation, humidity, and wind speed

It needs internet access once, to load its font and charting library from a
CDN (Google Fonts + Chart.js) — after that it's just a static file you can
host anywhere or email to someone.

To rebuild it after re-running the analyzer on new data:

```bash
python3 build_dashboard.py monthly_summary.csv sample_weather_data.csv weather_dashboard.html
```

## Using your own data

Run it against any CSV with this shape:

```bash
python3 weather_analyzer.py path/to/your_weather_data.csv
```

Expected columns (case-insensitive, any order):

| column              | meaning                          |
|---------------------|-----------------------------------|
| `date`              | any parseable date string         |
| `temperature_c`     | daily temperature, Celsius        |
| `humidity_pct`      | daily humidity, percent           |
| `precipitation_mm`  | daily precipitation, mm           |
| `wind_kph`          | daily wind speed, km/h            |
| `city` (optional)   | location label                    |

If your columns use different names or units, the easiest fix is to rename/
convert them in your CSV (or add a couple of lines to `load_data()`) — the
script doesn't otherwise care where the data came from.

## What the cleaning step does

- Parses `date`, drops rows where it fails
- Drops exact duplicate rows
- Trims/title-cases the `city` text field
- Flags physically impossible readings (e.g. -99°C, 999% humidity) as missing
  rather than trusting them
- Fills remaining gaps via linear interpolation over time, so the monthly
  aggregates aren't skewed by sparse missing data

## What gets aggregated (NumPy)

For each calendar month: mean / min / max / std for temperature, humidity,
and wind speed, plus total precipitation. All computed with `numpy.mean`,
`numpy.std`, etc. on the cleaned arrays.

## Re-running on fresh sample data

```bash
python3 generate_sample_data.py   # writes a new sample_weather_data.csv
python3 weather_analyzer.py
```
