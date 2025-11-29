# VidSynth – YouTube Summarizer (Chrome Extension)

VidSynth is a Chrome extension that summarizes a YouTube video **and** its comments by calling a local backend API. It’s designed to be run on your own machine while your summarization pipeline is running on `localhost`.

---

## Features

- One-click summarization from the extension popup.
- Works on YouTube video pages (URLs containing `?v=...`).
- Sends the current video’s ID to a local API endpoint.
- Displays both:
  - **Video Summary**
  - **Comments Summary**

---

## How It Works

### 1. Manifest & Content Script

- `manifest.json` registers a content script (`content.js`) for YouTube URLs and a popup (`popup.html`).
- The content script:
  - Reads the video ID from the current YouTube URL (`?v=...`).
  - Listens for messages from the popup and responds with the video ID.

### 2. Popup UI & Logic

- `popup.html` defines a simple UI:
  - Title: **VidSynth**
  - Button: **“Summarize Video & Comments”**
  - Result area where the summaries are rendered.

- `popup.js`:
  - When the button is clicked:
    1. Gets the active tab (assumed to be a YouTube video).
    2. Sends a message to the content script asking for the video ID.
    3. Calls your backend at `http://127.0.0.1:8000/summarize` with:
       ```json
       {
         "video_id": "<YOUTUBE_VIDEO_ID>"
       }
       ```
    4. Expects a JSON response like:
       ```json
       {
         "video_summary": "…",
         "comments_summary": "…"
       }
       ```
    5. Renders the returned summaries in the popup.

- Basic error handling is included:
  - If not on a YouTube video page → shows an error.
  - If the content script can’t be reached → shows guidance to open a video page and retry.
  - If the backend is down or unreachable → shows a “Pipeline API not running or unreachable” error.

---

## Project Structure

```text
.
├── manifest.json    # Chrome extension manifest (v3)
├── popup.html       # Popup UI
├── popup.js         # Popup logic & API call
└── content.js       # Content script to extract YouTube video ID
