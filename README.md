# Currency Control Chart

An interactive dashboard that shows how any currency in the workbook behaves against the IMF's Special Drawing Rights (SDR) and how it compares with ASEAN and world currencies. It opens on the Indonesian rupiah (IDR) and you can pick any other currency. It uses an x̄ control chart and runs from a single `index.html` file, in the browser, with no server.

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

Online: use the GitHub Pages link (https://febrianggi.github.io/currency-control-charts).

Offline: download `index.html` and open it in a browser. Chart.js, SheetJS, the fonts and a copy of the data are all inside the file, so it needs no internet connection.

## Connect it to Excel

The dashboard reads the workbook sheets directly and calculates the standardized values, limits, ranks and conclusions in the page. A copy of the workbook is built in, so the page works without choosing a file.

To use your own workbook:

1. Scroll to the **Excel source and notes** section.
2. Choose an `.xlsx` file, or drop it on the drop area.
3. Everything on the page updates. If the file cannot be read, the page tells you why and keeps the previous data.

A web page cannot watch a file on your disk by itself. In Chrome and Edge, the **Refresh from the same file** button re-reads the file you picked, so you can edit and save in Excel and then refresh. In other browsers, choose the file again after saving.

Nothing is uploaded anywhere. The file is read inside your browser.

### Workbook Layout

The workbook used for the dashboard is in `data.xlsx`.

## Run it in Google Colab

`currency_control_chart.ipynb` repeats the main calculations in Python, one step per cell: z-scores, the control chart of the selected currency, the world control chart for the latest month and the yearly position of two currencies. Open it in Colab, run the cells in order and upload the workbook when the first cell asks for it. Change `CODE`, `VS` and `L` in the second cell to pick the currencies and the sigma multiplier.

## Download the data yourself

`fetch_xe_data.ipynb` collects the rates from the source in this dashboard, the XE Currency Exchange Rate Tables with SDR (XDR) as the base currency: https://www.xe.com/currencytables/?from=XDR. It requests the table for each month end between `START` and `END`, keeps the "Units per XDR" column, and writes `data.xlsx` with a single sheet named `Global Data` (dates in column A, currency codes in row 1). Load that file in the dashboard or in the analysis notebook.

- One sheet is enough. The dashboard takes the IDR series and the ten ASEAN currencies from `Global Data` and computes the limits itself. The sheets `Data IDR`, `Data ASEAN`, `IDR VS ASEAN` and `IDR VS Global` are optional, and the world sheet can also be called `Data Dunia`.
- Check XE's terms of use before you scrape at scale. The notebook waits between requests, but the site can still change its layout or block automated access. If a request fails, download the tables by hand and paste them into the workbook.

## Adding newer months

Append new rows at the bottom of `Global Data` (and of `Data IDR` and `Data ASEAN` if your workbook has them) with the month end date and the rates, and keep the existing columns. Then load the workbook in the dashboard or in the notebook.

- Charts, tables, ranks and the latest month update from the new rows.
- A year shows up in the yearly view once it has all 12 months.
- The world and ASEAN limits are read from `IDR VS Global` and `IDR VS ASEAN` (columns G to I, row 2). Let Excel recalculate those cells before you save. The notebook always computes its own limits.

## Files

```
index.html                      the whole dashboard
currency_control_chart.ipynb    Google Colab notebook with the same calculations
fetch_xe_data.ipynb             Colab notebook that downloads the monthly rates from XE
README.md                       this file
data.xlsx                the source workbook
```

## Technical notes

- Plain HTML, CSS and JavaScript in one file. No build step is needed to use it.
- Charts use [Chart.js](https://www.chartjs.org/) 4.4.1 and Excel parsing uses [SheetJS](https://sheetjs.com/) 0.18.5 (mini build). Both are inlined.
- Standardization uses the sample standard deviation over all months in the sheet. The control limits for the world and ASEAN comparisons are read from the `IDR VS Global` and `IDR VS ASEAN` sheets and applied to every period.
- The world set holds 171 codes: 159 national currencies, 4 precious metals and 8 crypto assets. Ranks cover national currencies only, and the "Currencies only" switch hides the other codes from charts and tables.
- All charts are control charts: a center line, upper and lower control limits, and out-of-control points marked in red.
- The light or dark mode follows your system setting at first and can be toggled. It is not stored between visits.
