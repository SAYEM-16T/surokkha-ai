# Surokkha AI — AIVA Submission Guide (do this tomorrow)

You need to submit **3 things** to <https://judginghub.com/en/events/AIVA>:

1. **Live product link** ✅ (the web app — deploy below)
2. **Demo video** (YouTube, ≤3 min, English subtitles)
3. **Pitch deck** (PDF with speaker notes)

Everything is already built. This guide is the exact, in-order checklist to ship it. Budget ~2–3 hours.

---

## ⏱️ Order of operations

```
STEP 1  Get a free Gemini key            (5 min)
STEP 2  Test the app locally with the key (10 min)
STEP 3  Deploy to Vercel → live link      (20 min)
STEP 4  Export the pitch deck to PDF       (5 min)
STEP 5  Record + upload the demo video     (45 min)
STEP 6  Submit all 3 on JudgingHub         (10 min)
```

---

## STEP 1 — Get a free Gemini API key (5 min)

1. Open <https://aistudio.google.com/app/apikey> and sign in with a Google account.
2. Click **"Create API key"** → **"Create API key in new project"**.
3. Copy the key (looks like `AIza...`). Keep it safe — don't share it publicly.

> Free tier is plenty for the demo and judging.

---

## STEP 2 — Test locally with the key (10 min)

In a terminal, from the project folder:

```bash
cd /home/technonext/AA__ROOT/surokkha-ai
cp .env.example .env.local
```

Open `.env.local`, paste your key so it reads:
```
GEMINI_API_KEY=AIza...your_real_key...
```

Then:
```bash
npm install     # if you haven't already
npm run dev
```
Open <http://localhost:3000>. Click the sample chips and **Analyze**. You should now see
**"Engine: Rules + AI (Gemini)"** in the result (that confirms the AI is live).

> If you ever see "Engine: Rules only", the key isn't being read — re-check `.env.local` and restart `npm run dev`.

---

## STEP 3 — Deploy to Vercel → your live link (20 min)

### 3a. Put the code on GitHub
```bash
cd /home/technonext/AA__ROOT/surokkha-ai
git init
git add .
git commit -m "Surokkha AI — working demo"
gh auth login          # if not logged in (choose GitHub.com → HTTPS → login with browser)
gh repo create surokkha-ai --public --source=. --push
```

> `.gitignore` already excludes `.env.local`, so your key will NOT be uploaded. Good.

### 3b. Deploy on Vercel
1. Go to <https://vercel.com> → **Sign up / Log in with GitHub**.
2. **Add New… → Project** → import your `surokkha-ai` repo → **Import**.
3. Before clicking Deploy, open **Environment Variables** and add:
   - Name: `GEMINI_API_KEY`  Value: `AIza...your_key...`
   - (optional) Name: `GEMINI_MODEL`  Value: `gemini-2.0-flash`
4. Click **Deploy**. Wait ~1–2 minutes.
5. You get a link like `https://surokkha-ai-xxxx.vercel.app` — **open it, test a sample**. This is your **Live product link**. ✅

> Alternative (CLI): `npm i -g vercel && vercel && vercel --prod`, then add the env var in the dashboard and redeploy.

---

## STEP 4 — Export the pitch deck to PDF (5 min)

1. Open the file `init/pitch-deck.html` in your browser (double-click it, or drag into Chrome).
2. **Fill in the brackets first** — `[Your Name]`, team, the funding number, and any traction data you have. (Edit the HTML, or just edit the PDF later — but doing it in the HTML is cleanest.)
3. Press **Ctrl/Cmd + P** → Destination **Save as PDF** → **Save**.
   - The slides AND your speaker notes both print — judges read the notes to score you.
4. Name it `Surokkha-AI-Pitch-Deck.pdf`. This is your **Pitch deck** submission. ✅

> Tip: in the print dialog, turn **off** "Headers and footers" for a clean look.

---

## STEP 5 — Record + upload the demo video (45 min)

1. Open `init/demo-script.md` — it's a shot-by-shot script (~2:45) with exactly what to click and say.
2. Record your screen on the **live Vercel link** (use **OBS Studio** free, or Loom).
3. Speak the Bangla narration from the script.
4. Upload to **YouTube** → set visibility **Unlisted** or **Public** (NEVER Private).
5. Add subtitles: video → **Subtitles → Add → English → Upload file** → choose `init/demo-subtitles.srt`.
6. Copy the YouTube link — that's your **Demo video** submission. ✅

> Keep it **under 3:00**. Show the 3 key moments clearly: a **red scam alert**, the **reasons**, and a **green safe result**.

---

## STEP 6 — Submit on JudgingHub (10 min)

Go to <https://judginghub.com/en/events/AIVA> and paste:

| Field | What to paste |
|---|---|
| Live product link | your `https://...vercel.app` URL |
| Demo video | your YouTube (Unlisted/Public) URL |
| Pitch deck | upload `Surokkha-AI-Pitch-Deck.pdf` |

Double-check every link opens in an **incognito window** (so you know judges can open them too).

---

## If something breaks (quick fixes)

| Problem | Fix |
|---|---|
| Result says "Rules only" on Vercel | Env var not set — Vercel → Project → Settings → Environment Variables → add `GEMINI_API_KEY` → **Redeploy** |
| `gh: command not found` | Use the Vercel website import flow (3b) instead of CLI; or install GitHub CLI |
| Build fails on Vercel | Run `npm run build` locally first; fix any error it prints. (It currently builds clean.) |
| Bengali text looks like boxes | It's a font issue on that device only; the deployed site loads the Bengali web font automatically |
| Gemini quota error during judging | The app auto-falls back to rules-only and still works — no crash |

---

## What you're submitting — recap

- **Live link:** a real, working scam detector in Bangla + Banglish + English (not a placeholder).
- **Video:** 3 min showing it catch a bKash scam, a marriage scam, and correctly pass a safe message.
- **Deck:** 12 slides + detailed speaker notes covering problem, market, solution, business model.

That fully satisfies all three AIVA requirements. Good luck. 🛡️
