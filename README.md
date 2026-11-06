# Currency Control Chart

An interactive dashboard that shows how any currency in the workbook behaves against the IMF's Special Drawing Rights (SDR) and how it compares with ASEAN and world currencies. It opens on the Indonesian rupiah (IDR) and you can pick any other currency. It uses an x̄ control chart and runs from a single `index.html` file, in the browser, with no server.

Live page: `https://YOUR-USERNAME.github.io/YOUR-REPO/` (replace after you enable GitHub Pages, see below)

## What it shows

| Section | Content |
|---|---|
| Overview | A summary of the latest month for the selected currency, a meter showing where it sits between the limits, and its per SDR control chart with the center line, upper and lower control limits, out-of-control months, a date range and optional recomputed limits |
| Selected vs world | A control chart across every currency in the workbook (each point is a z-score), with the control limits and the currencies that fall outside them |
| Selected vs ASEAN | The same control chart for the ten ASEAN currencies. This section appears only when the selected currency is an ASEAN member |
| Country view | A written conclusion for the selected currency, its monthly z-score control chart, and its yearly position against a second currency chosen in Compare with (IDR by default) |
| Data tables | Selected currency, world ranking and selected-vs-compared tables with a date range, search, status filter, sorting and CSV download |
| Excel source and notes | Load your own workbook, see which sheets were found, and read the method notes |

The page is one scrolling page with a light and dark mode. Every chart and sentence updates when you change the currency or the period at the top.

## How to read it

The rate is units of the selected currency per one SDR, so a higher value means a weaker currency. The center line is the mean, and the control limits sit two sample standard deviations either side. A point beyond a limit means the currency moved further from its own average than normal variation explains.

For comparisons, each currency is standardized against its own monthly history (z-score). A positive z-score means the currency is weaker than its own average, and a z-score beyond the limits means it is out of control. Yearly values are the average of that year's monthly z-scores.

## Open it

Online: use the GitHub Pages link above.

Offline: download `index.html` and open it in a browser. Chart.js, SheetJS, the fonts and a copy of the data are all inside the file, so it needs no internet connection.

## Connect it to Excel

The dashboard reads the workbook sheets directly and calculates the standardized values, limits, ranks and conclusions in the page. A copy of the workbook is built in, so the page works without choosing a file.

To use your own workbook:

1. Scroll to the **Excel source and notes** section.
2. Choose an `.xlsx` file, or drop it on the drop area.
3. Everything on the page updates. If the file cannot be read, the page tells you why and keeps the previous data.

A web page cannot watch a file on your disk by itself. In Chrome and Edge, the **Refresh from the same file** button re-reads the file you picked, so you can edit and save in Excel and then refresh. In other browsers, choose the file again after saving.

Nothing is uploaded anywhere. The file is read inside your browser.

### Workbook layout

| Sheet | Required | Layout |
|---|---|---|
| `Data IDR` | Yes | Row 1 is a header. Column A is the month end date, column B is IDR per SDR. Optional: `L` in F2 and its value in G2 (default 2) |
| `Data ASEAN` | Yes | Row 2 holds the currency codes from column B. Raw values start in row 3, with dates in column A |
| `Data Dunia` | Yes | Row 1 holds the currency codes from column B, up to the first empty cell. Raw values start in row 2, with dates in column A |
| `IDR VS ASEAN` | No | CL, UCL and LCL in columns G to I of row 2. If missing, limits are computed |
| `IDR VS Global` | No | CL, UCL and LCL in columns G to I of row 2. If missing, limits are computed |

Dates can be text (`2026-04-30`) or real Excel dates. Add a new month at the bottom of each sheet and it appears on every chart. Years with all 12 months are used for the yearly averages.

The workbook used for the paper is in `data/QC_FIX.xlsx`.

## Publish it with GitHub Pages

The page is a static site, so GitHub can host it directly. The link opens the dashboard, not the repository.

1. Create a repository and upload `index.html`, `README.md`, `.nojekyll` and the `data` folder to the root.
2. Open **Settings**, then **Pages**.
3. Under **Build and deployment**, set **Source** to **Deploy from a branch**, choose the `main` branch and the `/ (root)` folder, then save.
4. After a minute or two the site is live at `https://YOUR-USERNAME.github.io/YOUR-REPO/`.

To use the short address `https://YOUR-USERNAME.github.io/`, name the repository `YOUR-USERNAME.github.io`.

## Run it in Google Colab

`currency_control_chart.ipynb` repeats the main calculations in Python, one step per cell: z-scores, the control chart of the selected currency, the world control chart for the latest month and the yearly position of two currencies. Open it in Colab, run the cells in order and upload the workbook when the first cell asks for it. Change `CODE`, `VS` and `L` in the second cell to pick the currencies and the sigma multiplier.

The same code is also in `currency_control_chart.py` as plain cells.

## Adding newer months

Append new rows at the bottom of `Data IDR`, `Data ASEAN` and `Data Dunia` with the month end date and the rates, and keep the existing columns. Then load the workbook in the dashboard or in the notebook.

- Charts, tables, ranks and the latest month update from the new rows.
- A year shows up in the yearly view once it has all 12 months.
- The world and ASEAN limits are read from `IDR VS Global` and `IDR VS ASEAN` (columns G to I, row 2). Let Excel recalculate those cells before you save. The notebook always computes its own limits.

## Files

```
index.html                      the whole dashboard (code, libraries, fonts and built-in data)
currency_control_chart.ipynb    Google Colab notebook with the same calculations
currency_control_chart.py       the notebook code as a plain script
README.md                       this file
.nojekyll                       tells GitHub Pages to serve the files as they are
data/QC_FIX.xlsx                the source workbook
```

## Technical notes

- Plain HTML, CSS and JavaScript in one file. No build step is needed to use it.
- Charts use [Chart.js](https://www.chartjs.org/) 4.4.1 and Excel parsing uses [SheetJS](https://sheetjs.com/) 0.18.5 (mini build). Both are inlined.
- Standardization uses the sample standard deviation over all months in the sheet. The control limits for the world and ASEAN comparisons are read from the `IDR VS Global` and `IDR VS ASEAN` sheets and applied to every period.
- The world set holds 171 codes: 159 national currencies, 4 precious metals and 8 crypto assets. Ranks cover national currencies only, and the "Currencies only" switch hides the other codes from charts and tables.
- All charts are control charts: a center line, upper and lower control limits, and out-of-control points marked in red.
- The light or dark mode follows your system setting at first and can be toggled. It is not stored between visits.

## Data notes

- The yearly values for 2022 to 2025 and the latest month come straight from the workbook. For the rupiah, the 2025 average z-score is +1.21, inside the world limits of ±1.98, and the latest month (April 2026) is +2.52, above them.
- If the paper text and the workbook disagree, the dashboard shows what the workbook contains.

## Source

The dashboard accompanies a research paper on the performance of the rupiah against world currencies using an x̄ control chart.
