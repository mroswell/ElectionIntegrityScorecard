# State Election Integrity Scorecard

An interactive scorecard evaluating U.S. states across seven pillars of election administration. Scores are 0–3 per criterion with per-cell rationale notes, sources, and verification status.

## Files

- **`election-integrity-scorecard.html`** — the single-page interactive scorecard. Sortable, filterable, multiple color schemes.
- **`scorecard-data.json`** — all the data. This is what you edit to update scores.

## How to run it

The HTML page fetches `scorecard-data.json` at runtime, so **it cannot be opened directly with `file://`** — browsers block `fetch()` on local files for security reasons. You need to serve it over HTTP.

### Option A: GitHub Pages

Drop both files in a GitHub repository, enable Pages (Settings → Pages → Source: main branch), and the scorecard will be live at `https://<username>.github.io/<repo>/election-integrity-scorecard.html`.

### Option B: Local development

```bash
cd /path/to/folder
python3 -m http.server 8000
# Then open http://localhost:8000/election-integrity-scorecard.html
```

## Editing the data

All scores and notes live in `scorecard-data.json`. The structure:

```json
{
  "schema_version": "1.0",
  "criteria_definitions": {
    "stf": {
      "name": "Staffing",
      "description": "Adequate, trained personnel...",
      "indicators": ["Dedicated state funding streams", "..."]
    },
    ...
  },
  "scoring_scale": {
    "3": { "label": "Strong", "description": "..." },
    "2": { "label": "Adequate", "description": "..." },
    "1": { "label": "Weak", "description": "..." },
    "0": { "label": "Absent/Harmful", "description": "..." }
  },
  "confidence_levels": {
    "placeholder": "Illustrative value; not sourced",
    "secondary": "Based on secondary sources (news, advocacy orgs)",
    "primary": "Verified against primary sources (statutes, state election websites)"
  },
  "states": [
    {
      "state": "Alabama",
      "region": "South",
      "criteria": {
        "stf": {
          "score": 1,
          "note": "Limited state funding; no mandatory training statute",
          "sources": [],
          "last_verified": null,
          "confidence": "placeholder"
        },
        ...
      }
    },
    ...
  ]
}
```

### Updating a single cell

Find the state and criterion in `states[].criteria[key]` and edit:

```json
"aud": {
  "score": 3,
  "note": "Risk-limiting audits mandated by statute since 2018; Election Code §X.Y.Z",
  "sources": [
    {
      "url": "https://example.gov/election-code/audit",
      "title": "State Election Code — Audit Provisions",
      "accessed": "2026-04-21"
    }
  ],
  "last_verified": "2026-04-21",
  "confidence": "primary"
}
```

### Confidence levels (and what they render as)

| Level | Meaning | Visual indicator |
|---|---|---|
| `placeholder` | Unverified illustrative value | Small red dot on the score cell |
| `secondary` | News articles, advocacy reports, NCSL | Small orange dot |
| `primary` | State statutes, official elections pages | No dot (clean cell) |

### Adding a criterion

1. Add an entry to `criteria_definitions` (the JS preserves key order, so put it where you want the column to appear)
2. Add the matching `{ score, note, sources, last_verified, confidence }` block to every state's `criteria` object
3. Add a new `<th data-sort="KEY">` to the table header in the HTML

### Adding a state

Add a new entry to `states[]` following the same shape. No HTML changes needed — rows are generated from the data.

## Criteria

| Code | Name | Focus |
|---|---|---|
| `stf` | Staffing | Funding, training, protection from harassment |
| `mch` | Voting Machines | Paper-based systems, certification, no internet-connected tabulators |
| `dsc` | Official Discretion | Constraints on local officials decertifying or delaying |
| `aud` | Audit Procedures | Risk-limiting audits, chain-of-custody, transparency |
| `ppr` | Paper Trail | Voter-verifiable paper records, retention, storage |
| `rol` | Voter Rolls | List maintenance, due process, ERIC participation |
| `box` | Ballot Logistics | Drop box security, chain of custody, signature verification |

## Features

- **Sort** by any column (click header)
- **Filter** by state name or region
- **6 color schemes** (Editorial, ColorBrewer YlOrBr, ColorBrewer PRGn, Viridis, Monochrome, Earth) — switcher in the controls bar. Your choice persists in the URL so you can share a link with a specific scheme.
- **Export CSV** of all scores
- **Hover any score** to see the rationale, confidence level, and verification date

## Suggested source starting points

- [NCSL — National Conference of State Legislatures](https://www.ncsl.org/elections-and-campaigns)
- [Verified Voting — The Verifier](https://verifiedvoting.org/verifier/)
- [Brennan Center for Justice — Election security](https://www.brennancenter.org/issues/ensure-every-american-can-vote/voting-system-security)
- [EAC — Election Assistance Commission](https://www.eac.gov/)
- State election codes and Secretary of State websites

## Caveats

**Every score currently in the JSON is a placeholder.** The values are my best estimates based on widely-reported patterns and should not be published as authoritative. Each cell needs to be checked against primary sources and marked with appropriate `confidence` and `last_verified` values before sharing publicly.

Scoring is inherently interpretive. The 0–3 scale is coarse; edge cases will always require judgment. Consider the rubric a starting point for structured debate rather than an objective ranking.
