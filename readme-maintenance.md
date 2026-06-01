# Partner map — build & maintenance

Interactive partner map for the OSW APAC Convening 2026 one-pager. The map is a
**fixed** (non-pan, non-zoom) choropleth of East Asia: each country is filled as a
whole block, shaded by how many organisations it holds. Click a country (or a
location chip) to filter the list to it; the **Organisation type** dropdown filters
by survey category. The two filters combine.

## Files

- `index.html` — the app (map, linked filtering, card list). You normally never edit
  this.
- `orgs.json` — the organisation data. **This is the main file you touch.**
- `countries.geojson` — the country outlines used for the choropleth (Japan, Korea,
  Taiwan plus China / North Korea / Philippines as visual context). You rarely touch
  this.

The app reads `orgs.json` and `countries.geojson` at load, so the choropleth shading,
the location chips, the type dropdown and the counts all rebuild from the data. Add a
row to `orgs.json` and everything updates.

## Adding or editing an organisation

Open `orgs.json` (on GitHub: open the file → pencil → edit in the browser). Each
organisation is one object:

```json
{
  "name": "Organisation name",
  "country": "Korea",
  "type": "Research / think tank",
  "url": "https://example.org",
  "topics": ["Marine spatial planning", "Finance & de-risking"]
}
```

- **country** — use `Japan`, `Korea`, `Taiwan`, or `Regional`. Japan, Korea and Taiwan
  are drawn as coloured country blocks; their shade deepens as more organisations are
  added (1–2 / 3–4 / 5+). `Regional` has no polygon by design and appears only as a
  chip. To colour a brand-new country you must add its outline to
  `countries.geojson` with a `"key"` matching the value you use here (see below).
- **type** — keep to the deployed survey categories so the dropdown stays clean:
  `CSO / NGO`, `Research / think tank`, `Foundation / donor`. A new value just adds a
  dropdown option, so only introduce one deliberately.
- **url** — leave as `""` if unknown; the card then shows a plain name with no link.
- **topics** — free list of strings; they render as chips on the card.

Commit the change. GitHub Pages republishes within about a minute.

## Adding a new country to the map (rare)

`countries.geojson` already contains Japan, Korea and Taiwan as clickable blocks. To
add another (say the map later needs Vietnam), add that country's polygon as a feature
with `properties: { "name": "Vietnam", "key": "Vietnam" }`, then use `"country":
"Vietnam"` in `orgs.json`. Context-only countries (no organisations) carry an empty
`"key": ""`. The fixed view auto-frames Japan, Korea and Taiwan; if you want the frame
to include the new country, add its key to the `focusKeys` set in `index.html`.

## Consent gate

Only add an organisation once it has agreed to appear publicly. The manual commit is
the review step — nothing reaches the live page until you push it.

## Previewing locally

`fetch()` needs a server, so opening `index.html` straight from disk shows an error.
From this folder run `python3 -m http.server`, then visit `http://localhost:8000/`.
On GitHub Pages it just works.

## Deploying to GitHub Pages, then Squarespace

1. Put `index.html`, `orgs.json` and `countries.geojson` in a GitHub repo (all in the
   same folder — the repo root is simplest).
2. Repo **Settings → Pages → Build from a branch → main → /(root) → Save**. After a
   minute the page is live at `https://<user>.github.io/<repo>/`. Open that URL to
   confirm it works.
3. In Squarespace (Business tier or higher, which allows code embeds), edit the
   one-pager, add a **Code Block** where the map should sit, and paste:

   ```html
   <iframe src="https://<user>.github.io/<repo>/"
           width="100%" height="900" style="border:0;" loading="lazy"
           title="OSW APAC partner map"></iframe>
   ```

   Squarespace does not auto-size iframes, so set `height` to fit the rendered content
   (≈900 px is a sensible start; raise it if cards are cut off) and adjust once you see
   it on the page.

## Current data note

Seeded with the seven consented form respondents (Korea 3, Japan 2, Regional 2; no
Taiwan respondents in this wave — Taiwan therefore shows unshaded until a respondent
is added). The brand red in `index.html` (`--iri-red`, plus the choropleth tints
`--fill-1..3`) is a placeholder — set it to the exact brand hex before publishing.
