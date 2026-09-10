# Molly's Note Goat

A single-file, browser-only, domain-agnostic personal organizer. No
account, no server — everything you enter stays in this browser
(`localStorage` for module/record data, IndexedDB for file/photo bytes).

## Using it

Just open `index.html`. Add modules from the **Library** in the sidebar —
pick a template, describe what you need and let AI draft it, or build one
by hand.

**AI capture** (Quick Capture's "Clean up with AI", AI-assisted module
setup) needs an Anthropic API key under **Settings**. **Sync** (push a
module to Google Sheets, Docs, or Calendar) needs your own Google Cloud
project's OAuth Client ID + API key, also under Settings. Both are stored
only in this browser and used only for the direct calls this app makes to
Anthropic's and Google's own APIs — nothing routes through a server of
ours.

Click **Manual** in the sidebar for a full walkthrough of every feature,
with a Simple/Full-detail toggle.

## Installing on your phone

1. Open this app's live URL (not the GitHub repo page) in your phone's
   browser.
2. Use "Add to Home Screen" / "Install app" from the browser menu.

That gives it a real icon and name on your home screen, plus a minimal
offline shell — data still lives only in that browser.

## License

MIT — see `LICENSE`.

<!-- trigger initial Pages build -->
