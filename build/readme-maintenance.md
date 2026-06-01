# Partner map — build & maintenance

Interactive partner map for the OSW APAC Convening 2026 one-pager. Click a country
on the map (or a location chip) to filter the list to that country; the
**Organisation type** dropdown filters by survey category. The two filters combine.

## Files

- `index.html` — the app. Map (Leaflet + CartoDB Positron tiles), the linked
  filtering, and the card list. You normally never edit this.
- `orgs.json` — the data. **This is the only file you touch to maintain the map.**

The app reads `orgs.json` at load time, so the map, the location chips, the type
dropdown and the result counts all rebuild themselves from the data. Add a row and
everything updates — no code changes.

## Adding or editing an organisation

Open `orgs.json` (on GitHub: open the file → pencil icon → edit in the browser).
Each organisation is one object:

```json
{
  "name": "Organisation name",
  "country": "Korea",
  "type": "Research / think tank",
  "url": "https://example.org",
  "topics": ["Marine spatial planning", "Finance & de-risking"]
}
```

- **country** — use `Japan`, `Korea`, `Taiwan`, or `Regional`. For a country to get a
  map marker it must exist in the `COUNTRY_GEO` table near the top of `index.html`
  (Japan, Korea and Taiwan are already there with coordinates). `Regional` has no
  marker by design and shows only as a chip. To add a brand-new country, add one
  line to `COUNTRY_GEO` with its lat/long.
- **type** — keep to the deployed survey categories so the dropdown stays clean:
  `CSO / NGO`, `Research / think tank`, `Foundation / donor`. A new value just adds
  a new dropdown option, so only introduce one deliberately.
- **url** — leave as `""` if not yet known; the card then shows a plain name with no
  link.
- **topics** — free list of strings; they render as chips on the card.

Commit the change. GitHub Pages republishes within about a minute.

## Consent gate

Only add an organisation once it has agreed to appear publicly. The manual commit is
the review step — nothing reaches the live page until you push it.

## Previewing locally

`fetch()` needs a server, so opening `index.html` straight from disk shows an error.
From this folder run `python3 -m http.server`, then visit
`http://localhost:8000/`. On GitHub Pages it just works.

## Deploying

Push `index.html` and `orgs.json` to the GitHub Pages repo, then embed the published
URL in a Squarespace Code Block:

```html
<iframe src="https://<user>.github.io/<repo>/" width="100%" height="900"
        style="border:0;" loading="lazy"></iframe>
```

Squarespace does not auto-size iframes; adjust `height` once you see the rendered
page.

## Current data note

Seeded with the seven consented form respondents (Korea 3, Japan 2, Regional 2; no
Taiwan respondents in this wave). The brand red in `index.html` (`--iri-red`) is a
placeholder — set it to the exact brand hex before publishing.
