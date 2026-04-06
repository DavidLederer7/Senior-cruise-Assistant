# Contributing

Thanks for your interest in improving the Ticket Drop Assistant. This document
covers the project's scope, how to set up a dev environment, and the conventions
to follow when sending changes.

## Scope: manual-assist only

This is the single most important rule. The assistant exists to help a person be
**ready** the instant a ticket drop goes live — countdown, reminders, a local
dashboard, clipboard helpers, and quick access to the event page. That's it.

Pull requests will **not** be accepted if they add automation that:

- clicks purchase / checkout controls,
- submits payment or completes an order,
- solves, bypasses, or relays CAPTCHAs,
- circumvents queue, rate-limit, or other platform restrictions, or
- otherwise acts on the user's behalf without a human in the loop.

The optional Eventbrite helper (`helper/eventbrite_helper.js`) may **highlight**
relevant fields, but it must never click anything. Keep new features on the
right side of that line.

## Requirements

- **macOS** — the assistant shells out to `osascript` (notifications, browser
  focus), `pbcopy` (clipboard), and `open` (launching the browser).
- **Python 3.9+** — the code uses `zoneinfo`, `from __future__ import annotations`,
  and PEP 604 (`str | None`) type hints.
- **No third-party dependencies.** Standard library only. Please don't add a
  `requirements.txt` or pull in packages; keeping it dependency-free is what lets
  people run it with a double-click.

## Setup

```bash
git clone https://github.com/DavidLederer7/Senior-cruise-Assistant.git
cd Senior-cruise-Assistant
python3 senior_cruise_assistant.py --setup     # fill in event + personal details
python3 senior_cruise_assistant.py --dry-run    # print the checklist and exit
python3 senior_cruise_assistant.py --open-now    # start the live assistant
```

`--setup` writes your answers to `senior_cruise_config.json`.

## Never commit your personal config

`senior_cruise_config.json` holds personal data — your promo code, UNI/school ID,
and the specific event URL — and is **gitignored** for that reason. Only commit
`senior_cruise_config.example.json`, which must stay generic and safe to share.

If you add a new config field, add it to **both** the `DEFAULT_CONFIG` dict in
`senior_cruise_assistant.py` and `senior_cruise_config.example.json`, with a
placeholder (not a real) value.

## Code style

- Match the existing style: small, single-purpose functions, `@dataclass` for
  config, type hints on signatures.
- Keep platform calls (`osascript`, `pbcopy`, `open`) isolated in their helper
  functions so the scheduling logic stays testable.
- No new external dependencies (see above).

## Testing your change

There is no automated test suite. Before opening a PR, verify manually:

1. `python3 senior_cruise_assistant.py --dry-run` prints the checklist cleanly.
2. Set `drop_iso` a couple of minutes out in your local config and confirm the
   countdown, the macOS notifications at each offset, the dashboard, and the
   browser-open behavior all fire at the right times.
3. The dashboard loads at `http://127.0.0.1:8765/dashboard/index.html` (and the
   file-URL fallback works if the port is taken).

## Commits and pull requests

- Keep commits focused and write present-tense, imperative messages
  ("Add backup-tab offset", not "Added...").
- Never commit `senior_cruise_config.json`, `.DS_Store`, `.pycache/`, or build
  artifacts like `ticket-drop-assistant.zip` — they're gitignored; please keep
  it that way.
- In the PR description, say how you tested the change (see above).

## About `shareable-release/`

`shareable-release/` is a **separate, independently-versioned git repository**
(the public `ticket-drop-assistant` distribution) that lives inside this folder
for convenience. It is gitignored here on purpose — don't commit it into this
repo. Make changes to it from within that directory, against its own remote.
