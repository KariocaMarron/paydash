# paydash
Hosting PayDash on GitHub — the Why and the How

This document explains in detail why we put the app on GitHub and how each step worked. It’s written so you can repeat or troubleshoot the process yourself and understand the reasoning behind each decision.


1. The problem we were solving

The app is a single index.html file. You can open that file by double-clicking it on a PC, and it works — but that approach fails for what you actually wanted:


Use it on your iPhone and on several PCs. A file sitting on one computer isn't reachable from your phone. Phones don't have an easy way to open a PC file, and copying the file to each device means each device has its own separate copy.
Sync your data through Google Drive. Google's sign-in (OAuth) flatly refuses to work on a file opened directly from disk. When you open a local file, the browser’s address bar shows file:///C:/.... Google only allows sign-in from a proper web address that starts with https://. A local file has no web address, so Drive sync is impossible there.


So we needed the app to live at a real web address — an https:// link that any device can open. That is exactly what "hosting" means.


Key idea: "Hosting" = putting a file on a computer that is always online (a web server) so it has a public URL. We used GitHub to do this for free.




2. What GitHub and GitHub Pages are


GitHub is a website where people store files for software projects. A storage area is called a repository (or "repo"). Yours is called Paydash.
GitHub Pages is a free feature of GitHub: it can take the files in a repo and publish them as a live website. If your repo contains an index.html, GitHub Pages serves it at an address like https://YOURNAME.github.io/REPONAME/.


We used GitHub Pages purely as a free, reliable web host. We are not really "coding on GitHub" — we're just using it to give the file a public address.


Why GitHub Pages specifically? It's free, it gives an https:// address automatically (Google's sign-in requires HTTPS), it never sleeps, and updating the site is just re-uploading a file.




3. Why the repository is public (and how we protected your data)

On GitHub's free plan, GitHub Pages only works with a public repository. Public means anyone on the internet who knows the link can view the files, including reading the source of index.html.

That created a privacy problem: the first version of the app had your name, salary, payslip figures and the whole team's rota written inside the file. On a public repo, anyone could read those.

How we solved it: we split the app into two parts.


The published file (index.html on GitHub) is a blank template — it contains the app's design and logic, but no personal data (blank name, no salary, no payslip, no holidays, no colleague names).
Your real data lives only in your private Google Drive, in a file called PayDashboard.json. The app downloads it only after you sign in with your Google account.


So a stranger visiting the public URL sees an empty dashboard. Your figures appear only when you authenticate, which only you can do. This is the standard, safe pattern: public code, private data.


A full personal copy of your data (Jose_PayDashboard.html) is stored locally in your folder as a private backup. It is never uploaded.




4. The steps we took — and why each one mattered

Step 1 — Create a GitHub account

A free account on GitHub.com. This is who “owns” the repo and the published site. Your username became part of your web address: kariocamarron.github.io.

Step 2 — Create the repository (paydash, Public)

This is the folder GitHub will publish. We made it Public because free GitHub Pages requires it (see section 3). We added a README so the repo wasn’t empty.

Step 3 — Upload the app file (and a naming lesson)

The first upload was named Jose_PayDashboard.html. When I opened the link, I got a 404 (file not found). Two things caused trouble:


Case sensitivity. Web servers treat Jose_PayDashboard.html and jose_paydashboard.html as different files. One wrong capital letter = 404.
Windows mangled the long name. The upload actually arrived as JOSE_P~1.HTM (Windows’ old “short name” format), which didn’t match the link at all.


The fix: we renamed the file to index.html. index.html is the universally recognised “home page” filename — a web server serves it automatically when someone visits the folder. So the clean address https://kariocamarron.github.io/paydash/ just works, with no long filename to mistype.


Lesson: name the main page index.html. It avoids capitalisation mistakes and gives you a tidy URL.



Step 4 — Turn on GitHub Pages

In the repo: Settings → Pages → Build and deployment → Source = “Deploy from a branch” → Branch = main, folder = / (root) → Save. This tells GitHub: “publish the files in this repo as a website.” After about a minute, it showed“Your site is live at …”.

Step 5 — Note the address and the “origin”


App link: https://kariocamarron.github.io/paydash/
Origin: https://kariocamarron.github.io (just the domain part). The origin is what Google’s sign-in needs to recognise (next step).


Step 6 — Set up Google sign-in (so Drive sync works)

In the Google Cloud Console, we:


Created a project (PayDash) and enabled the Google Drive API.
Configured the OAuth consent screen (app name, your email, marked External, and added your Google account as a test user so you’re allowed to use it without Google formally “verifying” the app).
Created an OAuth Client ID of type Web application, and listed https://kariocamarron.github.io under Authorised JavaScript origins. This is the security gate: Google will only allow sign-in from that exact address, so nobody can clone your app on a different site and use your credentials.
Pasted the resulting Client ID into the app’s CLIENT_ID line.



We did not use the “Client secret” — this app uses the browser sign-in popup (the “token” flow), which needs only the public Client ID, never a secret.



Step 7 — Make the public file blank, keep data in Drive

Once sign-in worked and your real data had been saved to Drive, we replaced the published index.html with the blank template (section 3). From then on, the public page is harmless, and your data is private.

Step 8 — Use it everywhere

Open the link on any PC or iPhone (Safari → Add to Home Screen to create an app icon), tap Connect to Drive, sign in once, and your data appears and stays in Sync.


5. How to make changes in future

The app file you edit locally is in your folder. To publish a change:


Edit the local index.html (or ask me to).
Go to the paydash repo → Add file → Upload files.
Drag in the new index.html (it replaces the old one) → Commit changes.
Wait ~1 minute, then hard-refresh the site (Ctrl+Shift+R on PC; on iPhone, close the tab and reopen it).


The web address never changes, so your phone icon and bookmarks keep working.


6. Troubleshooting (things we actually hit)

SymptomCauseFix404 — File not foundFilename mismatch / wrong capital letters / Windows shortened the nameUse index.html; check exact spelling and casePage shows plain “paydash” textGitHub was still serving the old README, or the browser cached itWait ~1 min for the rebuild, then hard-refresh (Ctrl+Shift+R)Dashboard loads but Drive won’t connectGoogle needs the exact origin allowed, and new settings take a few minutesConfirm https://kariocamarron.github.io is in Authorised JavaScript origins; wait ~5 min and retry “Google hasn’t verified this app “The app is in testing mode (normal for personal use)Advanced → Continue → Allow (you added yourself as a test user, so this is saf Syncnc stops after a whileSign-in sessions last about an hourClick Connect to Drive again


7. Glossary


Repository (repo): a project folder stored on GitHub.
Commit: saving a change to the repo (each upload is a commit).
GitHub Pages: GitHub’s free website-hosting feature.
Hosting: putting files on an always-on computer so they can be accessed via a public web address.
HTTPS: the secure (https://) version of a web address; required by Google sign-in.
index.html: the default home-page filename a web server serves automatically.
Origin: the domain part of a URL (e.g. https://kariocamarron.github.io).
OAuth / Client ID: Google’s sign-in system and the public key that identifies your app to it.
drive.file scope: a permission that lets the app see only the one file it creates in your Drive — nothing else.
