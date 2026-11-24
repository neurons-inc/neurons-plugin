# NIS histogram

How to draw the distribution of Neurons Impact Scores across a campaign's assets, as the hub's Campaign Insights and the in-chat campaign card do. The data comes from `campaign_stats`, `get_campaign_audit` or `wait_for_campaign_audit` ("Distribution, assets per one-point NIS bin", or `histogram` in the structured result).

## Bins

- **Nine one-point bins**, 1–2 up to 9–10, in score order left to right. Each bin holds the assets scoring from its lower bound up to, not including, its upper bound; the last bin includes 10.
- Draw every bin, empty ones too, so the axis is always 1 to 10.
- Use each bin's `count` as the tool gives it. Never recount from the asset list or re-bin.
- Colour each bar by the `band` the tool gives for that bin.

## Bands and colours

The hub's four NIS bands, in the hub's own NIS colours, which the in-chat cards use too (light and dark), so the hub, the card and the report match:

| Band | Range | Label | Light | Dark |
| --- | --- | --- | --- | --- |
| Minimal | 1 to under 3 | Score 1 – 2.9 | red `#e0414a` | `#e0414a` |
| Moderate | 3 to under 5 | Score 3 – 4.9 | orange `#ff8b22` | `#ff8b22` |
| Strong | 5 to under 7 | Score 5 – 6.9 | green `#04bf85` | `#04bf85` |
| Outstanding | 7 to 10 | Score 7 – 10 | dark green `#166534` | `#1b7e41` |

Text on a band colour (a band badge, a count inside a bar) is `#000000` on Minimal, `#1a1a1a` on Moderate and Strong, and `#ffffff` on Outstanding, so it meets WCAG AA. Never use white on the three lighter bands.

- Label the bins with their ranges and the bands with their names, not only colours, so the chart reads without colour too.
- Show the band counts and shares above the chart (the gauge and band bar, visuals 1 and 2 in `building-neurons-reports` `references/report-visuals.md`), each as the tool gives them.
- Title it "Neurons Impact Score distribution (1 – 10)".
- Mark the mean NIS as a thin vertical line labelled "Mean 5.4", with the score cut to one decimal as the tool wrote it.
- Never call a band or a range "top 25%", a quartile or a percentile. Bands are fixed score ranges, not shares of assets.

## HTML page or artifact

Inside the report's `<div class="nv">`, with the palette from `report-visuals.md`. Each bar's `height` is its count over the largest count, in percent; the mean line's `left` is `(mean − 1) / 9` in percent (5.08 → 45.3%).

```html
<style>
  .nv-hist { position: relative; display: flex; gap: 4px; align-items: stretch; height: 150px; margin: 24px 0 8px; padding: 0; list-style: none; }
  .nv-hist li { display: flex; flex: 1 1 0; flex-direction: column; align-items: center; min-width: 0; }
  .nv-hist b { font-size: 11px; } .nv-hist small { margin-top: 4px; color: var(--muted); font-size: 10px; white-space: nowrap; }
  .nv-hist .area { display: flex; flex: 1; align-items: flex-end; width: 100%; border-bottom: 1px solid var(--grid); }
  .nv-hist .bar { width: 100%; border-radius: 4px 4px 0 0; background: var(--band); }
  .nv-hist .mean { position: absolute; top: -4px; bottom: 18px; border-left: 2px dashed var(--head); }
  .nv-hist .mean span { position: absolute; top: -18px; left: 4px; color: var(--head); font-size: 11px; font-weight: 600; white-space: nowrap; }
</style>
<h4>Neurons Impact Score distribution (1 – 10)</h4>
<ul class="nv-hist" aria-label="Assets per one-point NIS bin">
  <li class="band-minimal" aria-label="NIS 1 to 2: 0 assets, Minimal"><b>0</b><span class="area"><span class="bar" style="height: 0%"></span></span><small>1–2</small></li>
  <li class="band-minimal" aria-label="NIS 2 to 3: 2 assets, Minimal"><b>2</b><span class="area"><span class="bar" style="height: 22%"></span></span><small>2–3</small></li>
  <!-- … one li per bin, nine in all … -->
  <li class="mean" style="left: 45.3%" aria-label="Mean NIS 5.0"><span>Mean 5.0</span></li>
</ul>
```

## Other formats

- **Slides**: the chart alone on one slide, titled with the takeaway ("Most ads land in Strong; three are Minimal").
- **Doc**: where the doc's tool draws charts from rows (Claude Docs does), the doc's own chart: module 2 in `doc-report.md`, next to this file. Without doc charts, a table with one row per bin (Range, Band, Assets).
- **Plain text**: one line per bin, as the tool writes it (`- 5–6: 3 (Strong)`).

## Not this chart

Scores per metric or per power are rows against a benchmark, not a distribution: draw them as metric bars (visual 8 in `report-visuals.md`), as `building-neurons-reports` section 4 says.
