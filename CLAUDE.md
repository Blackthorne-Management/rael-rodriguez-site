# CLAUDE.md — Rael Rodriguez personal site

Read this first, every session (cloud or desk).

## Who you're working with
- Rael Rodriguez owns this site. Rael is **not a developer**.
- Talk in plain language. No jargon unless you explain it in a few words.
- Rael often works from a phone (claude.ai/code), so keep replies short and easy to scan.
- After every task, end with a **short recap**: what changed, whether it's live yet, and anything Rael needs to do.
- Before anything hard to undo (deleting files, rewriting history, changing hosting or account settings), ask first.

## Where things live
- **Source of truth:** GitHub, `Blackthorne-Management/rael-rodriguez-site` (private), branch `main`. Work on `main` unless Rael asks for a branch.
  - The old repo `keepingitrael-dev/rael-rodriguez-site` is a frozen backup (the git remote `rael-old` on the desk computer). Don't push to it.
- **Hosting:** Netlify, under the **Blackthorne** Netlify account. The live address is https://raelrodriguez.com.
  - The new Blackthorne site ID isn't recorded yet. Add it here once the site exists.
  - The old site under Rael's own Netlify account (`6eeb842e-9c91-4e0b-a258-a2085c6b7417`) is being retired because it ran out of credits.
- **How it deploys:** Netlify is connected to the GitHub repo and **auto-deploys when `main` is pushed**. Never deploy from the command line (`netlify deploy` etc.). Pushing to GitHub is the deploy.
- **Build:** none. It's plain HTML/CSS/JS, and the publish folder is the repo root (see `netlify.toml`). The only server code is in `netlify/functions/`, and `/api/*` is routed to those functions.
- **Data and services (all inside the same Netlify site, no outside database):**
  - **Netlify Blobs**, store name `income-tracker`: categories and entries for the income tracker (`/tracker`, managed from `/admin`).
  - **Netlify Forms**, form name `coaching-inquiry`: the inquiry form on `coaching.html`. Email notifications for it are set up in the Netlify dashboard.
  - **Secrets are Netlify environment variables**, set in the Netlify dashboard (Site configuration → Environment variables). They are not in any file: `ADMIN_PASSWORD`, `ADMIN_SESSION_SECRET`, `NETLIFY_API_TOKEN`, `SITE_ID`, `GOAL_AMOUNT`, `CHALLENGE_START_DATE`, `CHALLENGE_END_DATE`. Never write their values into the repo or into chat.

## Files that are NOT on GitHub on purpose
Kept out by `.gitignore`. The desk computer backs them up nightly to
`D:\Blackthorne Management\Backups\Rael Rodriguez Site\local-only`.
- `.netlify/`: the desk computer's link to the Netlify site (local state only).
- `node_modules/`: installed packages; can always be reinstalled.
- `.env` (if one is ever created for local testing): must never be committed. The real secrets live in Netlify (see above).
- Photos and logos in `assets/` **are** on GitHub, because the site needs them to display.

## How a change ships
1. `git pull --rebase` first, every time.
2. Make the change.
3. Run the checks:
   - There are no automated tests in this project.
   - If any file in `netlify/functions/` changed, run `node --check` on it to catch syntax errors.
   - Re-read the changed HTML/JS for broken tags, links, and paths. Use relative paths (`assets/...`, `style.css`) like the rest of the site.
4. Commit with a clear one-line message saying what changed, then `git push`.
5. **Verify it's live:** after a minute or two, fetch the changed file from https://raelrodriguez.com and confirm the new content is there. Tell Rael plainly whether it is or isn't live yet.

## Hard rules and product decisions (don't undo these)
- **No build tools or frameworks.** It stays plain HTML/CSS/JS unless Rael asks otherwise.
- **Two visual themes on purpose:**
  - `ugc.html` uses its own crimson/black/cream theme (`ugc-style.css`).
  - Every other page uses navy/gold/cream (`style.css`).
  - Don't merge the two themes.
- **Live page (`live.html`):**
  - The schedule is **Mondays and Thursdays, 7:45–9:30 PM Eastern**, set in `live-status.js`.
  - Times are always America/New_York, whatever time zone the visitor is in.
  - During the schedule, **Twitch and YouTube both show Live together** (`platforms: ["twitch", "youtube"]`). Instagram was removed on purpose (Rael's request, 2026-10-08). Don't add it back or go to a single platform.
  - `live: "auto"` is the normal setting. `true` or `false` is only for a one-off extra or skipped stream, then it goes back to `"auto"`.
- **Pages kept out of search engines:** `live.html`, `admin/`, and `tracker/admin.html` have `noindex, nofollow`. Keep it.
- **Income tracker storage:** in `netlify/functions/_shared/store.js`, `consistency: 'strong'` is left out on purpose because it breaks reads on Netlify. Don't add it back.
- **Coaching form:** it has to keep `data-netlify="true"` and the `name="coaching-inquiry"` attribute, or Netlify stops collecting submissions.
- **Placeholders removed on purpose:** the placeholder "Features" links on `content.html` were deliberately removed. Don't bring them back.
- **The README is partly out of date.** Its "Still needs your real content" list includes things that are already done (headshot, LinkedIn, Features links), and it says `live.html` isn't linked, but `content.html` now links to it. Trust the current code over the README.

## What a cloud session can't do here, and what to do instead
- **No secrets / `.env`:** functions that need the environment variables (admin login, tracker data, forms list) can't be run for real. Check them by reading the code and running `node --check`, then confirm on the live site after deploy.
- **No local preview:** you can't open the site in a browser here. Check the change by reading it carefully, push it, then fetch the live page from https://raelrodriguez.com to confirm. For anything visual (layout, colors, spacing), ask Rael to look on the phone and say what to check.
- **No device testing:** you can't test on a real phone or other browsers. Keep layouts working at phone width (the site stacks on mobile), and ask Rael to check on the phone when a change affects layout.
- **No Netlify dashboard access:** you can't see deploy logs or change settings. If a push doesn't appear live, tell Rael to check the **Deploys** tab at app.netlify.com.

## Known issues
- **Moving to Blackthorne Netlify (in progress, 2026-10-02):** Rael's old Netlify site stopped deploying after Sep 10, probably because it ran out of credits. As a result, raelrodriguez.com still shows commit `228f694`, and later changes (including all three platforms showing Live) aren't live yet.
  - The fix is a new Netlify site under Blackthorne, linked to the Blackthorne repo.
  - The 7 environment variables have to be re-entered there. `NETLIFY_API_TOKEN` and `SITE_ID` must be the new site's values.
  - The domain has to be moved over, and the coaching-form email notification turned back on.
  - The income-tracker data and past form submissions stay on the old site and do **not** move automatically.
  - Remove this note once the move is done.
