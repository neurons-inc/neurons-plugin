# Report visuals

The visuals of Neurons' in-chat result cards, as snippets for reports: pages, artifacts and decks built from HTML. A doc whose tool draws charts from rows draws the same visuals as its own charts ("In a doc", at the end). A report that uses them reads like the cards the person already saw in the chat. Every value in them comes from a tool result in this conversation, written as the tool wrote it; the snippets only draw it.

Each visual says its values in text too (a label, a table or an `aria-label`), so nothing is known only from a shape or a colour. Asset and group names come from people: write them as text, HTML-escaped, never as markup.

## Which field goes where

| Tool and field | Visual |
| --- | --- |
| `get_analysis_results`: the NIS and its band | 1 Gauge |
| `get_analysis_results`: each metric's value, benchmark range or guideline | 8 Metric bars |
| `get_analysis_results` or `compare_analyses`: several assets' NIS, same purpose | 7 Ranked strip |
| `campaign_stats` or an audit: `meanNis`, `meanBand`, `count` | 1 Gauge, captioned "mean over N assets" |
| `campaign_stats` or an audit: `bandCounts`, with the shares the text gives (an audit's `bandShares`) | 2 Band bar |
| `campaign_stats` or an audit: `histogram` | `references/nis-histogram.md` in `building-campaign-audits` |
| `campaign_stats` or an audit: `platforms` | one line of text under the band bar |
| an audit: each finding's `examples` (name, NIS) | 7 Ranked strip, small and unnumbered, inside the finding card |
| `compare_groups`: `groups[].meanNis`, `band`, `nisN`, `count` | 1 Gauge per group, captioned "over nisN of count" |
| `compare_groups`: `metrics[]`, and `groups[].metrics[].mean`, `n`, `missing` | 3 Radar with its table |
| `compare_groups`: `differences[].nis` and `differences[].metrics[].diff` | 4 Difference chips |
| `compare_groups`: `groups[].splits` and `differences[].splits` | 5 Split rows |
| `compare_groups`: `fixFirst[]` | 6 Fix-first list |
| `compare_groups`: `warnings` | a "Check first" box above everything else |
| `compare_groups`: `notAvailable`, `withoutBenchmarks`, `missingFeatures` | short notes under the last visual |

Thumbnails (6, 7) are each asset's `original` from the one `get_analysis_image_links` call the report makes anyway (SKILL.md section 5), by the file name its command saved. A video's thumbnail is its 0:00.5 frame: fine as a thumbnail, never as a finding's image.

## The palette

Put this `<style>` in once per page and wrap the report in `<div class="nv">`. It is the cards' palette, light and dark; `.nv` paints its own background, so dark text never lands on a light page. The bands are the Neurons hub's NIS colours: Minimal red `#e0414a`, Moderate orange `#ff8b22`, Strong green `#04bf85`, Outstanding dark green `#166534` (lifted to `#1b7e41` in dark, where the hub's is too dark to see). A band badge is the hub's Synapse chip: the band colour as its fill with white text on every band (white misses WCAG AA on the three lighter bands; the design team chose it for consistency with the hub, to revisit in Synapse). The good, okay and bad chips are Synapse's pale red, orange and green tags. The series are Synapse colours with no score meaning, so a group never reads as good or bad by its colour. A chosen style (SKILL.md, "Style") changes the fonts, headings and accents; keep the band, series and chip colours, so a band looks the same in the hub, the chat and the report.

```html
<style>
  .nv { --ink: #333; --head: #380f57; --muted: #6f6f6f; --surface: #fff; --line: #eeeeee; --grid: #e4e2ea; --track: #ecebf0; --pale: #f3eeff;
    --band-minimal: #e0414a; --band-moderate: #ff8b22; --band-strong: #04bf85; --band-outstanding: #166534;
    --on-minimal: #ffffff; --on-moderate: #ffffff; --on-strong: #ffffff; --on-outstanding: #ffffff;
    --s1: #745789; --s2: #074de4; --s3: #858585; --s4: #6184ff;
    --good-bg: #e9fcf1; --good: #166534; --bad-bg: #f8dfe0; --bad: #e0414a; --okay-bg: #feead7; --okay: #7d3d02; --chip-bg: #f0f0f0; --chip: #595959;
    color: var(--ink); background: var(--surface); font: 14px/1.4 Inter, system-ui, sans-serif; font-variant-numeric: tabular-nums; }
  @media (prefers-color-scheme: dark) { .nv { --ink: #f1ecf8; --head: #e9dfff; --muted: #a596b9; --surface: #212121; --line: #3a3a3a; --grid: #3a3a40; --track: #3a3a40; --pale: #3a2f52;
    --band-outstanding: #1b7e41;
    --s1: #c5abfd; --s2: #7198ed; --s3: #c2c2c2; --s4: #85a0fe;
    --good-bg: #024b1f; --good: #82fcb3; --bad-bg: #470609; --bad: #f9b8bc; --okay-bg: #4b2501; --okay: #fdbc82; --chip-bg: #333; --chip: #d6cce4; } }
  .nv h2, .nv h3, .nv h4 { color: var(--head); } .nv .muted { color: var(--muted); font-size: 12px; }
  .nv .band-minimal { --band: var(--band-minimal); } .nv .band-moderate { --band: var(--band-moderate); }
  .nv .band-strong { --band: var(--band-strong); } .nv .band-outstanding { --band: var(--band-outstanding); }
  .nv .s1 { --s: var(--s1); } .nv .s2 { --s: var(--s2); } .nv .s3 { --s: var(--s3); } .nv .s4 { --s: var(--s4); }
  .nv .swatch { display: inline-block; width: 10px; height: 10px; border-radius: 3px; background: var(--s, var(--band)); }
  .nv .badge { display: inline-block; padding: 2px 8px; border-radius: 999px; background: var(--chip-bg); color: var(--chip); font-size: 12px; font-weight: 600; }
  .nv .badge.band-minimal { background: var(--band-minimal); color: var(--on-minimal); } .nv .badge.band-moderate { background: var(--band-moderate); color: var(--on-moderate); }
  .nv .badge.band-strong { background: var(--band-strong); color: var(--on-strong); } .nv .badge.band-outstanding { background: var(--band-outstanding); color: var(--on-outstanding); }
  .nv .badge.good { background: var(--good-bg); color: var(--good); } .nv .badge.okay { background: var(--okay-bg); color: var(--okay); } .nv .badge.bad { background: var(--bad-bg); color: var(--bad); }
  .nv table { border-collapse: collapse; font-size: 12px; } .nv th, .nv td { padding: 5px 8px; border-bottom: 1px solid var(--line); text-align: right; }
  .nv th:first-child { text-align: left; } .nv thead th { color: var(--muted); font-weight: 500; }
</style>
```

Band classes: `band-minimal` (1 – 2.9), `band-moderate` (3 – 4.9), `band-strong` (5 – 6.9), `band-outstanding` (7 – 10), always the band the tool gives. Series classes `s1` to `s4` are the groups in the tool's order: `s1` is the first group ("Our ads").

## 1. NIS gauge

A half circle from 0 to 10 in the band's colour, the score under it, then the band badge. Set `stroke-dasharray` to the NIS × 10, from the NIS as the tool wrote it (5.4 → `54 100`). With no NIS, leave out the second path and write "—".

```html
<style>
  .nv-gauge { display: inline-flex; flex-direction: column; align-items: center; margin: 0; text-align: center; }
  .nv-gauge path { fill: none; stroke-width: 10; stroke-linecap: round; } .nv-gauge .t { stroke: var(--track); } .nv-gauge .v { stroke: var(--band); }
  .nv-gauge b { margin-top: -26px; color: var(--head); font: 600 24px/1 Inter, sans-serif; } .nv-gauge b small { color: var(--muted); font-size: 12px; font-weight: 500; }
  .nv-gauge .badge { margin-top: 6px; } .nv-gauge figcaption { margin-top: 4px; color: var(--muted); font-size: 11px; }
</style>
<figure class="nv-gauge band-strong" role="img" aria-label="Mean NIS 5.4 of 10, Strong, over 18 assets">
  <svg viewBox="0 0 112 62" width="112" height="62" aria-hidden="true">
    <path class="t" d="M10 56 A46 46 0 0 1 102 56" pathLength="100"/>
    <path class="v" d="M10 56 A46 46 0 0 1 102 56" pathLength="100" stroke-dasharray="54 100"/>
  </svg>
  <b aria-hidden="true">5.4<small>/10</small></b>
  <span class="badge band-strong" aria-hidden="true">Strong</span>
  <figcaption aria-hidden="true">over 18 assets</figcaption>
</figure>
```

Where: the top of a single-ad report, each group's card in a comparison, the impact breakdown of a campaign. One asset's gauge always has its NIS breakdown next to it (SKILL.md section 4). **Doc:** "NIS **5.4** / 10, Strong" on its own line; with doc charts, the campaign's mean also goes on the band chart ("In a doc"). **Deck:** the gauge alone, large, with the takeaway as the slide title.

## 2. Band bar

One stacked bar of the assets per band, each part as wide as its count, with the counts and shares in the key. Write counts and shares as the tool gives them; never compute them.

```html
<style>
  .nv-bandbar .track { display: flex; gap: 2px; height: 18px; overflow: hidden; border-radius: 6px; background: var(--track); }
  .nv-bandbar .track span { min-width: 4px; background: var(--band); }
  .nv-bandbar ul { display: grid; grid-template-columns: repeat(auto-fit, minmax(150px, 1fr)); gap: 6px 12px; margin: 10px 0 0; padding: 0; list-style: none; font-size: 12px; }
  .nv-bandbar li { display: grid; grid-template-columns: auto 1fr; gap: 0 6px; align-items: center; } .nv-bandbar li .swatch { grid-row: span 2; }
  .nv-bandbar li em { grid-column: 2; font-style: normal; }
</style>
<div class="nv-bandbar">
  <div class="track" role="img" aria-label="Assets per band: Minimal 2, Moderate 10, Strong 16, Outstanding 6.">
    <span class="band-minimal" style="flex-grow: 2"></span><span class="band-moderate" style="flex-grow: 10"></span>
    <span class="band-strong" style="flex-grow: 16"></span><span class="band-outstanding" style="flex-grow: 6"></span>
  </div>
  <ul>
    <li><span class="swatch band-minimal"></span><b>Minimal <span class="muted">1 – 2.9</span></b><em>2 assets · 6%</em></li>
    <li><span class="swatch band-moderate"></span><b>Moderate <span class="muted">3 – 4.9</span></b><em>10 assets · 29%</em></li>
    <li><span class="swatch band-strong"></span><b>Strong <span class="muted">5 – 6.9</span></b><em>16 assets · 47%</em></li>
    <li><span class="swatch band-outstanding"></span><b>Outstanding <span class="muted">7 – 10</span></b><em>6 assets · 18%</em></li>
  </ul>
</div>
```

Leave out the bar part of an empty band, but keep its key item ("0 assets · 0%"). Where: next to the campaign gauge, as the hub's band cards. **Doc:** the band chart ("In a doc"), whose key names each band's range, count and share, with no table under it; a table (Band, Range, Assets, Share) only when the doc has no charts. **Deck:** on the "Current scenario" slide, under the gauge.

## 3. Radar with its table

The nine metric averages on 0–100 axes, one outline per group (four at most), and beside it the table with the same numbers: never the radar without the table. Fill `axes` with `metrics[].label` and each series with that group's `metrics[].mean`, in the tool's order; `null` stays `null` (drawn at the centre, "n/a" in the table).

```html
<style>
  .nv-metrics { display: grid; grid-template-columns: minmax(220px, 300px) minmax(0, 1fr); gap: 12px; align-items: center; }
  @media (max-width: 560px) { .nv-metrics { grid-template-columns: minmax(0, 1fr); } }
  .nv-radar { width: 100%; max-width: 300px; height: auto; overflow: visible; }
  .nv-radar .g { fill: none; stroke: var(--grid); } .nv-radar text { fill: var(--ink); font: 500 10px Inter, sans-serif; }
  .nv-radar g polygon { fill: var(--s); fill-opacity: .1; stroke: var(--s); stroke-width: 2; stroke-linejoin: round; } .nv-radar circle { fill: var(--s); }
</style>
<div class="nv-metrics">
  <svg class="nv-radar" role="img" aria-label="Radar of metric averages, 0 to 100, for Our ads and L'Oréal. The table lists the values."></svg>
  <table><!-- Metric, then one column per group headed by its swatch and label; a mean over fewer than all its assets adds <span class="muted">n 12</span> --></table>
</div>
<script>
(() => {
  // From compare_groups' structured result, in its order; never typed from memory.
  const axes = ['Start Focus', 'Focus', 'End Focus', 'Memory', 'Engagement', 'Cognitive Demand', 'Intent', 'Trust', 'Avoidance'];
  const series = [[61.2, 55.4, 48.9, 63.1, 52.7, 47.3, 58.8, 60.2, 21.4], [57.5, 58.1, 44.2, 60.4, 55.0, 52.6, 54.1, 57.9, 25.8]];
  const svg = document.currentScript.previousElementSibling.querySelector('.nv-radar');
  const c = 150, r = 96, n = axes.length;
  const at = (f, i) => [c + r * f * Math.sin((2 * Math.PI * i) / n), c - r * f * Math.cos((2 * Math.PI * i) / n)].map((v) => +v.toFixed(1));
  const pts = (fs) => fs.map((f, i) => at(f, i).join(',')).join(' ');
  let s = [0.25, 0.5, 0.75, 1].map((f) => `<polygon class="g" points="${pts(axes.map(() => f))}"/>`).join('');
  s += axes.map((_, i) => `<line class="g" x1="${c}" y1="${c}" x2="${at(1, i)[0]}" y2="${at(1, i)[1]}"/>`).join('');
  series.slice(0, 4).forEach((vals, k) => {
    const f = vals.map((v) => Math.min(100, Math.max(0, v ?? 0)) / 100);
    s += `<g class="s${k + 1}"><polygon points="${pts(f)}"/>` + f.map((x, i) => (vals[i] == null ? '' : `<circle cx="${at(x, i)[0]}" cy="${at(x, i)[1]}" r="3"/>`)).join('') + '</g>';
  });
  s += axes.map((label, i) => { const [x, y] = at(1 + 14 / r, i); return `<text x="${x}" y="${y}" text-anchor="${Math.abs(x - c) < 4 ? 'middle' : x > c ? 'start' : 'end'}" dominant-baseline="middle">${label}</text>`; }).join('');
  svg.setAttribute('viewBox', '0 0 300 300'); svg.innerHTML = s;
})();
</script>
```

Where: a comparison's "Metric averages", right after the group gauges. A single asset gets metric bars (8) instead: a radar has no benchmark. **Doc:** the metric means as horizontal grouped bars ("In a doc"), a row per metric, each bar labelled at its end with its mean and each group's n in the legend, with no table under it; the table only when the doc has no charts. **Deck:** radar and table on one slide; in a .pptx, the deck's own radar chart or this PNG, drawn from the structured result you saved as JSON:

```python
import json, math, matplotlib; matplotlib.use("Agg"); import matplotlib.pyplot as plt
d = json.load(open("groups.json"))  # compare_groups' structured result
axes = [m["label"] for m in d["metrics"]]; ang = [2 * math.pi * i / len(axes) for i in range(len(axes))] + [0]
ax = plt.figure(figsize=(5, 5), dpi=160).add_subplot(polar=True); ax.set_theta_offset(math.pi / 2); ax.set_theta_direction(-1)
for g, c in zip(d["groups"][:4], ["#745789", "#074de4", "#858585", "#6184ff"]):
    v = [m["mean"] or 0 for m in g["metrics"]]; v += v[:1]
    ax.plot(ang, v, color=c, lw=2, label=g["label"]); ax.fill(ang, v, color=c, alpha=.1)
ax.set_xticks(ang[:-1], axes, fontsize=8); ax.set_ylim(0, 100); ax.set_yticks([50, 100])
ax.legend(loc="upper center", bbox_to_anchor=(.5, -.08), ncol=4, frameon=False); plt.savefig("radar.png", bbox_inches="tight")
```

## 4. Difference chips

One chip per difference from the first group: the NIS first, then each metric. Green when better, red when worse, grey when neither:

- `better` / `worse`: higher is better for the NIS and every metric except Avoidance, where lower is better.
- `neutral`: Cognitive Demand always (it is best inside each asset's own range), and every `null`, written "n/a".
- `same`: a difference that rounds to `0.0`.

Write the difference to one decimal, with its sign and a real minus (`+3.7`, `−1.0`).

```html
<style>
  .nv-chips { display: flex; flex-wrap: wrap; gap: 6px; margin: 0; padding: 0; list-style: none; }
  .nv-chip { display: inline-block; padding: 3px 9px; border-radius: 999px; background: var(--chip-bg); color: var(--chip); font-size: 12px; }
  .nv-chip.better { background: var(--good-bg); color: var(--good); } .nv-chip.worse { background: var(--bad-bg); color: var(--bad); }
</style>
<p><span class="swatch s2"></span> <b>L'Oréal</b> <span class="muted">vs Our ads</span></p>
<ul class="nv-chips" aria-label="L'Oréal minus Our ads">
  <li class="nv-chip worse">NIS <b>−0.6</b></li><li class="nv-chip worse">Start Focus <b>−3.7</b></li><li class="nv-chip better">Focus <b>+2.7</b></li>
  <li class="nv-chip neutral">Cognitive Demand <b>+5.3</b></li><li class="nv-chip worse">Avoidance <b>+4.4</b></li>
</ul>
```

Say the rule once above the chips: "Each group minus Our ads. Green is better, red is worse. Lower Avoidance is better; Cognitive Demand stays grey." Then name the two or three biggest gaps in words. **Doc:** the difference bars ("In a doc"), each labelled with its number and "better" or "worse", never colour alone; without charts, a Difference column in the metric table. **Deck:** the chips under the radar, or one slide per compared group.

## 5. Split rows

When `compare_groups` ran with `split_by`: one row per use case or purpose, one column per group, with the mean NIS, its band badge, the asset count, and for every group after the first its NIS chip (4). A group without that split says "none"; compare only the splits both groups have.

```html
<table>
  <thead><tr><th>Use case</th><th><span class="swatch s1"></span> Our ads</th><th><span class="swatch s2"></span> L'Oréal</th></tr></thead>
  <tbody>
    <tr><th>Digital advertising / social media</th>
      <td><b>5.7</b> <span class="badge band-strong">Strong</span> <span class="muted">12 assets</span></td>
      <td><b>5.1</b> <span class="badge band-strong">Strong</span> <span class="muted">9 assets</span> <span class="nv-chip worse">NIS <b>−0.6</b></span></td></tr>
    <tr><th>Print</th><td><b>4.8</b> <span class="badge band-moderate">Moderate</span> <span class="muted">6 assets</span></td><td class="muted">none</td></tr>
  </tbody>
</table>
```

Write a split's key in words: a purpose by its label ("Brand building"), a use case's `category/subcategory` with spaces ("Digital advertising / social media"). **Doc and deck:** the same table.

## 6. Fix-first list

`fixFirst` in the tool's order, each with its thumbnail: name, group, metric, value against the benchmark bound, and gap.

```html
<style>
  .nv-fix { margin: 0; padding: 0; list-style: none; counter-reset: fix; }
  .nv-fix li { display: grid; grid-template-columns: 20px 64px minmax(0, 1fr); gap: 10px; align-items: center; padding: 8px 0; border-bottom: 1px solid var(--line); counter-increment: fix; }
  .nv-fix li::before { content: counter(fix); color: var(--muted); font-weight: 600; }
  .nv-fix img { width: 64px; height: 64px; object-fit: cover; border-radius: 8px; background: var(--track); }
  .nv-fix .name { overflow: hidden; font-weight: 600; text-overflow: ellipsis; white-space: nowrap; }
  .nv-fix .tag { padding: 1px 7px; border: 1px solid var(--line); border-radius: 999px; font-size: 11px; }
  .nv-fix .gap { color: var(--bad); font-weight: 600; }
</style>
<ol class="nv-fix">
  <li><img src="images/summer-sale-original.png" alt="Summer sale 1080x1080">
    <div><div class="name">Summer sale 1080x1080.png</div>
      <div class="muted"><span class="tag">Our ads</span> <b>Focus</b> 31.4 vs benchmark from 48.0 · <span class="gap">16.6 below</span> · counts towards NIS · +2 more metrics outside</div></div></li>
</ol>
```

`direction: below` reads "vs benchmark from {lower}", `above` reads "vs benchmark up to {upper}". Add "counts towards NIS" when `prioritised`, and "+N more metrics outside" from `otherMetricsOutside`. Where: the last section of a comparison, before the recommendations. **Doc:** a numbered list, each item with its thumbnail (uploaded into the doc, SKILL.md section 5) and the same line. **Deck:** up to three per slide.

## 7. Ranked strip

Assets side by side in rank order, number 1 first: thumbnail, rank, name, NIS and band. Rank only assets with the same purpose, by the NIS the tool gives (SKILL.md section 4); otherwise keep the person's order and leave out the rank numbers.

```html
<style>
  .nv-strip { display: grid; grid-template-columns: repeat(auto-fill, minmax(140px, 1fr)); gap: 10px; margin: 0; padding: 0; list-style: none; }
  .nv-strip li { overflow: hidden; border: 1px solid var(--line); border-top: 4px solid var(--band); border-radius: 12px; background: var(--surface); }
  .nv-strip img { display: block; width: 100%; aspect-ratio: 1; object-fit: cover; background: var(--track); }
  .nv-strip li > div { padding: 8px; } .nv-strip .name { overflow: hidden; font-size: 12px; font-weight: 600; text-overflow: ellipsis; white-space: nowrap; }
  .nv-strip .rank { color: var(--muted); font-size: 11px; font-weight: 600; }
</style>
<ol class="nv-strip">
  <li class="band-outstanding"><img src="images/night-repair-original.png" alt="Night Repair social">
    <div><span class="rank">#1</span><div class="name">Night Repair social</div><b>7.4</b> <span class="badge band-outstanding">Outstanding</span></div></li>
</ol>
```

Where: "Side by side" in a report on two or more ads; the ranked view of a campaign ("score these as one campaign": `campaign_stats` for the gauge and band bar, `get_analysis_results` for each asset's NIS); and small, without rank numbers, as the example creatives in an audit's finding cards, in the tool's order. **Doc:** as "In a doc" says; the thumbnails as SKILL.md section 5 says. Without doc charts, a table (Rank, Image, Asset, NIS, Band). **Deck:** one slide of up to eight assets; more go on the next slide.

## 8. Metric bars

One row per metric: the asset's value as a marker on 0–100, the benchmark range shaded, and the status in words. Trust and Avoidance say "guideline" and shade their guideline band; a metric without a range says "unbenchmarked" and has no shading. Status words follow the metric's direction (`reading-neurons-results`): above, within or below the range; for Cognitive Demand "within", "too high" or "too low"; for a guideline "meets" or "misses".

```html
<style>
  .nv-bars { display: grid; grid-template-columns: max-content minmax(80px, 1fr) max-content; gap: 8px 12px; align-items: center; font-size: 13px; }
  .nv-bars .bar { position: relative; height: 10px; border-radius: 5px; background: var(--track); }
  .nv-bars .range { position: absolute; top: 0; bottom: 0; border-radius: 5px; background: var(--pale); box-shadow: inset 0 0 0 1px var(--s1); }
  .nv-bars .mark { position: absolute; top: -4px; width: 4px; height: 18px; margin-left: -2px; border-radius: 2px; background: var(--head); }
</style>
<div class="nv-bars">
  <span>Start Focus</span>
  <span class="bar" aria-hidden="true"><span class="range" style="left: 55%; width: 20%"></span><span class="mark" style="left: 86.6%"></span></span>
  <span><b>86.6</b> <span class="muted">range 55–75</span> <span class="badge good">above</span></span>
  <span>Avoidance <span class="muted">guideline</span></span>
  <span class="bar" aria-hidden="true"><span class="range" style="left: 0%; width: 30%"></span><span class="mark" style="left: 18.2%"></span></span>
  <span><b>18.2</b> <span class="muted">under 30</span> <span class="badge good">meets</span></span>
</div>
```

`left` is the range's lower bound and `width` its upper minus lower, both as percent of 100. The badge colour follows the status word, never a judgement of how far out: `within`, `above` and `meets` are green (`good`); `below` and `misses` are red (`bad`); Cognitive Demand's `too high` and `too low` are orange (`okay`). These are the hub's metric colours, not the NIS bands: a metric status never takes a band badge. The words carry the meaning. Where: a single asset's scores, each asset's column in "Side by side", and any per-metric row in a campaign. Start, whole-ad and End are three separate rows, never one line. **Doc:** the metric bars as a chart ("In a doc"), each row labelled with its value, range and status, with no table under it; a table (Metric, Value, Range, Status) only when the doc has no charts. **Deck:** the prioritised metrics only, one slide.

## In a doc

Many doc tools draw live charts from rows: the reader hovers a value, opens its data and comments on a bar. Where the doc has them, draw the visuals above as the doc's own charts, never as a picture of a chart. Read the doc tool's chart guide first and follow its shape. In Claude Docs that is `guide` with `topic.charts`: a widget whose code is one `<claude.Visualize>` tag, its rows in `sources` (`{rows:{kind:'data', data:[…]}}`, inline, from the tool result), its one child a function that draws an `<svg>` from those rows. A chart that already has a `definition` literal is edited as `topic.chart-definition` says. Other hosts: their chart block, fed the same rows.

- **Rows from the tool, as it wrote them.** One row per band, bin, asset, metric or group, with the tool's numbers and words (`{band:"strong", range:"Score 5 – 6.9", assets:3, share:27}`). Write every string in `sources` as a JSON string, the tool's text through `JSON.stringify`, never between quotes you type: asset names come from shared workspaces, and a quote in one would break the chart or run as code in the doc. Write bands in lower case, and look colours up without indexing (Claude Docs refuses `obj[key]` in a module): a chain of comparisons on the lower-cased band, with a neutral fallback. Every printed value and label is read from the rows; only the title and notes are typed. Never compute a count, share, mean or band: the tool gives them.
- **Values on the marks, no repeat table.** Print each value on its mark ("3 · 27%", "7.4 · Outstanding") and name the band in each band mark's hover text. The doc chart's labels and its data the reader can open already give every number, so put no table under a chart that repeats it: the same numbers twice, one of them a wide table to scroll sideways, only reads as noise. Add text only for what the chart doesn't show, in one line, not a table: where band colours aren't named on the chart, the legend under it ("Minimal 1–2.9 · Moderate 3–4.9 · Strong 5–6.9 · Outstanding 7–10"). In any doc table, escape a `|` in a name as `\|`.
- **Colours.** Band marks take the hub's band colours, whatever the doc theme: Minimal `#e0414a`, Moderate `#ff8b22`, Strong `#04bf85`, Outstanding `#166534`; text on a band fill is white, `#ffffff`, on every band. Groups take the series colours above (`s1` first). The good and bad of a difference are the doc's own good and bad colours. Everything else (words, axes, gridlines, reference lines) takes the doc's theme colours, so it reads in light and dark (Claude Docs: `var(--cds-text-primary)`, `var(--cds-text-secondary)`, `var(--cds-chart-axis)`, `var(--cds-chart-reference)`). The doc may note that the band colours are literal and won't flip in dark mode: keep them, so a band looks the same in the hub, the chat and the doc, and the labels name every band.
- **Look at it.** Take the doc's screenshot of each chart (Claude Docs: a `read` of the widget with `{"kind":"screenshot"}`), move any label it reports as overlapping, and look again.

| Visual | Doc chart | Rows | Text beside it |
| --- | --- | --- | --- |
| 1 Gauge and 2 Band bar (a campaign) | one chart: the mean NIS as a marker on a 1–10 track tinted by band, labelled "7.3 · Outstanding"; under it one stacked bar of assets per band, "count · share%" on each part, and a key with each band's range, count and share. Leave out an empty band's part, keep its key item | a row per band (band, range, from, to, assets, share); a one-row source for the mean (NIS, band, assets) | none: the key names each band's range, count and share |
| 1 Gauge (one asset, or a group) | "NIS **5.4** / 10, Strong" on its own line, or the same track with its marker | — | the NIS breakdown (SKILL.md section 4) |
| NIS histogram | nine bars, 1–2 to 9–10, every bin drawn, each in its band's colour with its count on top; the mean as a dashed reference line labelled "Mean 7.3" | a row per bin (bin, from, to, assets, band) as the tool gives them; a one-row source for the mean | the band legend in one line ("Minimal 1–2.9 · Moderate 3–4.9 · Strong 5–6.9 · Outstanding 7–10"), unless the band chart's key is just above |
| 7 Ranked strip | no chart: a doc chart can't show images, so one table does both jobs, a row per asset, number 1 first: #, thumbnail, name, NIS, band (`doc-report.md`'s ranked list). Never a ranked chart and a second list of the same assets | — | — |
| 8 Metric bars | a row per metric on 0–100: the benchmark range as a pale span, the value as a marker, and "62 · within 45–70" printed at its end | a row per metric (metric, value, low, high, status) | none: each row prints its value, range and status |
| 3 Radar with its table | horizontal grouped bars on 0–100: a row per metric, one bar per group in its series colour, the mean printed at the end of each bar; each group's n in the legend ("Group A · n 12"). Horizontal, so a mean has room with up to four groups; a radar only when the doc's chart guide draws one | a row per metric and group (metric, group, mean, n) | none: each bar prints its mean, the legend each group's n (a radar drawn in a doc keeps its table, as the radar prints no values) |
| 4 Difference chips | bars about zero, one per metric, labelled "+0.7 better" or "−4.2 worse", Cognitive Demand grey | a row per metric (metric, diff, verdict) | none: each bar prints its number and verdict |

A doc without charts: the tables, as each visual's **Doc:** line names. This is only about docs: HTML pages, artifacts and decks keep their text equivalents (aria labels, the radar's table) as the snippets above show. The example creatives of a finding are the thumbnail table SKILL.md section 5 describes, with no chart.

The modules to copy (band chart, histogram) and the ranked list table, tested in Claude Docs, are in `building-campaign-audits` `references/doc-report.md`; draw the other visuals the same way.
