# Broccolift 🥦

A personal workout and running tracker built as a Progressive Web App (PWA). Runs entirely in the browser — no server needed. Data is stored in your own Google Sheet.

## What it does

- **Lift mode** — log D1/D2/D3/Free workouts, track sets and weights, view progress charts, muscle group coverage and training load heatmaps
- **Run mode** — log runs by type (Easy/Fast/Long), follow a periodised weekly plan (W0–W4 + Special weeks), track weekly distance with target lines
- All data lives in **your own Google Sheet** — you own it, you can export it, nothing is stored on any server

---

## Setup guide (for a new user)

You will need a Google account and a GitHub account.

### Step 1 — Fork the repository

1. Go to `https://github.com/antpeacor/broccolift`
2. Click **Fork** (top right) → Fork to your own account
3. In your fork, go to **Settings → Pages**
4. Under "Build and deployment", set Source to **Deploy from a branch**, branch **main**, folder **/ (root)**
5. Click Save — your app will be live at `https://YOUR_GITHUB_USERNAME.github.io/broccolift`

### Step 2 — Create your Google Sheet

1. Go to [sheets.google.com](https://sheets.google.com) and create a new spreadsheet
2. Name it `broccolift` (or anything you like)
3. Create the following tabs (right-click the sheet tab → Insert sheet):

| Tab name | Columns (row 1 = header) |
|---|---|
| `sessions` | `id, date, day, exercise, set1, set2, set3, note` |
| `d1` | `exercise` |
| `d2` | `exercise` |
| `d3` | `exercise` |
| `df` | `exercise` |
| `exercises` | `exercise, Quads, Hamstrings, Glutes, Calves, Back, Chest, Shoulders, Arms, Rear delts, Core` |
| `runs` | `id, date, day, distance, week_type` |
| `running_weeks` | `date, week_type, km_target, km_real` |

4. Fill `d1`, `d2`, `d3` with your exercise names (one per row, below the header)
5. Fill `exercises` with muscle group scores — each row must sum to 100. You can ask ChatGPT: *"Fill in muscle group stimulus scores for each exercise. Each row must sum to 100. Groups: Quads, Hamstrings, Glutes, Calves, Back, Chest, Shoulders, Arms, Rear delts, Core."*
6. Fill `running_weeks` with your plan if you use run mode (dates must be Mondays, week_type = W0/W1/W2/W3/W4/S)
7. Copy the **Spreadsheet ID** from the URL: `docs.google.com/spreadsheets/d/**COPY_THIS_PART**/edit`

### Step 3 — Create a Google Cloud OAuth app

This lets the app authenticate with your Google account and access your sheet.

1. Go to [console.cloud.google.com](https://console.cloud.google.com)
2. Create a new project (top left dropdown → New Project)
3. Go to **APIs & Services → Library** → search for **Google Sheets API** → Enable it
4. Go to **APIs & Services → OAuth consent screen**
   - User type: **External**
   - Fill in app name (e.g. `Broccolift`), your email, and developer email
   - Add scope: `https://www.googleapis.com/auth/spreadsheets`
   - Add yourself as a test user
5. Go to **APIs & Services → Credentials → Create Credentials → OAuth 2.0 Client ID**
   - Application type: **Web application**
   - Authorised JavaScript origins: add `https://YOUR_GITHUB_USERNAME.github.io`
   - Click Create
6. Copy the **Client ID** (looks like `123456789-abc.apps.googleusercontent.com`)

### Step 4 — Update the app code

In your forked repository, open `index.html` and find these two lines near the top of the `<script>` section (around line 488):

```javascript
const CLIENT_ID = '281076985814-vq0vfa3jces2jlttd236jofhn9jo7v42.apps.googleusercontent.com';
const SHEET_ID  = '1nddYAEfDluokRhQIt1LE372ZTBnEt72oPk6OIzz_7CU';
```

Replace both values with your own:

```javascript
const CLIENT_ID = 'YOUR_CLIENT_ID_HERE';
const SHEET_ID  = 'YOUR_SHEET_ID_HERE';
```

Commit the change. GitHub Pages will redeploy automatically within 1–2 minutes.

### Step 5 — Open the app

Go to `https://YOUR_GITHUB_USERNAME.github.io/broccolift`, sign in with your Google account, and you're done.

**Tip:** on Android, open the URL in Chrome → three-dot menu → **Add to Home screen** to install it as an app.

---

## Run mode — weekly plan structure

`running_weeks` tab format:

| date | week_type | km_target | km_real |
|---|---|---|---|
| 2026-05-05 | W1 | 30 | |
| 2026-05-12 | W2 | 33 | |
| 2026-05-19 | W3 | 36 | |
| 2026-05-26 | W0 | 27 | |

- Dates must be **Mondays**
- Week types: **W1** (base), **W2** (+10%), **W3** (+10%), **W4** (+10%), **W0** (recovery = 75% of peak), **S** (special: race/travel/sick — ignored in calculations)
- `km_real` is filled automatically when you press **Update targets** in the app
- **Update targets** recalculates all future week targets based on your actual performance

---

## Troubleshooting

**"Error: sessions: 404"** — The `sessions` tab doesn't exist or is named differently. Tab names are case-sensitive.

**"Save failed: 400"** — Usually a column range issue. Try refreshing the page.

**Re-auth on every reload** — Google OAuth tokens expire after 1 hour. The app will show a "Reconnect" banner — one tap restores the session.

**Blank page after sign-in** — Check the browser console for errors. Most likely the Sheet ID or tab names don't match.
