# 🎧 Spotify Insights — Personal Music Analytics

[![Tool - Python](https://img.shields.io/badge/Tool-Python-blue)]() [![Notebook - Jupyter](https://img.shields.io/badge/Notebook-Jupyter-orange)]() [![API - Spotify](https://img.shields.io/badge/API-Spotify-green)]()

A reproducible analytics project that extracts, transforms, and visualizes Spotify listening data to surface personal and cohort-level music insights. The repository includes code and notebooks for data extraction (Spotify Web API), feature engineering (audio features, listening sessions), exploratory analysis, and dashboard-ready outputs.

---

## Table of Contents
- About
- Project Goal
- Business / Personal Context
- Data Sources
- Data Collection & Cleaning
- Analyses & Features
- Visualizations & Dashboards
- Tools & Technologies
- Key Insights (examples)
- Recommendations & Actions
- How to run / reproduce
- Limitations & Privacy
- Contributing
- Contact
- Screenshot

---

## About
Spotify Insights analyzes listening behavior and track-level audio features to answer questions like: What artists and tracks dominated my listening? How did my taste change over time? Which audio features (danceability, energy, valence) correlate with my most-played tracks? This repo contains scripts, notebooks, and export-ready data for building visual dashboards and reports.

---

## Project Goal
Build a reproducible analytics workflow that:
- Extracts personal or sample Spotify listening data
- Creates a cleaned, analysis-ready dataset
- Produces exploratory analyses and visualizations
- Packages outputs for dashboards (Jupyter/Plotly/Power BI/Streamlit)

---

## Personal / Business Context
This repo is suitable for:
- Personal listening analytics and self-reflection
- Demonstrating music-data skills for portfolio or interviews
- Building dashboards for small user groups (e.g., classroom or club)
Not intended for large-scale production analytics without adapting rate-limiting, auth, and storage.

---

## Data Sources
- Spotify Web API (OAuth) — user listening history, playlists, audio features
- Optional user-exported CSVs (e.g., "StreamingHistory*.json/CSV") for extended history
- Sample dataset included: data/sample_tracks.csv (if present)

Typical data fields:
- Track: id, name, artists, album, release_date, duration_ms
- Listening: timestamp, device, context (playlist/album)
- Audio features: danceability, energy, tempo, valence, loudness, acousticness, instrumentalness

---

## Data Collection & Cleaning (highlights)
- Authentication using Spotify OAuth (client_id, client_secret, redirect URI)
- Rate-limit-aware extraction (batching and retry logic)
- Normalized track and artist naming; expanded artist arrays to separate rows for artist-level analysis
- Timezone-aware parsing of listening timestamps; sessionization where needed
- Merged audio features to listening records by track ID
- Derived columns: listening_date, listening_hour, session_id, track_play_count

---

## Analyses & Derived Features
- Top tracks, artists, and albums (weekly/monthly/yearly)
- Listening time distribution by hour / weekday
- Churn & diversity metrics (unique artists per period, Simpson/HHI)
- Audio-features profiling: distributions and trendlines for danceability, energy, valence, tempo
- Similarity & recommendation experiments using audio-feature clustering
- Playlist and context analysis (which playlists drive the most listening)
- Mood / activity inference from audio features (e.g., high energy + tempo → workout)

---

## Visualizations & Dashboards
- Jupyter notebooks with Matplotlib / Seaborn and interactive Plotly charts
- Optional Streamlit app for interactive exploration (streamlit/app.py)
- Power BI-ready CSV exports in exports/ for dashboards (if you prefer Power BI)
- Example visuals:
  - Time-series of weekly listening minutes
  - Radar charts for audio-feature signatures of top artists
  - Sankey diagrams for source-to-playlist flows

---

## Tools & Technologies
- Data extraction: spotipy (Spotify Web API client) or direct requests
- Data processing: Python, pandas, numpy
- Visualization: matplotlib, seaborn, plotly, altair
- Optional: Streamlit, Power BI (for .pbix dashboard imports)
- Environment: Python 3.9+ recommended

---

## Key Insights (example summaries)
- Peak listening hours: evenings (19:00–23:00)
- Top artist(s): Artist A, Artist B (X% of plays)
- Audio-feature trend: average valence increased over the past year (more upbeat tracks)
- Playlist behavior: curated playlists account for 60% of discovery plays
(Actual insights will vary based on your dataset.)

---

## Recommendations & Actions
- Create weekly playlist of top 30 tracks to surface favorites
- Use audio-feature clusters to build mood-based playlists (Workout, Chill, Focus)
- Track long-term trends in valence/energy to understand shifts in mood/taste
- Export curated segments (e.g., “Top 2025 Tracks”) for sharing or archiving

---

## How to run / reproduce (quickstart)
1. Clone the repo:
   git clone https://github.com/Sandeep-crtl/Spotify-Insights.git
2. Create a virtual environment and install dependencies:
   python -m venv .venv
   source .venv/bin/activate  # Windows: .venv\Scripts\activate
   pip install -r requirements.txt
3. Register a Spotify app for API credentials:
   - https://developer.spotify.com/dashboard/
   - Set REDIRECT_URI and add it to your app settings
4. Configure credentials:
   - Create a .env file with SPOTIPY_CLIENT_ID, SPOTIPY_CLIENT_SECRET, SPOTIPY_REDIRECT_URI (or place in environment)
5. Run the extraction notebook or script:
   - notebooks/01_extract_spotify_data.ipynb
   - Or: python scripts/extract_spotify_data.py --output data/raw/
6. Run cleaning & analysis:
   - notebooks/02_clean_and_features.ipynb
   - notebooks/03_exploratory_visuals.ipynb
7. Optional: Launch Streamlit dashboard:
   streamlit run streamlit/app.py

Notes:
- Sample data may be included in data/sample/ for offline runs
- API limits require periodic sleeps for large exports

---

## Limitations & Data Privacy
- Listening history is personal; do not publish or share without consent
- This repo may exclude very long-term history due to API export limits
- Rate limits and token expiration may interrupt large extractions — implement retries for production use
- Some analyses are exploratory and should be validated statistically before strong conclusions

---

## Contributing
Contributions, issues, and feature requests are welcome:
- Open an issue describing the idea or bug
- Fork, create a branch, and submit a pull request for code changes
- Add dataset examples under data/sample/ (no private user exports)

---

## Contact
- Maintainer: Sandeep-crtl
- Project: Spotify Insights (personal / demo analytics)

---

## Screenshot
![App Screenshot](https://github.com/Sandeep-crtl/Spotify-Insights/blob/main/Spotify-Insights.png)
