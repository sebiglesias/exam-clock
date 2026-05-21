# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Development

No build step. Open `index.html` directly in a browser, or serve it with any static file server:

```sh
npx serve .
python3 -m http.server
```

To test the clock view directly, append query params:

```
http://localhost:PORT/?start=09:00&end=11:00
```

## Architecture

The entire app lives in a single `index.html` — HTML structure, CSS (inlined `<style>`), and JavaScript (inlined `<script>`). There are no external JS dependencies; Google Fonts is the only external resource.

**Two views, one page.** The app has two mutually exclusive screens:

- `#setup` — shown by default; a form where the user enters start/end times
- `#clock-view` — shown when `?start` and `?end` URL params are present

`launch()` encodes the form values into the URL (`window.location.search`), causing a page reload that triggers clock mode. The clock screen never shows without valid URL params.

**Theme system.** A tiny IIFE in `<head>` reads `localStorage` and sets `data-theme` on `<html>` before first paint to prevent flash. A second IIFE in `<body>` wires up the three theme buttons (light / system / dark) and re-applies on OS preference change via `matchMedia`. CSS variables on `:root` and `html[data-theme="light"]` handle the two explicit states; `@media (prefers-color-scheme: light)` handles the system state.

**Clock tick logic** (`tick()`, called every 1 s via `setInterval`):
1. Before start → shows "Starts in X" countdown
2. During exam → fills `#bar-fill` by percentage, shows remaining time
3. After end → adds `.overtime` class (bar turns red, full width), counts up elapsed overtime
