# Verse Lens (EnglishHaiku)

Verse Lens is a full-stack web app that turns a photo into an AI-generated poem, lets you edit the text, and exports a high-resolution composite image with the poem watermark.

## Features

- Upload image: JPEG, PNG, WebP (max 20MB)
- Optional metadata: season, weather, location, mood
- AI poem generation:
  - Auto-selects style: `haiku` or `imagist`
  - Returns poem, style, and rationale
- Editable poem before export
- Client-side watermark rendering via Canvas (original file remains untouched)
- Download composite image in original format and resolution
- PWA-ready frontend

## Tech Stack

- Backend: FastAPI, Pydantic, OpenAI-compatible SDK, Loguru
- Frontend: Vue 3, Vite, Tailwind CSS, vite-plugin-pwa
- AI endpoint: Alibaba Bailian OpenAI-compatible API (`qwen3.5-plus`)

## Project Structure

```text
EnglishHaiku/
├── src/verse_lens/
│   ├── api.py
│   ├── models.py
│   ├── prompts.py
│   └── services.py
├── frontend/
│   ├── src/
│   │   ├── App.vue
│   │   ├── components/
│   │   └── composables/
│   ├── vite.config.js
│   └── package.json
├── prd.md
├── technical_architecture.md
└── pyproject.toml
```

## Prerequisites

- Python 3.11+
- [uv](https://docs.astral.sh/uv/)
- Node.js 18+
- npm 10+

## Local Development

### 1. Start Backend

```bash
cd /Users/weizhang/Aimee/cs/projects/EnglishHaiku
uv sync
uv run uvicorn verse_lens.api:app --app-dir src --reload --host 127.0.0.1 --port 8000
```

Backend base URL: `http://127.0.0.1:8000`

### 2. Start Frontend

```bash
cd /Users/weizhang/Aimee/cs/projects/EnglishHaiku/frontend
npm install --cache .npm-cache
npm run dev -- --host 127.0.0.1 --port 5173
```

Frontend URL: `http://127.0.0.1:5173`

`/api` is proxied to `http://127.0.0.1:8000` in `frontend/vite.config.js`.

## API

### Health Check

`GET /api/health`

Response:

```json
{ "status": "ok" }
```

### Generate Poem

`POST /api/generate`

Headers:

```text
Content-Type: application/json
X-API-Key: <your_bailian_api_key>
```

Request body:

```json
{
  "image_base64": "data:image/jpeg;base64,...",
  "metadata": {
    "season": "Late Autumn",
    "weather": "Misty, after rain",
    "location": "Scottish Highlands",
    "mood": "Quiet melancholy"
  }
}
```

Response body:

```json
{
  "poem_title": "After Rain",
  "poem_text": "Granite veins streaked wet...\n...",
  "style": "haiku",
  "rationale": "The scene is sparse and quiet, centered on a single moment."
}
```

## Privacy and Security

- API key is passed per request (`X-API-Key`) and not persisted
- Uploaded image is not saved by backend
- Watermark compositing is done in the browser

## Troubleshooting

- Port `8000` already in use:
  - Start backend on another port, for example `8001`
  - Update Vite proxy (`frontend/vite.config.js`) to the same port
- npm cache permission issue:
  - Use local cache install: `npm install --cache .npm-cache`

## License

This project is licensed under the GNU General Public License v3.0. See `LICENSE`.
