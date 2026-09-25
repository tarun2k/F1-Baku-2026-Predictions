<p align="center">
  <img src="assets/cover.png" alt="Baku — before lights out" width="100%">
</p>

<p align="center">
  <a href="baku_2026.ipynb"><b>Open the executed notebook</b></a>
</p>

## The idea

Predict the 2026 Azerbaijan Grand Prix finishing order using practice, qualifying and
recent driver and constructor form. Everything runs in one notebook: FastF1 extraction,
workbook and weather loading, exploration, features, model comparison and the final forecast.

**22 drivers · 166 training races · 84 validation races**

The forecast is frozen at **25 September 2026, 14:43 UTC**. The starting grid is provisional.

## The prediction

<img src="assets/forecast.png" alt="Baku finishing-order prediction and uncertainty intervals" width="900">

Russell, Leclerc and Hadjar lead the predicted order. Antonelli moves from P16 to P8;
Norris stays P5. The intervals are deliberately broad.

## How well does it work?

Average finishing-position error; lower is better.

| Period | XGBoost + FP2 | Starting-grid baseline |
|---|---:|---:|
| 2023–2024 | 3.085 | 3.306 |
| 2025 | 3.303 | 3.344 |
| 2026 before Baku | 3.429 | 3.403 |

Every validation race uses earlier training races only. The model improves on the grid
in development, but does not beat it consistently in later periods.

## Experiments I tried

These earlier experiments are summarized here; the notebook runs the main modeling workflow.

| Experiment | Result |
|---|---|
| Expected qualifying versus the actual grid | Flagged displaced drivers, but did not reliably improve race predictions. |
| More weight on recent races | Neither a 20-race half-life nor a 60-race window earned a change. |
| Twice the weight for the same circuit | Negligible earlier gains; 2026 MAE worsened from 3.429 to 3.506. |

I kept the original model. These were retrospective comparisons using already examined
seasons; the qualifying experiment used a smaller matched set of races.

## Run it

The notebook already contains its tables and plots. To rerun locally, install
`requirements.txt`, open `baku_2026.ipynb` in Jupyter or VS Code, and run all cells.

In Colab, upload the notebook, then the workbook, grid CSV and weather JSON when prompted.
If setup installs packages, restart the kernel once and run all cells again. The first
FastF1 download can take a while.

```text
baku_2026.ipynb              Extraction through prediction
requirements.txt            Python dependencies
data/weekend.xlsx           Practice and qualifying workbook
data/grid.csv               Provisional grid and penalty sources
data/weather_snapshot.json  Timestamped pre-race weather
assets/                     README graphics
```

FastF1 loads historical results and qualifying, FP2 laps, and previous-race pace/weather.
The workbook supplies the current weekend; the grid CSV applies documented penalties.
The saved run includes 187 historical races. The 2018 season initializes rolling features
and is excluded from model training. Later API amendments may change a future rerun.

Weather is loaded directly from the JSON, checked against the information cutoff and
summarized over the race window. It remains context rather than a model input because
comparable historical pre-race forecasts are unavailable. Downloads may be reused in a
temporary system folder; no cache files or cache archive are included in this repository.
Fresh exports go to `results/` when the notebook runs.

Probabilities are approximate historical-error simulations. Strategy, incidents and later
grid changes remain unknown. Historical weather only informs later races; a current-weather
model was not supported by comparable pre-race forecast data.

Historical data: [FastF1](https://docs.fastf1.dev/). Grid sources are retained in
[`grid.csv`](data/grid.csv), and weather provenance in
[`weather_snapshot.json`](data/weather_snapshot.json). Executed locally with Python 3.10;
hosted Colab has not been directly verified.
