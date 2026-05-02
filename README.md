# Wayback CDX Bulk Fetcher

A browser-based tool for retrieving large-scale sitemap and outlink data from the [Internet Archive Wayback Machine](https://web.archive.org/) CDX API. Handles 100,000+ records via paginated requests with no backend required.

Built for forensic web research, digital archiving, and domain analysis.

---

## Features

- **Paginated CDX fetching** using `resumeKey` — reliably walks the full Wayback index regardless of result size
- **Sitemap crawl** — all captured URLs for a domain, collapsed by unique key, with MIME type, HTTP status, and timestamp
- **Outlink extraction** — external URLs referenced or linked from the target domain, de-duplicated and filtered to external-only
- **URL classification** — automatically categorizes results into pages, images, CSS/JS assets, and files (PDF, video, etc.)
- **Date range filtering** — narrow results to a specific archive period
- **Adjustable chunk size** — 5k / 10k / 25k / 50k records per request
- **Live search + kind filter** in the results table
- **Five download formats**: all URLs, images only, outlinks by domain, full CSV, and a structured report `.txt`
- No server, no API key, no install — runs entirely in the browser

---

## Usage

### Hosted (GitHub Pages)

Visit the live tool at:
```
https://[your-username].github.io/[repo-name]/
```

### Local

Clone or download the repo, then open `index.html` in any modern browser. Both `index.html` and `background.jpg` must be in the same directory.

```bash
git clone https://github.com/[your-username]/[repo-name].git
cd [repo-name]
open index.html
```

---

## How It Works

This tool queries the [Wayback CDX Server API](https://github.com/internetarchive/wayback/tree/master/wayback-cdx-server), a public read-only index of all URLs the Internet Archive has ever crawled.

### Sitemap query

```
GET https://web.archive.org/cdx/search/cdx
  ?url=example.com/*
  &fl=original,mimetype,statuscode,timestamp
  &collapse=urlkey
  &output=text
  &limit=10000
  &showResumeKey=true
```

The `collapse=urlkey` parameter deduplicates URLs by their canonical SURT key, so each unique page/asset appears only once. The `showResumeKey=true` + `resumeKey=` pagination chain walks through the complete result set in chunks, making it safe for sites with 100k+ captures.

### Outlink query

```
GET https://web.archive.org/cdx/search/cdx
  ?url=example.com/*
  &fl=original
  &showLinksTo=true
  &collapse=urlkey
  &output=text
```

Outlinks are post-filtered client-side to remove any URLs belonging to the target domain, leaving only external references.

---

## Download Formats

| Format | Contents |
|---|---|
| All URLs `.txt` | One URL per line, all captures |
| Images `.txt` | Filtered to `.jpg`, `.png`, `.gif`, `.webp`, `.svg`, etc. |
| Outlinks by domain `.txt` | External URLs grouped under their hostname |
| Full CSV | All rows with `url, kind, mime, statuscode, timestamp` columns |
| Full report `.txt` | Structured report with summary stats and all sections |

---

## Repo Structure

```
├── index.html       # Main application (self-contained)
├── background.jpg   # Background image (must be in same folder)
└── README.md
```

---

## Notes

- The Wayback CDX API is a public, unauthenticated endpoint. No API key is required, but it is rate-limited. If you receive errors on very large crawls, try reducing the chunk size to 5k.
- The table preview is capped at 2,000 displayed rows for performance. All records are retained in memory and available via the download buttons.
- Outlink fetching can be slow for large sites — disable it with the toggle if you only need the sitemap.
- Date fields accept `YYYYMMDD` format, e.g. `20150101` to `20201231`.

---

## Credits

Built with the [Internet Archive CDX Server API](https://github.com/internetarchive/wayback/tree/master/wayback-cdx-server).  
Background photograph by [Pawel Czerwinski](https://unsplash.com/@pawel_czerwinski) via Unsplash.  
Typography: [Hanken Grotesk](https://fonts.google.com/specimen/Hanken+Grotesk) (Google Fonts).
