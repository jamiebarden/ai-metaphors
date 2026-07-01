# ai-metaphors-72-once-only
72 AI metaphor decision scheme.  Once one is selected, the other's can't be selected.
[README.md](https://github.com/user-attachments/files/29530754/README.md)
# What Kind of AI Is It, to You?

An interactive classroom instrument. A student answers five short questions about
their attitude toward AI — **Feeling, Relationship, Independence, Substance, and
Focus in Time** — and is matched to one of **72 metaphors** drawn from the course
field guide. After the result, a *paths not taken* explorer lets them change any
single answer and watch the match move.

The whole thing is one self-contained `index.html` file. No build step to run.

---

## Put it online (GitHub Pages)

1. Create a new repository on GitHub (e.g. `ai-metaphors`).
2. Upload **`index.html`** to the top level of the repo.
3. Go to **Settings → Pages**.
4. Under **Source**, choose **Deploy from a branch**, pick `main` and `/ (root)`, **Save**.
5. After a minute, visit `https://<your-username>.github.io/<repo-name>/`.

That URL is what you share with students.

---

## Shared store — the once-only rule across every device

Out of the box the app runs in **single-device mode**: claims are remembered only in
the browser being used. To make a claimed metaphor disappear for the **whole class at
the same time**, across everyone's phones and laptops, connect a free Firebase
Realtime Database. It takes about five minutes, once.

### 1. Create the database

1. Go to <https://console.firebase.google.com> and **Add project** (any name; you can
   skip Google Analytics).
2. In the left menu open **Build → Realtime Database → Create Database**.
3. Choose a location, and start in **locked mode** (you'll paste rules in step 3).

### 2. Get your web config

1. In **Project settings** (gear icon) → **General**, scroll to **Your apps**.
2. Click the **web** icon `</>`, register the app (any nickname, no Hosting needed).
3. Firebase shows a `firebaseConfig = { ... }` object. Copy those values.
4. Open `index.html`, find the block near the top of the `<script>` that starts with
   `const FIREBASE_CONFIG = null;`, and replace `null` with your config object — make
   sure it includes the **`databaseURL`** line. Save and re-upload `index.html`.

### 3. Paste these security rules

In **Realtime Database → Rules**, replace the contents with the following and
**Publish**. This lets anyone read the claims and claim a free metaphor, but stops one
student from overwriting another's claim:

```json
{
  "rules": {
    "claims": {
      ".read": true,
      "$slug": {
        ".write": "!data.exists() || !newData.exists()"
      }
    }
  }
}
```

That's it. Open the app, expand the **Instructor panel**, and you should see
**Shared store: connected**. Claims now sync live: when one student lands on a
metaphor, it vanishes from everyone else's pool within a second, and two students who
finish at the same instant can't grab the same one.

### Notes

- **Reset** (in the Instructor panel) now clears the record for **every device at once** —
  use it between classes.
- The web config is fine to keep in the public file; access is governed by the rules
  above, not by the key.
- The free tier easily covers a class. If you ever set `FIREBASE_CONFIG` back to `null`,
  the app simply falls back to single-device mode.

---

## Add the student images

Each metaphor can show a `.png` that a student creates for it.

1. Make a folder named **`images`** at the top level of the repo (next to `index.html`).
2. Drop each picture in, named after its metaphor's **slug** — `The Tool` →
   **`tool.png`**, `The Stochastic Parrot` → **`stochastic-parrot.png`**.
3. Commit. The result card loads `images/<slug>.png` automatically.

Until a file exists for a metaphor, the card shows a lettered placeholder, so the app
always works even with no images uploaded yet. The full list of expected filenames is
in **`image-filenames.csv`** and inside the app's **Instructor panel**.

---

## Sharing a result

Every result has a **Copy link** button. The link encodes the five answers and the
metaphor, so it reopens the same result on any device — handy for a discussion board.
Opening a shared link is read-only and does **not** claim a metaphor.

---

## Files

- `index.html` — the whole app (paste your Firebase config here to enable shared mode).
- `image-filenames.csv` — every metaphor's expected image filename, branch, and tone.
- `images/` — the folder you create for student `.png` files.
