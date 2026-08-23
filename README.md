# HaqSetu AI — Entitlement Audit & Recovery Engine

**Discover less. Recover more.**

HaqSetu is a civic-tech prototype that helps Indian citizens find out which government benefits they're entitled to, understand why they aren't receiving them, and follow a clear, personalized recovery plan.

> An AI-powered entitlement auditor helping citizens find the benefits they are entitled to, understand what is blocking them, and take the next step.

**🔗 Live demo:** https://aarya2024-afk.github.io/haqsetu-ai-prototype/ (see [Deployment](#deployment) below)_

---

## What this is

This repo contains a **self-contained, front-end-only prototype** of the HaqSetu product experience. It's built to demonstrate the full product journey end-to-end for a hackathon/demo audience without needing a backend, database, or API keys.

The core journey it demonstrates:

```
Citizen Profile → Entitlement Audit → Eligible Benefits → Current Benefits
      → Missing Benefits → Blocker Detection → Recovery Plan
      → Application / Correction → Tracking
```

### Try it in under 3 minutes

1. Open the site and click **Try Demo** (loads a sample citizen, Aarav Sharma).
2. Review his uploaded documents in the **Document Centre**.
3. Click **Run Entitlement Audit** and watch the audit animation.
4. See the result: **3 benefits need attention**, an estimated **₹35,000/year** gap.
5. Open **Scholarship A** → see it's eligible but *blocked* by an expired income certificate.
6. Open **Fee Reimbursement** → see the **name mismatch** cross-check between his College ID and bank document.
7. Open **Student Transport Benefit** → see it's eligible but was **never applied for**.
8. Go to **Recovery Plan**, mark an action complete, and watch the **Entitlement Tracker** progress.
9. Try the **Rejection Analyzer** to see a real rejection notice translated from bureaucratic wording into a plain next step.

---

## Why it's built this way

This prototype is intentionally **not** styled like a typical AI-startup demo. It follows a "modern public-service portal" visual language — muted teal/off-white/terracotta palette, editorial serif headings, thin borders, minimal rounding, real photography, no glassmorphism or neon — so it reads as a plausible civic digital service rather than a generic chatbot UI.

It also encodes an important architectural principle: **the AI layer never decides eligibility.**

| Layer | Responsibility |
|---|---|
| **Rule Engine** | Eligibility, income/age/state conditions, benefit calculations, scheme matching |
| **AI Layer** | Document extraction & comparison, plain-language explanations, rejection-letter interpretation, recovery-plan generation, conversational Q&A ("Ask HaqSetu") |

In this prototype both layers are simulated with a deterministic **demo dataset** (see below) so the whole thing works with zero external dependencies or API keys. The code is written so this data layer can later be swapped for real rule evaluation and a live LLM call without touching the UI.

---

## Demo dataset — important disclaimer

All eligibility results, documents, and figures (Aarav Sharma, ₹35,000 gap, the four sample benefits, etc.) are a **labelled prototype dataset**, not live or official government records. This is stated in-app in a persistent banner and again on the results page. Do not represent this data as authoritative when demoing or deploying.

---

## Tech stack

This prototype intentionally has **zero build step and zero dependencies**:

- Plain **HTML + CSS + vanilla JavaScript** in a single file (`index.html`)
- Client-side "routing" via a small `go(page)` state machine — no framework, no bundler
- Google Fonts (`Playfair Display` for headings, `Inter` for UI/body) loaded via CDN `<link>`
- Unsplash-hosted photography loaded via `<img>`/CSS `background-image` URLs

This means you can open `index.html` directly in a browser with no server, and deploy it anywhere that serves static files.

> **Note on the original spec:** the design brief this was built from called for a full React + TypeScript + Tailwind + Node/Express + Supabase stack with a modular `src/{components,pages,services,ai,rules,data,types,utils}` structure. That's the right shape for a production build. This repo is the **prototype/demo tier** — a single static file that proves the product experience — so it can be shipped and judged in minutes. See [Roadmap](#roadmap-toward-the-full-architecture) for how to grow this into that structure.

---

## Running locally

No install required.

```bash
git clone https://github.com/<your-username>/haqsetu-ai-prototype.git
cd haqsetu-ai-prototype
open index.html        # macOS
# or: xdg-open index.html   (Linux)
# or: start index.html      (Windows)
```

Or serve it (recommended, avoids any browser file:// quirks):

```bash
python3 -m http.server 8000
# then visit http://localhost:8000
```

---

## Deployment

Since this is a single static HTML file, you can deploy it almost anywhere for free. Three easy options:

### Option A — GitHub Pages (recommended, ties directly to this repo)

1. Push this repo to GitHub (see [Publishing to GitHub](#publishing-to-github) below).
2. In your repo on GitHub: **Settings → Pages**.
3. Under **Build and deployment → Source**, choose **Deploy from a branch**.
4. Branch: `main`, folder: `/ (root)` → **Save**.
5. Wait ~1 minute. Your live URL will appear at the top of that page, typically:
   ```
   https://<your-username>.github.io/haqsetu-ai-prototype/
   ```
6. Paste that URL into the **Live demo** line at the top of this README.

### Option B — Netlify (drag-and-drop, fastest)

1. Go to [app.netlify.com/drop](https://app.netlify.com/drop).
2. Drag the whole project folder (containing `index.html`) onto the page.
3. Netlify gives you a live URL immediately. You can rename it or add a custom domain from the site dashboard.

### Option C — Vercel

```bash
npm install -g vercel
vercel --prod
```
Follow the prompts (no build command needed — it's a static site).

---

## Publishing to GitHub

If you haven't already turned this folder into a GitHub repo:

```bash
cd haqsetu-ai-prototype
git init
git add .
git commit -m "Initial commit: HaqSetu AI prototype"
git branch -M main
git remote add origin https://github.com/<your-username>/haqsetu-ai-prototype.git
git push -u origin main
```

Create the empty repo on GitHub first at [github.com/new](https://github.com/new) (don't initialize it with a README there, to avoid a merge conflict — this repo already has one), then run the commands above.

---

## Project structure

```text
haqsetu-ai-prototype/
├── index.html      # entire prototype: markup, styles, demo data, and app logic
├── README.md        # this file
└── LICENSE           # MIT license
```

Inside `index.html`, the JavaScript is organised into clearly commented sections that mirror the intended production architecture even though it's one file:

```text
DATA LAYER          — demo dataset (profile, documents, benefits, recovery actions, tracker state)
RULE ENGINE          — eligibility/gap calculations, kept separate from the AI layer
ICONS                — inline SVG icon set
ROUTER               — page state machine (go(), render())
RENDER — PAGES        — one function per screen (home, start, documents, processing,
                         results, benefit detail, recovery, tracker, rejection analyzer, dashboard)
```

## Roadmap toward the full architecture

To evolve this prototype into the production-shaped app described in the original spec:

1. **Extract data** — move `PROFILE`, `DOCUMENTS`, `BENEFITS`, etc. out of `index.html` into `src/data/*.json`.
2. **Extract rules** — move `computeGap()` / `computeCounts()` and eligibility checks into `src/rules/`, written so they can be swapped for real scheme rules per state/scheme.
3. **Add an AI service layer** — move the "Ask HaqSetu" and rejection-analyzer canned responses into `src/ai/`, backed by a real LLM call (see the app's own **AI Assistant** section for the intended prompt shape: answer only from structured entitlement data, never invent eligibility).
4. **Componentize the UI** — port each `page*()` render function into a React component under `src/pages/`, with shared pieces (benefit card, doc row, stepper) under `src/components/`.
5. **Add a backend** — Node/Express (or equivalent) + Postgres/Supabase to persist real citizen profiles, documents, and recovery progress, replacing the in-memory `STATE` object.
6. **Connect real data sources** — replace the demo dataset with verified government scheme APIs/datasets, keeping the same rule-engine interface so the UI doesn't need to change.

## Disclaimer

HaqSetu AI is a hackathon/demo prototype. It is not affiliated with, endorsed by, or representing any Indian government department. All scheme names, amounts, and citizen data shown are fictional sample data for demonstration purposes only.

## License

MIT — see [LICENSE](./LICENSE).
