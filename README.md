# dusk-li-data

Data store for [dusk.li](https://dusk.li) — a directory that rates websites on their **dark mode support** and **colour contrast accessibility**.

This repository holds one YAML file per reviewed website. It is consumed as a [Hugo data directory](https://gohugo.io/templates/data-templates/) by the [dusk-li](https://github.com/dusk-li/dusk-li) website and is populated automatically by the [a-scanner-duskli](https://github.com/dusk-li/a-scanner-duskli) scanner.

## Repository structure

```
dusk-li-data/
├── .github/
│   └── workflows/
│       └── update-dusk-li.yml   # Triggers a dusk-li submodule update on push to main
├── en/
│   └── docs/
│       └── sidebar.yml          # Navigation sidebar config for dusk.li
├── websites/
│   ├── example.com.yaml         # One file per reviewed domain
│   └── ...
└── template.yaml                # Blank template for manual submissions
```

## Data format

Each reviewed domain has a corresponding YAML file at `websites/<domain>.yaml`.

### Fields

| Field | Type | Description |
|-------|------|-------------|
| `url` | string | Full URL of the reviewed site (e.g. `https://example.com`) |
| `dark_mode` | enum | `None` \| `Manual` \| `Auto` — level of dark mode support |
| `contrast_accessibility` | enum | `PASS` \| `FAIL` \| `BLOCKED` — WCAG2AA contrast result |
| `accessibility_rating` | string | Score out of 3 (e.g. `2/3`) |
| `last_updated` | date | ISO 8601 date of last review (e.g. `2024-03-15`) |

### Dark mode values

| Value | Meaning | Points |
|-------|---------|--------|
| `Auto` | Site uses JavaScript `window.matchMedia('prefers-color-scheme: dark')` to automatically follow the OS preference | 2 |
| `Manual` | Site uses a CSS `@media (prefers-color-scheme: dark)` rule (theme responds to OS but may require a toggle) | 1 |
| `None` | No dark mode support detected | 0 |

### Contrast accessibility values

| Value | Meaning | Points |
|-------|---------|--------|
| `PASS` | pa11y found zero WCAG2AA errors | 1 |
| `FAIL` | pa11y found one or more WCAG2AA errors | 0 |
| `BLOCKED` | pa11y could not complete the check (site blocked headless browser, timeout, etc.) | 0 |

### Scoring

The `accessibility_rating` is the sum of the dark mode and contrast points, giving a maximum of **3/3**.

### Example

```yaml
---
url: https://example.com
dark_mode: Auto
contrast_accessibility: PASS
accessibility_rating: 3/3
last_updated: 2024-03-15
```

## Contributing

### Requesting a review via GitHub Issues (recommended)

Open an issue in [a-scanner-duskli](https://github.com/dusk-li/a-scanner-duskli/issues/new?template=review-request.yml) using the **Request Site Review** template. The automated scanner will check the site and commit the result here within a few minutes.

> **Note:** The same domain can only be re-reviewed once every 7 days via this method.

### Submitting a pull request manually

Use `template.yaml` as a starting point:

```yaml
---
category: <category name>
url: https://www.example.com
dark_mode: <None|Manual|Auto>
contrast_accessibility: <PASS|FAIL>
accessibility_rating: 0/3
last_updated: YYYY-MM-DD
```

1. Copy `template.yaml` to `websites/<domain>.yaml` (e.g. `websites/example.com.yaml`)
2. Fill in all fields according to the data format above
3. Open a pull request

### Guidelines

- The `url` must be the canonical URL of the homepage (include `https://`)
- The filename must be the bare hostname (e.g. `example.com.yaml`, not `www.example.com.yaml`)
- Use [pa11y](https://pa11y.org/) against the WCAG2AA standard to verify the contrast result
- Use [color.a11y.com](https://color.a11y.com/Contrast/) as a secondary reference

## Automation

### Scanner (`a-scanner-duskli`)

The [a-scanner-duskli](https://github.com/dusk-li/a-scanner-duskli) repository contains a Python scanner that:

- Fetches the site's HTML and checks for dark mode CSS/JS patterns
- Runs pa11y to assess WCAG2AA colour contrast compliance
- Writes the result as a YAML file and commits it to this repo

Scans run automatically each night via GitHub Actions.

### Website update (`update-dusk-li.yml`)

On every push to `main`, the `.github/workflows/update-dusk-li.yml` workflow updates the `data` submodule pointer in [dusk-li](https://github.com/dusk-li/dusk-li), which triggers a Netlify rebuild of the website.

```
dusk-li-data (main) ──push──▶ update-dusk-li.yml ──▶ dusk-li (submodule update) ──▶ Netlify rebuild
```

## License

GPL-3.0 — see the [dusk-li organisation](https://github.com/dusk-li).
