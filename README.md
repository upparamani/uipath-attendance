# UIPath Lab Attendance System

**Serverless, real-time attendance system for the ISE Department · KLS Gogte Institute of Technology, Belagavi**

Built for the UI Path Lab (Information Science & Engineering), this system lets students mark their own attendance using a one-time session token displayed by the professor. No backend server, no database installation — runs entirely from a GitHub Pages static site with [JSONbin.io](https://jsonbin.io) as the cloud data store.

---

## How It Works

```
Professor opens session → token displayed on screen
    ↓
Students scan/visit the URL on their device
    ↓
Student enters USN + token → marked present in real time
    ↓
Professor monitors live list → closes session → exports Excel
```

---

## Files

| File | Purpose |
|---|---|
| `index.html` | Entire application — UI, logic, export |
| `students.js` | Student roster for Division A and Division B |
| `style.css` | (Legacy) base styles — now embedded in index.html |

---

## Features

### Professor Dashboard (`?prof=1`)
- Password-protected access (default: `1111`)
- Select **Division**, **Date**, and **Period time** (e.g. 8:30 AM) before opening
- **Open / Pause / Resume / Close** session controls
- Auto-generated 4-digit random token per session, visible on screen
- 5-minute session timer with auto-close
- **Live present & absent lists** updated every 3 seconds
- **Flash card notification** slides in top-right whenever a student marks attendance
- Present / Absent / Total live counters
- **Export Excel** — generates a KLS-format `.xlsx` file covering all sessions (up to 25 periods) with one column per session

### Student Page
- Select Division → Enter USN → name preview shown instantly
- Enter session token from board → Submit
- After successful submission: **form is permanently replaced** with a success screen ("You may close this tab") — no back navigation, no resubmission possible
- Duplicate attempt shows a clear warning and **disables all inputs** so USN cannot be swapped to attempt proxy

### Excel Export (KLS Format)
Matches the standard KLS Gogte attendance register format:

| Row | Content |
|---|---|
| 1 | KLS Gogte Institute of Technology (merged, dark blue) |
| 2 | Gogte Institute of Technology, Belagavi |
| 3 | Department of Information Science & Engineering |
| 4 | UI Path Lab · ISE Dept · Division · 2025-26 |
| 5 | Spacer |
| 6 | Generated timestamp (right-aligned) |
| 7 | SI · USN · Name · Total · % · **rotated date+time columns** |
| 8 | DAY TOTAL row |
| 9+ | Students — **P** (green) / **A** (red) per session |

- Dates in `dd.mm.yyyy` format
- Times in `8.30am` / `2.15pm` format
- Alternating row shading
- Total and % calculated across all exported sessions

---

## Setup

### 1. Clone / fork this repo

```bash
git clone https://github.com/<your-username>/uipath-attendance.git
```

### 2. Update the student roster

Edit `students.js`. The file exports a `STUDENTS` object with two keys — `A` and `B` — each containing an array of `{ id, name }` objects.

```js
const STUDENTS = {
  A: [
    { id: "2GI23IS001", name: "Abhijit M Kalamadi" },
    // ...
  ],
  B: [
    { id: "2GI23IS071", name: "Prerana S Patil" },
    // ...
  ]
};
```

### 3. Set up JSONbin

1. Create a free account at [jsonbin.io](https://jsonbin.io)
2. Create a new bin with this initial JSON:
   ```json
   {
     "activeSession": null,
     "sessions": []
   }
   ```
3. Copy your **Bin ID** and **Master Key**
4. In `index.html`, update the config at the top of the `<script>` block:
   ```js
   const BIN_ID  = "your-bin-id-here";
   const API_KEY = "your-master-key-here";
   ```

### 4. Change the professor password

In `index.html`, find:
```js
const PROF_PASS = "1111";
```
Change to any password you prefer.

### 5. Deploy to GitHub Pages

1. Push to GitHub
2. Go to **Settings → Pages → Source → main branch / root**
3. Your site will be live at `https://<username>.github.io/<repo-name>/`

---

## Usage

| Who | URL |
|---|---|
| Students | `https://<username>.github.io/<repo-name>/` |
| Professor | `https://<username>.github.io/<repo-name>/?prof=1` |

The professor card only appears when `?prof=1` is in the URL — keep this link private.

---

## Proxy Prevention

Several layers prevent proxy attendance:

1. **Token required** — professor generates a new random 4-digit token each session; students without physical presence don't know it
2. **Division check** — token is tied to a specific division; wrong division = rejected
3. **One submission per USN** — second attempt shows a warning and locks all inputs
4. **No back navigation** — after submission the form is permanently replaced; refreshing starts fresh but requires the token again
5. **Session pause** — professor can pause mid-session (e.g. during a roll check) to block late submissions

---

## Tech Stack

| Layer | Technology |
|---|---|
| Hosting | GitHub Pages (static) |
| Data store | JSONbin.io (free tier) |
| Excel export | [xlsx-js-style](https://github.com/gitbrent/xlsx-js-style) v1.2.0 |
| Fonts | Google Fonts — Sora + Space Mono |
| No frameworks | Vanilla HTML / CSS / JS |

---

## Limitations

- JSONbin free tier has rate limits (~100 requests/day on the free plan — sufficient for one class session)
- Session history is stored in the same bin; very large history (100+ sessions) may approach the 100KB bin size limit — export and clear periodically
- All students share one JSONbin; do not expose the API key publicly in a production setting

---

## Department

**Department of Information Science & Engineering**  
KLS Gogte Institute of Technology, Belagavi  
Academic Year 2025–26
