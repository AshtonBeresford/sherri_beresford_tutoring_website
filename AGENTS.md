# Sherri Beresford Tutoring Website — Agent & Developer Guide

## Project Overview

- **Name:** Sherri Beresford Tutoring Website
- **Author:** Ashton Beresford
- **Purpose:** Single-page marketing site for a private tutoring business. Displays pricing, experience/qualifications, and photos.
- **Built with agentic engineering via Cursor.**

---

## Tech Stack

| Layer | Technology |
|---|---|
| Backend | Python 3, Flask |
| WSGI server | Gunicorn (production) |
| Templating | Jinja2 (HTML templates) |
| Styling | Plain CSS (`static/css/style.css`) |
| JavaScript | None — no JS frameworks |
| Database | None — all content is static |
| Hosting | Render.com (Web Service) |
| CI/CD | GitHub → Render auto-deploy on push to `main` |

Dependencies are pinned in `requirements.txt`. The virtualenv lives in `sherri/` and **must not be committed** (see `.gitignore`).

---

## Repository File Structure

```
app.py                    ← Flask entry point; single route "/" renders index.html
templates/
  index.html              ← Single-page Jinja2 template containing all sections
static/
  css/
    style.css             ← All site styling
  images/                 ← Sherri's photos; reference via url_for('static', ...)
Procfile                  ← web: gunicorn app:app  (required by Render)
requirements.txt          ← flask, gunicorn
AGENTS.md                 ← This file
README.md
.gitignore                ← Must include: sherri/, __pycache__/, *.pyc, .env
```

`app.py` should remain minimal — one route only:

```python
from flask import Flask, render_template

app = Flask(__name__)

@app.route("/")
def index():
    return render_template("index.html")
```

---

## Local Development Workflow

1. **Activate the virtual environment** (PowerShell):
   ```powershell
   .\sherri\Scripts\activate
   ```

2. **Install dependencies** (first time or after changes to `requirements.txt`):
   ```powershell
   pip install -r requirements.txt
   ```

3. **Run the development server:**
   ```powershell
   flask --app app run --debug
   ```
   The site is available at `http://127.0.0.1:5000`. Flask's debug reloader picks up changes to templates and static files automatically — no restart needed.

4. **Do not commit** the `sherri/` virtualenv directory. Confirm it is listed in `.gitignore`.

---

## Deployment — Render.com

- The Render Web Service is **already connected** to this GitHub repository.
- **Auto-deploy trigger:** any commit pushed or merged to the `main` branch causes Render to redeploy automatically.
- Changes on any other branch are visible **only locally** until merged to `main`.
- Render reads `Procfile` for the start command:
  ```
  web: gunicorn app:app
  ```
- Set the environment variable `FLASK_ENV=production` in the Render dashboard (Settings → Environment).
- Python version can be pinned via a `runtime.txt` file if needed (e.g., `python-3.12.0`).

### Typical development loop

```
local edits → test at localhost:5000 → commit & push to feature branch
→ open PR → merge to main → Render auto-deploys → live site updated
```

---

## Website Sections (single scrollable page)

All sections live in `templates/index.html`. Each section should have a corresponding `id` attribute for anchor navigation if a sticky nav is added later.

| Section | `id` | Content |
|---|---|---|
| Hero / Intro | `#hero` | Name, tagline, brief description |
| About / Experience | `#about` | Qualifications, subjects tutored, background |
| Pricing | `#pricing` | Rates per subject/level (table or cards) |
| Gallery | `#gallery` | Photos from `static/images/` |
| Contact | `#contact` | Phone, email, or contact details |

---

## Coding Conventions

- **Content/copy** lives directly in `templates/index.html` — no separate data files.
- **Images** are stored in `static/images/` and referenced using Flask's `url_for` helper:
  ```html
  <img src="{{ url_for('static', filename='images/photo.jpg') }}" alt="...">
  ```
- **CSS** all goes in `static/css/style.css`. No inline styles.
- **No JavaScript frameworks.** Plain HTML and CSS only, unless a specific interactive feature is requested.
- **Keep `app.py` to one route.** If new routes are ever needed, add them there; do not create separate route files unless the app grows significantly.
- **Do not modify the virtualenv** (`sherri/`) — it is a local-only artefact.
- Prefer semantic HTML elements (`<section>`, `<header>`, `<nav>`, `<footer>`, `<article>`).
- Use descriptive `alt` text on all images.
