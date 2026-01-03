# Spotify Insights — Power BI Report

Overview

Spotify Insights is a Power BI report that analyzes Spotify Top-50 chart data (example table: Top-50-world). The report provides track- and artist-level KPIs, popularity and duration statistics, explicit-content analysis, chart-position insights, album-type breakdowns, and year-over-year trends.

This repository contains guidance, sample dataset structure, and DAX measures to build the report in Power BI Desktop.

Contents

- README.md (this file)
- /data/Top-50-world.csv (suggested sample dataset)
- /reports/Spotify-Insights.pbix (Power BI Desktop file — optional)
- /assets/screenshots/ (optional report screenshots)

Prerequisites

- Power BI Desktop (latest stable release)
- (Optional) Power BI Pro or Premium to publish and share
- (Optional) DAX Studio or Tabular Editor for advanced measure management
- A dataset (CSV, JSON or API export) containing Top-50 chart rows

Required dataset columns

The report and measures expect a table named Top-50-world (or import the CSV and rename the table). The following columns are required (recommended exact names):

- song (text) — track title
- artist (text) — primary artist name
- popularity (numeric / whole number) — Spotify popularity score (0–100)
- duration_ms (numeric) — duration in milliseconds
- is_explicit (boolean or text) — explicit flag (TRUE/FALSE or 0/1 or "True"/"False")
- position (numeric / whole number) — chart position (1..50)
- total_tracks (numeric) — total tracks on the album (if available)
- album_type (text) — e.g., "single", "album", "compilation"
- year (numeric / whole number) — release or chart year (optional, used for time-scoped measures)

Notes:
- If your source uses different column names, map/rename them in Power Query to the above names.
- If is_explicit is text or numbers, convert to boolean in Power Query or within the model.

Data import and preparation (Power BI Desktop)

1. Get Data → CSV (or JSON / Web / Spotify API export).
2. Open Power Query Editor:
   - Rename the table to Top-50-world.
   - Convert data types:
     - song, artist, album_type → Text
     - popularity, position, total_tracks, year → Whole Number
     - duration_ms → Whole Number
     - is_explicit → True/False (or transform "0"/"1" to false/true)
   - Remove duplicates or unwanted rows if needed.
   - (Optional) Add a calculated column DurationMinutes = duration_ms / 60000 for debugging — the measures use duration_ms directly.
3. Close & Apply.

Data model

- For a simple report, a single table (Top-50-world) is sufficient.
- If you have lookup tables (Artist, Album, Date), create relationships to enable richer visuals and time intelligence:
  - Top-50-world[artist] → Artist[artist]
  - Top-50-world[year] → Date[year] or Date[Date] if you have a full date table
- Add and mark a Date table as the model Date table if you want to use built-in time intelligence.

Measures (DAX)

Create a measure table (example name: Measures) or add the measures to the Top-50-world table. Create each measure individually in Power BI Desktop. The DAX definitions used in this project are below. You can copy them into Power BI or run the final EVALUATE query in DAX Studio connected to the model.

--- DAX measures ---

DEFINE
    MEASURE 'Top-50-world'[Total Songs] = COUNTROWS('Top-50-world')
    MEASURE 'Top-50-world'[Distinct Songs] = DISTINCTCOUNT('Top-50-world'[song])
    MEASURE 'Top-50-world'[Distinct Artists] = DISTINCTCOUNT('Top-50-world'[artist])
    MEASURE 'Top-50-world'[Avg Popularity] = AVERAGE('Top-50-world'[popularity])
    MEASURE 'Top-50-world'[Max Popularity] = MAX('Top-50-world'[popularity])
    MEASURE 'Top-50-world'[Min Popularity] = MIN('Top-50-world'[popularity])

    MEASURE 'Top-50-world'[Avg Duration Minutes] = AVERAGE('Top-50-world'[duration_ms]) / 60000
    MEASURE 'Top-50-world'[Max Duration Minutes] = MAX('Top-50-world'[duration_ms]) / 60000
    MEASURE 'Top-50-world'[Min Duration Minutes] = MIN('Top-50-world'[duration_ms]) / 60000

    MEASURE 'Top-50-world'[Explicit Songs] = CALCULATE(COUNTROWS('Top-50-world'), 'Top-50-world'[is_explicit] = TRUE())
    MEASURE 'Top-50-world'[Non-Explicit Songs] = CALCULATE(COUNTROWS('Top-50-world'), 'Top-50-world'[is_explicit] = FALSE())
    MEASURE 'Top-50-world'[Pct Explicit Songs] = DIVIDE([Explicit Songs], [Total Songs], 0)
    MEASURE 'Top-50-world'[Avg Popularity Explicit] = CALCULATE(AVERAGE('Top-50-world'[popularity]), 'Top-50-world'[is_explicit] = TRUE())
    MEASURE 'Top-50-world'[Avg Popularity NonExplicit] = CALCULATE(AVERAGE('Top-50-world'[popularity]), 'Top-50-world'[is_explicit] = FALSE())

    MEASURE 'Top-50-world'[Avg Position] = AVERAGE('Top-50-world'[position])
    MEASURE 'Top-50-world'[Position 1 Songs] = CALCULATE(COUNTROWS('Top-50-world'), 'Top-50-world'[position] = 1)
    MEASURE 'Top-50-world'[Position 1 Artists] = CALCULATE(DISTINCTCOUNT('Top-50-world'[artist]), 'Top-50-world'[position] = 1)

    MEASURE 'Top-50-world'[Avg Tracks per Album] = AVERAGE('Top-50-world'[total_tracks])
    MEASURE 'Top-50-world'[Album Type Count] = DISTINCTCOUNT('Top-50-world'[album_type])
    MEASURE 'Top-50-world'[Singles Count] = CALCULATE(COUNTROWS('Top-50-world'), 'Top-50-world'[album_type] = "single")
    MEASURE 'Top-50-world'[Albums Count] = CALCULATE(COUNTROWS('Top-50-world'), 'Top-50-world'[album_type] = "album")

    -- Artist-scoped (use when Artist in context)
    MEASURE 'Top-50-world'[Songs per Artist] = COUNTROWS('Top-50-world')
    MEASURE 'Top-50-world'[Distinct Songs per Artist] = DISTINCTCOUNT('Top-50-world'[song])
    MEASURE 'Top-50-world'[Avg Popularity per Artist] = AVERAGE('Top-50-world'[popularity])
    MEASURE 'Top-50-world'[Position1 Hits per Artist] = CALCULATE(COUNTROWS('Top-50-world'), 'Top-50-world'[position] = 1)

    -- Time-scoped (use when Year in context)
    MEASURE 'Top-50-world'[Songs per Year] = COUNTROWS('Top-50-world')
    MEASURE 'Top-50-world'[Avg Popularity per Year] = AVERAGE('Top-50-world'[popularity])
    MEASURE 'Top-50-world'[Avg Duration per Year] = AVERAGE('Top-50-world'[duration_ms]) / 60000
    MEASURE 'Top-50-world'[Pct Explicit per Year] = DIVIDE(
        CALCULATE(COUNTROWS('Top-50-world'), 'Top-50-world'[is_explicit] = TRUE()),
        [Songs per Year], 
        0
    )

EVALUATE
    SUMMARIZECOLUMNS(
        "Total Songs", [Total Songs],
        "Distinct Songs", [Distinct Songs],
        "Distinct Artists", [Distinct Artists],
        "Avg Popularity", [Avg Popularity],
        "Max Popularity", [Max Popularity],
        "Min Popularity", [Min Popularity],
        "Avg Duration Minutes", [Avg Duration Minutes],
        "Max Duration Minutes", [Max Duration Minutes],
        "Min Duration Minutes", [Min Duration Minutes],
        "Explicit Songs", [Explicit Songs],
        "Non-Explicit Songs", [Non-Explicit Songs],
        "Pct Explicit Songs", [Pct Explicit Songs],
        "Avg Popularity Explicit", [Avg Popularity Explicit],
        "Avg Popularity NonExplicit", [Avg Popularity NonExplicit],
        "Avg Position", [Avg Position],
        "Position 1 Songs", [Position 1 Songs],
        "Position 1 Artists", [Position 1 Artists],
        "Avg Tracks per Album", [Avg Tracks per Album],
        "Album Type Count", [Album Type Count],
        "Singles Count", [Singles Count],
        "Albums Count", [Albums Count],
        "Songs per Artist", [Songs per Artist],
        "Distinct Songs per Artist", [Distinct Songs per Artist],
        "Avg Popularity per Artist", [Avg Popularity per Artist],
        "Position1 Hits per Artist", [Position1 Hits per Artist],
        "Songs per Year", [Songs per Year],
        "Avg Popularity per Year", [Avg Popularity per Year],
        "Avg Duration per Year", [Avg Duration per Year],
        "Pct Explicit per Year", [Pct Explicit per Year]
    )
--- end DAX measures ---

Notes on using the DAX

- In Power BI Desktop, create each measure individually rather than using the DEFINE/EVALUATE script. The DEFINE block is suitable for external tools like DAX Studio or Tabular Editor.
- To run the final EVALUATE SUMMARIZECOLUMNS query, connect DAX Studio to your Power BI model.

Recommended report pages & visuals

1. Overview (Landing)
   - KPI cards: Total Songs, Distinct Artists, Avg Popularity, Avg Duration Minutes, Pct Explicit Songs
   - Donut or stacked bar: Album Type Count / Singles vs Albums
   - Bar chart: Top 10 Songs by Popularity
2. Artists
   - Table: Artist, Songs per Artist, Distinct Songs per Artist, Avg Popularity per Artist, Position1 Hits per Artist
   - Bar chart: Top Artists by Position1 Hits or Avg Popularity
3. Tracks
   - Table with song, artist, popularity, duration (minutes via measure), position, is_explicit
   - Scatter chart: Duration (x) vs Popularity (y)
4. Time Trends
   - Line chart: Avg Popularity per Year and Songs per Year
   - Area chart: Pct Explicit per Year
5. Album & Release
   - Breakdown by album_type and Avg Tracks per Album
6. Top Charts
   - Visuals filtered to top positions (1–10) and trends across years

Slicers & interactions

- Slicers: Year, Album Type, Artist (search), Explicit (True/False), Position (Top N)
- Use bookmarks and buttons for quick navigation (Top 10, Top Artists, Export view)

Performance & model tips

- Disable Auto Date/Time in Power BI options if you use a dedicated date table.
- Use Import mode for snapshots or small datasets. Use DirectQuery/composite models only when necessary.
- Reduce cardinality where possible (trim unused columns).
- Set summarization for columns correctly (do not summarize text fields).

Publishing & refresh

1. Publish: File → Publish → Select workspace in Power BI Service.
2. Dataset settings:
   - If data is in cloud (OneDrive/SharePoint), use the appropriate connector and configure refresh.
   - If on-premises, configure and use an On-premises data gateway.
3. Scheduled refresh: configure credentials and refresh schedule in Power BI Service.
4. Sharing: share report or configure an app; Power BI Pro required for cross-workspace sharing unless using Premium.

Exporting & advanced

- Use DAX Studio to run the EVALUATE query to export summary tables to CSV.
- Use Tabular Editor for bulk measure creation or edits.
- If using the Spotify API, obtain Client ID/Secret from Spotify Developer Dashboard and export data to CSV for Power BI ingestion.

Troubleshooting

- Popularity blanks: ensure popularity is numeric.
- Duration incorrect: confirm duration_ms is numeric and measures divide by 60000.
- is_explicit not filtering: normalize TRUE/FALSE values in Power Query.
- Unexpected measure results: check filter context and relationships.

Contributing

- To add the README automatically, confirm and the maintainer will accept or request changes.
- Suggested contributions: sample data CSV, pbix file, screenshots, example API scripts to fetch Spotify data.

License

- Add a license file (e.g., MIT) if you want to make the project open-source. This repository currently does not include a license file.

Contact / Maintainer

- Repository: https://github.com/Sandeep-crtl/Spotify-Insights
- Maintainer: Sandeep-crtl

