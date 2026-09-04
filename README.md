# Instamart WBR — live dashboard

A single-page, **pure static** Weekly Business Review dashboard for Instamart. No backend,
no build step, no API keys. It reads a Google Sheet directly in the browser and renders
live numbers, in the format of the Blinkit WBR reference.

## How the data flows

1. **Control layer — the template workbook** (`instamart_source_template1`, shared *anyone with
   the link*). It holds a `Config` tab and 16 "grain" tabs. Each grain's **row 2** holds the
   link to the real source sheet (`sheet_url`, `tab_name_in_sheet`, `status`, `notes`).
   Read in-browser via Google **gviz JSONP** (a `<script>` tag — bypasses CORS).
2. **Data layer — the source sheets** (each *Published to web*). The dashboard fetches each
   source's **CSV export** directly and aggregates it in the browser.

> Published-sheet CSV can be fetched cross-origin only from an **https origin** (i.e. the
> deployed Netlify site). Opening `index.html` from a local file shows the shell + Config but
> the source sections report "works on Netlify, not local file". That's expected.

Nothing is invented: a grain whose row 2 still holds the template placeholder
(`EXAMPLE_SHEET_ID`) renders as **"not configured"** until a real link is added.

## Status (built so far)

| Section | Source grain(s) | State |
|---|---|---|
| Sources strip | all 16 | live (shows each grain's load state) |
| Availability | Availability_Weekly (+ Stock) | **live** — OSA by city / SKU type, WoW |
| Marketing | Marketing, Keywords_Monthly | **live** — spend, GMV, ROI, SOV |
| P&L, Revenue, JBP, Decisions | PnL, Categories, JBP, … | next — placeholders for now |

6 of 16 grains currently have real source links; the other 10 are placeholders to fill in
the template sheet later. Add a link and the section lights up automatically — no code change.

## Files

- `index.html` — the dashboard (design system + live data layer, one file).
- `connection-test.html` — a standalone diagnostic that reads the template and reports which
  grains are wired vs placeholders.
- `netlify.toml` — static-site config.

## Deploy

Static site. On Netlify: **Publish directory = `.`**, no build command. Pushing to the
connected GitHub repo auto-deploys.
