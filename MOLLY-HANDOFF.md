# Molly's Note Goat — handoff seed

Read this whole file before touching anything. It's the working context
for continuing this deployment. This repo is **public** — see the rule
below before adding anything to it.

## The one hard rule

**Never add personal or family details, diagnoses, or anyone's health/
care specifics to this repo.** This is a public, indexable GitHub repo.
"Molly" appearing in the app's own branding is the one accepted exception,
already decided. Everything else about who she is, who she cares for, or
any specifics of that care stays out of version control entirely — keep
it in conversation with the user, never in a committed file. An earlier,
more detailed version of this handoff (written when this lived as a
branch of a private repo) did name those details; it was not carried
forward when this repo was created, and no future version of this file
should reintroduce them.

Also never commit an API key, OAuth Client ID, passcode, or any other
credential — this app is BYOK by design (see below); nothing like that
should ever need to appear in code.

## What this is

A customized deployment of the Note Goat app/codebase for Molly, used to
track care-related information for a family member. Scope is deliberately
limited to that one purpose — don't expand it on your own initiative;
check with the user first.

## Where the work lives

- Repo: `laustenfaund/MollyNoteGoat` — a standalone public repo, **not** a
  fork of and not git-linked to the original Note Goat codebase. It was
  split out from a private repo's working branch, which has since been
  retired; this repo is now the sole home for Molly's version.
- Branch: `main`. Push directly here.
- Live at: `https://laustenfaund.github.io/MollyNoteGoat/` (GitHub Pages,
  deployed from `main`, `/` root).
- **Critical constraint:** `localStorage` is scoped by origin only, never
  by path. Every GitHub Pages project site under the `laustenfaund`
  account shares one storage bucket. Never let any other repo under that
  account have GitHub Pages enabled at the same time this one does — it
  already caused a real incident (another repo's saved credentials showed
  up in this app's Settings on a phone that had visited both sites).
  Before doing anything else GitHub-Pages-related, confirm no sibling
  repo has Pages on.

Read `DESIGN.md` in this repo before making structural changes — a lot of
"obvious" simplifications in this app were already considered and
rejected on purpose; it has the reasoning.

## What's already done

- Branding renamed to "Molly's Note Goat" throughout `index.html` and
  `manifest.json`. Left unchanged on purpose: the backup-file-format
  identity string (`'not a Note Goat backup file'`) and the AI
  module-design system prompt — both are internal/format identifiers,
  not user-facing display.
- Manual masthead personalized: eyebrow "For Molly", warm direct-address
  lede (not generic software-doc language). Don't revert to generic
  third-person copy.
- Every manual section has a `<p class="detail-simple">` plain-language
  paragraph plus the original technical walkthrough wrapped in
  `<div class="detail-full">`. A toggle in the masthead ("Simple" / "Full
  detail") flips a `mode-full` class on `<body>`, default Simple,
  remembered in `localStorage` under `noteGoatManualMode`. Keep both
  versions in sync when editing any section.
- Calendar-sync reminders + optional Time field on Appointments-style
  modules.
- BYOK only — no passcode-gated proxy variant exists in this repo. Molly's
  Anthropic key and Google credentials are entered directly into Settings
  by the person who has them; nothing routes through a shared backend.
- Medical modules seeded by default on a fresh install: Doctors,
  Appointments, Medications (with a frequency/time-of-day schedule),
  Referrals, Notes to Bring, Symptom & Behavior Log, and Emergency &
  Family Contacts — alongside the generic app's usual defaults (Next
  Steps, Unanswered Questions, Personal Notes).
- Output-token ceiling on the Anthropic call raised to a flat 4096 across
  all call sites, so AI responses don't truncate.
- App icon: the Noto Emoji goat (Apache 2.0 / SIL OFL 1.1, © Google LLC —
  see `icons/CREDITS.md`), composited on the app's own striped
  background, safe-zone cropped for maskable use.
- Sidebar/main background redesign: `.shell`/`.sidebar`/`.main` no longer
  have their own solid fill — a muted, straight-edged stripe background
  runs continuously behind the interface, with only "modules" (nav items,
  cards, tables) keeping solid color chips on top. Sidebar title card:
  the old decorative watercolor layer is gone, title shortened to fit one
  line, goat icon added to its left (transparent background, height
  matched to the title pill).
- Manual masthead's decorative stripe recolored to match the same muted
  palette used elsewhere.
- Book/gear glyphs removed from the sidebar's Manual/Settings buttons
  (text-only labels now); the per-module ⚙ "edit this module" icon is
  unaffected.

A full audit (current files + entire git history of this repo) has been
run and confirmed clean: no API keys, no OAuth credentials, no diagnosis
or family details anywhere in this repo, past or present.

## What's NOT done — and whose job it is

These need the account owner's own Google account, and physical access to
Molly's device. Flag these as the user's steps; do not attempt them, and
never enter an API key or credential into a field yourself even if asked
to.

1. Get an Anthropic API key and enter it into Molly's Settings, once, on
   her actual device — after that one-time entry it persists in that
   browser's `localStorage`.
2. Create a dedicated Google account for Molly's sync (separate from the
   owner's own account). Under it: a Google Cloud project,
   Sheets/Docs/Calendar/Drive APIs enabled, OAuth consent screen
   (External, Testing — note the ~100-user cap and that test-user grants
   expire and need re-consent roughly every 7 days), an OAuth Client ID
   (Web application, authorized origin `https://laustenfaund.github.io`)
   and an API key restricted to those four APIs.
3. Enter the Client ID + API key (step 2) into Molly's Settings, same as
   step 1.
4. Log Molly's browser into the dedicated Google account (a normal web
   login, not a Chrome profile) so "Connect / Sign in to Google" is a
   quick pick, not a full login each time.
5. Decide how the app actually reaches her device — a plain local file
   breaks Google Sync and PWA install; the live Pages URL (above) is the
   working path — she opens it and uses "Add to Home Screen."

## Architecture constraints — do not "fix" these

- Sync is one-way, push-only, on demand. Never read anything back from
  Google. Deliberate privacy/simplicity decision, not a gap.
- The Google OAuth token is never persisted across a page reload
  (`googleRt.accessToken` lives only in a JS variable, on purpose — see
  DESIGN.md "Sync"). Don't add refresh-token storage or any mechanism to
  keep someone signed in across reloads — doing that safely needs a
  client secret + backend, which breaks the whole single-file,
  no-backend model this app family is built on. The recurring "click
  Connect" friction is an accepted, known tradeoff, not something to
  engineer around.
- BYOK, both sides: Molly's Anthropic key and her Google Cloud
  project/OAuth credentials. There's no shared-connection equivalent for
  either — sync writes to the signed-in person's own
  Drive/Sheets/Calendar/Docs, so a shared credential would blur whose
  data is whose, and none should be built.

## Tone / design principles to preserve (DESIGN.md has the full reasoning)

- Home page: four calming cards, not a dashboard. No bare counts, no
  gamification, no punitive framing for overdue items, no
  therapy-coded copy, nothing that "announces who it's for."
- Visual motif is inspired by textile artist Kaffe Fassett — keep that
  association in mind for any future visual changes; don't swap it for
  something generic without asking.
- Molly should be able to customize this the same way the original app
  allows anyone to — create modules, add/edit fields, build links,
  through the Library and each module's ⚙. If a manual or UI text
  anywhere implies she can't create or edit modules herself, that's
  wrong; fix it.
- A "patch-bay" style visual redesign of inter-module links was discussed
  and explicitly put on hold pending real usage feedback — don't build it
  unprompted.

## Parked ideas — discussed, not built

- **In-app workspace switcher.** Discussed whether Molly could use the
  app for a second, unrelated purpose alongside this one. Today there's
  no separation: every module's data lives under one `localStorage` key
  (`STORE_KEY = 'tc_state_v1'`), shared across the whole Library — a
  second purpose would just mix into the same pool of modules, not sit
  apart from it. A real second setup exists today with zero code changes:
  a second browser profile (or browser) on her device pointed at the same
  URL. An actual in-app switcher — namespacing storage per workspace plus
  a UI to pick one — would be a real change to the persistence layer, not
  a config flip. Pinned for later; don't build it unless asked for again.

## If you're starting a brand-new thread with this file

Paste this whole file as your first message, or point the thread at it
(`git show origin/main:MOLLY-HANDOFF.md` in this repo) and ask it to read
before doing anything else.
