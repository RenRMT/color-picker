# Palettes

`index.html` shows one palette at a time. Every colour lives in the palette's JSON file; the page itself has none.

| File | What it is |
|---|---|
| `index.html` | The page. No need to edit it to add or change a palette. |
| `palettes.json` | The palettes the switcher offers, in order. The first is the default. |
| `ren.json`, `roos.json`, `rijk.json` | One file per palette. |

Link to a palette with `?p=`, e.g. `/palettes/?p=roos`.

## Adding a palette

1. Copy one of the palette files to `mine.json` and edit it.
2. Add `{ "id": "mine", "name": "My palette" }` to `palettes.json`.
3. Open `/palettes/?p=mine`.

To preview locally, run `python3 -m http.server` in the repository folder and open <http://localhost:8000/palettes/>. Double-clicking `index.html` won't work: browsers don't let a page opened from disk read the JSON files.

Mistakes such as a misspelled colour name or an invalid hex value show up in a red box at the top of the page, and the swatch is drawn hatched.

## Palette file

| Field | |
|---|---|
| `title` | Page heading. |
| `lang` | `en` or `nl`: language of the controls and labels. |
| `intro` | Optional paragraph under the title. |
| `source` | Optional `{ "label": "…", "url": "…" }`. |
| `theme` | Optional look of the page: `font`, `background`, `text`, `muted`, `heading`, `accent`, `rule`, `radius`. Use hex for `text`; it's also the dark caption colour on light swatches. |
| `labels` | Optional overrides for any control text, e.g. `{ "steps": "Aantal" }`. |
| `cssPrefix` | Optional prefix for the CSS export. Defaults to the palette id; `""` for none. |
| `colours` | Named colours: `{ "Primary Teal": "#1C6758" }`. |
| `ramps` | Tint ramps, below. |
| `sections` | What the page shows, in order, below. |

### `ramps`

Ramps always run light → dark. Give each colour either its full list of steps:

```json
"ramps": {
  "steps": ["150", "300", "450", "600", "750", "base"],
  "separator": "-",
  "baseStep": "base",
  "pick": "even",
  "colours": {
    "Hemelblauw": ["#D9EBF7", "#B3D7EE", "#8CC4E6", "#66B0DD", "#409CD5", "#007BC7"]
  }
}
```

or one base colour, and let the page mix the tints:

```json
"ramps": {
  "tints": { "mixWith": "#FFFFFF", "percent": [15, 30, 45, 60, 75, 100] },
  "baseStep": "100%",
  "colours": { "Hemelblauw": "#007BC7" }
}
```

| Field | |
|---|---|
| `steps` | Step names. Default `1`, `2`, `3`… or, with `tints`, the percentages (`15%` …). |
| `separator` | Between colour and step in a name: `"Teal 6"`, `"Groen-750"`. Default a space. |
| `baseStep` | The step named after the colour alone: `"Hemelblauw"`, not `"Hemelblauw-base"`. |
| `pick` | How a scale chooses steps when asked for fewer than the ramp has. `"even"` (default) spreads them evenly, ends included. A list such as `["6", "2", "4", "3", …]` is a priority order: 3 steps takes the first three. |

Every ramp step is also a named colour, so sections can use `"Oranje-750"` or `"Hemelblauw 15%"` directly.

### `sections`

Every section has a `title`, an optional `intro`, and a `type`:

- **no type**: fixed swatches. `"groups": [{ "title": "…", "note": "…", "colours": [ … ] }]`. A colour is a name (from `colours` or a ramp step) or inline `{ "name": "Neutral", "value": "#F9F9F9" }`.
- **`"ramps"`**: each ramp as one row. Add `"colours": [ … ]` to show only some.
- **`"sequential"`**: pick a colour and a number of steps. `"default": { "colour": "Teal", "steps": 7 }`.
- **`"diverging"`**: two ramps around a centre. `"default": { "left": "Rood", "right": "Groen", "steps": 7, "centre": "Grijs-100" }`. `"centre"` lists the centre colours; with more than one the page adds a picker.

`sequential` and `diverging` also take `pick` (overrides the ramp setting) and `from`, a list of the steps they may use. ROOS leaves out its lightest step this way, so the centre of a diverging scale stays the lightest colour.

## CSS variables

**Copy as CSS variables** copies every named colour and ramp step of the current palette as a `:root { --roos-hemelblauw: #007BC7; … }` block, ready to paste into another project.
