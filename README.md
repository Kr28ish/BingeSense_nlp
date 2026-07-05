# BingeSense

BingeSense is a movie discovery and recommendation app built with FastAPI and Streamlit. It combines live TMDb data with a local TF-IDF recommendation model so users can browse movies, open detailed pages, and explore similar titles quickly.

## Features

- Browse trending, popular, top-rated, now playing, and upcoming movies
- Search movies by title with suggestion dropdown support
- Open a dedicated movie details view with poster, backdrop, release date, genres, and overview
- Get similar-movie recommendations from a local TF-IDF model
- Fall back to genre-based recommendations from TMDb when needed
- Use a lightweight single-page Streamlit UI backed by FastAPI endpoints

## Tech Stack

- Frontend: Streamlit
- Backend: FastAPI
- Data layer: pandas, NumPy, SciPy, scikit-learn
- External API: TMDb
- Local assets: `df.pkl`, `indices.pkl`, `tfidf.pkl`, `tfidf_matrix.pkl`

## Project Structure

```text
nlp/
|-- app.py
|-- main.py
|-- requirements.txt
|-- README.md
|-- .env
|-- movies.ipynb
|-- movies_metadata.csv
|-- df.pkl
|-- indices.pkl
|-- tfidf.pkl
`-- tfidf_matrix.pkl
```

## How It Works

### Home feed

The Streamlit app calls the `/home` endpoint and displays movie cards from one of these categories:

- `trending`
- `popular`
- `top_rated`
- `now_playing`
- `upcoming`

### Search flow

When a user types a title:

1. Streamlit calls `/tmdb/search`
2. The backend forwards the request to TMDb
3. Matching results are shown as suggestions and poster cards

### Recommendation flow

When a movie is opened:

1. Streamlit fetches `/movie/id/{tmdb_id}` for full details
2. It then calls `/movie/search`
3. The backend tries TF-IDF recommendations from local assets
4. Genre recommendations from TMDb are also returned as a fallback or complement

## API Endpoints

- `GET /health` - health check
- `GET /home` - home feed cards by category
- `GET /tmdb/search` - TMDb search results
- `GET /movie/id/{tmdb_id}` - detailed movie information
- `GET /recommend/genre` - genre-based recommendations
- `GET /recommend/tfidf` - TF-IDF recommendations by title
- `GET /movie/search` - bundled movie details and recommendations

## Setup

### Requirements

- Python 3.11+
- A TMDb API key

### 1. Create a virtual environment

```powershell
cd C:\Users\krish\OneDrive\Desktop\nlp
python -m venv .venv
.venv\Scripts\Activate.ps1
```

### 2. Install dependencies

```powershell
pip install -r requirements.txt
```

### 3. Configure environment variables

Create or update `.env`:

```env
TMDB_API_KEY=your_tmdb_api_key_here
API_BASE_URL=http://127.0.0.1:8000
```

`API_BASE_URL` is optional for Streamlit, but setting it makes local development much cleaner.

## Run The Project

Start the backend in one terminal:

```powershell
.venv\Scripts\Activate.ps1
uvicorn main:app --reload
```

Start the frontend in another terminal:

```powershell
.venv\Scripts\Activate.ps1
streamlit run app.py
```

Default local URLs:

- Backend: `http://127.0.0.1:8000`
- Frontend: `http://localhost:8501`

## Notes

- The TF-IDF pickle files must be present for local recommendation results to work
- If TMDb is unavailable, TMDb-powered routes will return upstream errors
- If a title is not found in the local dataset, the app can still fall back to genre recommendations
