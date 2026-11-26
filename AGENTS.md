# Agent Instructions: revealandremember.com

These instructions are for any AI coding agent (Claude Code, Codex, etc.) working in this repo.

## Who you're working with

Most of the development on this site is done by **Emily**, the site owner. She is **not a software engineer**. She will describe what she wants in plain language ("change the photo on the About page", "push it live") and won't ask for branches, PRs, builds or cleanups by name.

You are responsible for following a professional development workflow **on her behalf**, every time, without being asked. That workflow is described below. Treat it as the default; don't skip steps because she didn't mention them.

When you talk to Emily:

- Use plain language. Say "a preview version of the site" rather than "a Vercel preview deployment for the feature branch". Mention technical terms only when they help her.
- Tell her what she can **see and click**: the preview link, which page changed, what to check on her phone.
- If a request is ambiguous in a way that changes what happens to the live site, ask a short, plain question before acting (see "Interpreting what Emily says").
- Don't ask her to make engineering decisions she can't reasonably make. Pick the standard, conservative option and briefly say what you did.

## Stack (quick reference)

- [Astro](https://astro.build), static output (`dist/`). See `README.md` for project structure and where content lives, and **`STYLE_GUIDE.md` for how styling works**.
- Hosted on **Vercel**, connected to this GitHub repo (`emfoxgh/revealandremember.com`).
  - Every pushed branch gets an automatic **preview deployment** at its own URL.
  - `master` is **production**: whatever is on `master` is live at https://revealandremember.com.
- Default branch: **`master`** (not `main`).
- Commands: `npm run dev` (local server at http://localhost:4321), `npm run build` (production build).

Note: `codex.md` is a generic "landing page builder" prompt for a React/Tailwind/GSAP stack. It does **not** describe this project. Don't follow its technical requirements or agent flow here.

## The workflow

### 1. Starting a change

When Emily asks for any change to the site (text, images, styling, new page, etc.):

1. Make sure the working tree is clean. If it isn't, find out why before doing anything else (an unfinished change from earlier?), and ask Emily if it's unclear whether it belongs to this request.
2. Check for an existing, unmerged branch of hers that this request obviously continues (e.g. "also change the heading on that page"). If there is one, keep working on that branch. If you're not sure, ask: "Is this part of the About page update you're working on, or a separate change?"
3. Otherwise, start fresh from an up-to-date `master`:
   ```bash
   git switch master && git pull --ff-only
   git switch -c feature-<short-description>
   ```
   Use the existing naming convention: `feature-<kebab-case-description>`, e.g. `feature-contact-page`, `feature-update-podcasts`.

**Never commit directly to `master`.** All changes reach `master` through a pull request.

### 2. Making the change

- Make the edit and check it on the local dev server (`npm run dev`), on both **desktop and mobile widths**. The site has responsive layouts (collapsing nav, stacking columns); don't break them.
- **Follow `STYLE_GUIDE.md` for every visual change.** Build new sections and pages from the shared tokens and classes (section backgrounds, heading and text classes, cards, buttons, `PageHeader`). Don't hard-code colors, fonts or font sizes, and don't restyle one page in a way that makes it differ from the others. If Emily asks for a look that the shared classes can't produce, say whether it should apply site-wide or only to that spot before you build it.
- Keep changes focused on what Emily asked for. If you notice something else worth fixing, mention it to her instead of quietly bundling it in.
- Put images in `public/images/` with sensible lowercase, hyphenated filenames, and always write meaningful `alt` text.
- Don't add, remove or upgrade dependencies, change `astro.config.mjs`, or touch Vercel/DNS settings unless Emily specifically asks. If a request seems to need any of that, explain it to her in plain terms and suggest she check with Colton first.

### 3. Saving and previewing (push to the branch)

When the change is ready for Emily to look at, or she asks to "save", "push", "upload", or "let me see it":

1. Run `npm run build` and make sure it succeeds. Don't push a broken build.
2. Commit with a clear, human-readable message describing *what changed on the site* (e.g. "Update About page photo and intro text").
3. Push the branch: `git push -u origin <branch>`.
4. If there's no pull request for the branch yet, open one against `master`:
   ```bash
   gh pr create --base master --title "<Plain-language title>" --body "<What changed and why, in plain language>"
   ```
   Opening the PR early is fine and expected. It's where Vercel posts the preview link. **Opening a PR does not make anything live.**
5. **Get the Vercel preview link and give it to Emily.** Vercel takes about a minute to build. You can find the link either way:
   - The Vercel bot comment on the PR (`gh pr view --comments`), which has a "Preview" link, or
   - GitHub's deployments API for the pushed commit:
     ```bash
     SHA=$(git rev-parse HEAD)
     ID=$(gh api "repos/emfoxgh/revealandremember.com/deployments?sha=$SHA" --jq '.[0].id')
     gh api "repos/emfoxgh/revealandremember.com/deployments/$ID/statuses" --jq '.[0] | "\(.state) \(.environment_url)"'
     ```
     Wait until the state is `success`. If it's `failure`, look into the build error and fix it before reporting back.
   
   Then tell Emily something like: "Here's a preview of your change: <link>. It isn't live on your real website yet. Have a look on your phone and computer, and tell me when you want it live."

   If you can't get the link (e.g. Vercel is slow or the deploy failed), say so plainly and don't make up a URL.

### 4. Going live (merge to `master`)

When Emily says **"push it live"**, "make it live", "publish", "put it on my website", "ship it", or similar, that means **merge the pull request into `master`**, which triggers Vercel's production deployment.

1. Make sure everything is committed and pushed and the PR's checks/Vercel preview succeeded:
   ```bash
   gh pr checks <number>
   ```
   Then add an entry to the top of `SITE_HISTORY.md` on the branch (see "Site history and bookmarks" below), commit it and push.
2. Squash-merge the PR and delete the branch **on GitHub and locally** (squash is this repo's existing convention):
   ```bash
   gh pr merge <number> --squash --delete-branch
   ```
3. Bring local `master` up to date and clean up stale references:
   ```bash
   git switch master && git pull --ff-only && git fetch --prune
   ```
   Confirm the feature branch no longer exists locally (`git branch`) or on the remote (`git branch -r`). Delete it by hand if `gh` didn't.
4. Confirm the production deployment succeeded (same deployments API as above, using the new `master` commit SHA; the environment will be `Production`). Then tell Emily it's live at https://revealandremember.com. Mention that her browser may need a refresh to show it.
5. Bookmark the new live version with a dated tag on the new `master` commit, and push the tag (tags don't change the site):
   ```bash
   git tag -a live-YYYY-MM-DD -m "<plain-language summary>" && git push origin live-YYYY-MM-DD
   ```
   If that date's tag already exists, add `-2`, `-3`, etc. The tag name must match the `SITE_HISTORY.md` entry.

Only merge when Emily has **clearly asked for it to go live**. Never merge on your own initiative, and never treat "save", "push", "commit" or "preview" as permission to go live.

### 5. Site history and bookmarks

`SITE_HISTORY.md` is Emily's plain-language record of every version that has gone live, newest first. Every go-live gets one entry, written in step 4.1 before merging:

- **Heading:** the date and a short name for the version (e.g. "November 26, 2026: Guided-healing homepage").
- **Bookmark:** the tag name you will create in step 4.5 (`live-YYYY-MM-DD`).
- **See it:** the Vercel preview link for the branch's latest site change (it shows the same site that is about to go live, and stays available).
- **What changed:** 2–5 plain-language bullets about what a visitor would notice. No file names or code terms.

**Restoring an old version.** When Emily says something like "put the site back to how it looked on November 25th":

1. Find the matching entry and bookmark in `SITE_HISTORY.md` (or `git tag -l 'live-*'`). If the date is ambiguous, show her the closest entries and ask which one.
2. Start a fresh branch from `master` (e.g. `feature-restore-2026-11-25`) and bring back that version's site files, keeping the current agent instructions and history:
   ```bash
   git restore --source live-2026-11-25 -- src public astro.config.mjs package.json package-lock.json
   ```
3. Build, then go through the normal preview step and give her the preview link. Only go live when she asks, and add a new `SITE_HISTORY.md` entry for it ("Restored the November 25 version").

If Emily needs it back immediately, she can also use **Instant Rollback** in the Vercel dashboard (Deployments, then pick the version). If she does, bring `master` back in line afterwards with the restore steps above, so the next change doesn't undo her rollback.

## Interpreting what Emily says

| Emily says something like... | What it means |
|---|---|
| "Change X", "Update Y", "Add a section for Z" | Do the work on a feature branch (step 1–2). |
| "Save this", "Push it", "Let me see it", "Can I see a preview?" | Commit, push the branch, open/update the PR, give her the **preview** link (step 3). **Not** live. |
| "Push it live", "Make it live", "Publish", "Put it on my site" | Merge the PR into `master`, delete the branch locally and on GitHub, confirm production (step 4). |
| "Undo that", "Go back" | Revert on the feature branch if it's not live yet. If it's already live, make a new branch that reverts the change and go through preview → live again. Never rewrite `master` history. |
| "Put it back to how it looked on <date>", "Restore the <date> version" | Follow "Restoring an old version" in section 5: new branch, preview first, live only when she asks. |
| "Start over" / "Forget this change" | Ask before discarding anything. If she confirms, close the PR and delete the branch locally and on GitHub. |

If "push" is genuinely ambiguous, ask: *"Do you want to see a preview first, or make it live on revealandremember.com now?"*

## Guardrails

- Never commit directly to, force-push to, or rewrite history on `master`.
- Never force-push a branch with an open PR unless you have a specific reason, and explain it if you do.
- Never commit secrets, API keys, `.env` files, `node_modules/`, or `dist/`.
- Commits should use the git identity already configured on the machine (Emily's GitHub noreply email). Don't change git config or put a personal email address into commits or files.
- Don't delete branches that aren't part of the current change without asking. Mention stale ones to Emily instead.
- If something goes wrong (build failure, merge conflict, failed deploy, the live site is broken), stop, explain what happened in plain language, and fix it through the same branch → preview → merge flow. If it's beyond a routine fix, suggest she loop in Colton.
- Keep `README.md` accurate when you change project structure (new pages/routes, new shared components).
