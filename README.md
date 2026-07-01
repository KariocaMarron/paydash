# paydash

**Hosting PayDash on GitHub — the Why and the How**

This document explains why the app lives on GitHub and how each step worked, so you can repeat or troubleshoot the process yourself and understand the reasoning behind each decision.

---

## 1. The problem we were solving

The app is a single `index.html` file. You can open that file by double-clicking it on a PC and it works — but that approach fails for what you actually wanted:

- **Use it on your phone and on several PCs.** A file sitting on one computer isn't reachable from your phone, and copying it to each device means each device has its own separate copy.
- **Sync your data through Google Drive.** Google's sign-in (OAuth) refuses to work on a file opened directly from disk. A local file's address is `file:///C:/…`, and Google only allows sign-in from a proper web address starting with `https://`. A local file has no web address, so Drive sync is impossible there.

So we needed the app to live at a real web address — an `https://` link any device can open. That is exactly what "hosting" means.

> **Key idea:** "Hosting" = putting a file on a computer that is always online (a web server) so it has a public URL. We used GitHub to do this for free.

---

## 2. What GitHub and GitHub Pages are

**GitHub** is a website where people store files for projects; a storage area is called a **repository** ("repo"). Yours is `paydash`. **GitHub Pages** is a free feature that publishes the files in a repo as a live website: if the repo contains an `index.html`, Pages serves it at an address like `https://YOURNAME.github.io/REPONAME/`.

We use GitHub Pages purely as a free, reliable web host — we're not "coding on GitHub," just giving the file a public address. It's free, provides `https://` automatically (which Google's sign-in requires), never sleeps, and updating the site is just re-uploading a file.

---

## 3. Why the repository is public (and how your data stays private)

On GitHub's free plan, Pages only works with a **public** repository. Public means anyone who knows the link can view the files, including the source of `index.html`.

That created a privacy problem: an early version had personal details written inside the file. On a public repo, anyone could read them.

**How we solved it — public code, private data:**

- **The published file (`index.html`)** is a **blank template.** It contains the app's design and logic, but no personal data: blank name, blank employer, no salary, no payslips, no holidays, and no colleague names or rota. A stranger visiting the URL sees an empty dashboard.
- **Your real data lives only in your private Google Drive**, in a file called `PayDashboard.json`. The app downloads it only after you sign in with your Google account — which only you can do.
- **A full personal copy** (with your details filled in) is kept **only on your computer** as a private backup. It is never uploaded to GitHub.

So your figures appear only when you authenticate. This is the standard, safe pattern: public code, private data.

---

## 4. The steps we took — and why each one mattered

**Step 1 — Create a GitHub account.** Free, on GitHub.com. Your username becomes part of your web address: `kariocamarron.github.io`.

**Step 2 — Create the repository (`paydash`, Public).** Public because free GitHub Pages requires it (section 3). We added a README so the repo wasn't empty.

**Step 3 — Upload the app file (a naming lesson).** The first upload used a long filename and returned a 404, for two reasons:
- **Case sensitivity** — web servers treat `Name.html` and `name.html` as different files; one wrong capital = 404.
- **Windows shortened the long name** to an old "short name" (e.g. `NAME~1.HTM`), which didn't match the link.

The fix: name the main page **`index.html`** — the universally recognised home-page filename a server serves automatically. So `https://kariocamarron.github.io/paydash/` just works, with no long filename to mistype.

**Step 4 — Turn on GitHub Pages.** Repo → **Settings → Pages → Build and deployment → Source = "Deploy from a branch" → Branch = `main`, folder = `/ (root)` → Save.** After ~a minute it shows "Your site is live at …".

**Step 5 — Note the address and the "origin".**
- App link: `https://kariocamarron.github.io/paydash/`
- Origin: `https://kariocamarron.github.io` (just the domain) — this is what Google's sign-in must recognise.

**Step 6 — Set up Google sign-in (so Drive sync works).** In the Google Cloud Console we:
- Created a project and enabled the **Google Drive API**.
- Configured the **OAuth consent screen** (app name, your email, marked External, added your Google account as a **test user**).
- Created an **OAuth Client ID** (type: Web application) and listed `https://kariocamarron.github.io` under **Authorised JavaScript origins** — the security gate, so only that exact address can use the sign-in.
- Pasted the resulting **Client ID** into the app's `CLIENT_ID` line.

We did **not** use a "Client secret" — this app uses the browser sign-in popup (the token flow), which needs only the public Client ID.

**Step 7 — Keep the public file blank, data in Drive.** Once sign-in worked and your data was saved to Drive, the published `index.html` is the blank template. The public page is harmless; your data is private.

**Step 8 — Use it everywhere.** Open the link on any PC or iPhone (Safari → **Add to Home Screen** for an app icon), tap **Connect to Drive**, sign in once, and your data appears and stays in sync.

---

## 5. What the app does

Beyond hosting, the dashboard is a personal UK pay, hours and holiday tool (2026/27 tax year). Main features:

- **Home menu** with quick-access cards to each section; light/dark mode.
- **My roster & team rota** — weekly shifts with net-hours totals.
- **Income** — gross and estimated take-home (weekly/monthly/yearly), a minimum-wage check, and a link to the full latest payslip.
- **Holiday & lieu** — taken, remaining, booked, plus automatic 12.07% accrual.
- **Payslip import** — reads payslip PDFs using a position-aware reader (rebuilds the columns correctly), with a full breakdown and a plain-English glossary of every pay/deduction code.
- **History & trends** — every payslip charted, year-to-date totals, an "expected vs actual" comparison (adjusted for the number of working days each month), and **month notes & alerts** that explain each month's difference or flag an unexplained shortfall.
- **PAYE tax check** — uses HMRC's cumulative method to check whether the Income Tax deducted so far looks correct.
- **P60 import** — reads year-end P60 PDFs (total pay, tax, NI) per tax year.
- **Backups** — export/import your data as JSON at any time (there is also a `PayDashboard_import_backup.json` you can import to load prepared data), plus automatic Google Drive sync when connected.

All calculation and document reading happens **locally in the browser** — nothing is sent to a server.

---

## 6. How to publish an update

1. Edit the app locally (or ask for the change).
2. Go to the `paydash` repo → **Add file → Upload files**.
3. Drag in the new **`index.html`** (it replaces the old one) → **Commit changes.**
4. Wait ~1 minute, then hard-refresh the site (**Ctrl+Shift+R** on PC; on iPhone, close the tab and reopen).

The web address never changes, so your phone icon and bookmarks keep working. Only upload the **blank** `index.html` — keep your personal copy on your computer.

> Note: the `README.md` is a separate file. Uploading a new `index.html` does **not** change the README; edit and commit `README.md` separately to update it.

---

## 7. Troubleshooting

| Symptom | Cause | Fix |
|---|---|---|
| **404 — File not found** | Filename mismatch / wrong capitals / Windows shortened the name | Use `index.html`; check exact spelling and casing |
| **Page shows plain "paydash" text** | GitHub still serving the old README, or the browser cached it | Wait ~1 min for the rebuild, then hard-refresh (Ctrl+Shift+R) |
| **Dashboard loads but Drive won't connect** | Google needs the exact origin allowed, and new settings take a few minutes | Confirm `https://kariocamarron.github.io` is in Authorised JavaScript origins; wait ~5 min and retry |
| **"Google hasn't verified this app"** | The app is in testing mode (normal for personal use) | Advanced → Continue → Allow (you're a test user, so this is safe) |
| **Sync stops after a while** | Sign-in sessions last about an hour | Click **Connect to Drive** again |

---

## 8. Glossary

- **Repository (repo):** a project folder stored on GitHub.
- **Commit:** saving a change to the repo (each upload is a commit).
- **GitHub Pages:** GitHub's free website-hosting feature.
- **Hosting:** putting files on an always-on computer so they're reachable via a public web address.
- **HTTPS:** the secure (`https://`) version of a web address; required by Google sign-in.
- **`index.html`:** the default home-page filename a web server serves automatically.
- **Origin:** the domain part of a URL (e.g. `https://kariocamarron.github.io`).
- **OAuth / Client ID:** Google's sign-in system and the public key that identifies your app to it.
- **`drive.file` scope:** a permission letting the app see only the one file it creates in your Drive — nothing else.
- **YTD (Year To Date):** running total since the start of the tax year (6 April); a payslip's "To Date" column.
