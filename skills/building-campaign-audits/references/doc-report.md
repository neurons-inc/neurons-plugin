# Campaign audit as a doc

The whole recipe for an audit written up as a doc whose tool draws charts from rows and takes uploaded images. Claude Docs does both; read its guides first (`guide` with `topic.charts`, then `topic.uploads`). Follow the steps in order: a doc that skips one is not done. Every number comes from the audit tool's result (`get_campaign_audit` or `wait_for_campaign_audit`); never compute a count, share, mean or band.

## The doc, block by block

1. **Title and header.** The audit's title and "N creatives analysed"; the period, the purpose and the platforms with their counts. Lead with Neurons' headline sentence.
2. **Impact breakdown.** "Mean NIS **7.3** / 10, Outstanding", then the **band chart** (module 1). No table under it: its key names each band's range, count and share.
3. **Distribution.** One sentence on where most assets land, the **histogram** (module 2). No table under it: each bar prints its count and bin, and the band chart's key just above names the band colours.
4. **Ranked.** The **ranked list** (below): one table with every scored asset in the audit's asset list (the tool text's "Every asset, highest NIS first", or `media`: `name`, `nisScore`, `band`), each with its thumbnail. An audit reads one purpose, so its assets rank; skip this block only when `media` has no NIS.
5. **Powers and findings**, in the view the tool gives (SKILL.md section 4, item 5). Each finding: the eyebrow (What's working / What needs fixing), the recommendation headline and summary, the finding with "x of y assets", the line "Examples, highest NIS first:" (lowest first for a weakness; "and N more" when the tool says so), the **thumbnail table** (below), then the numbered recommendations.
6. **How to read this.** What the NIS and its bands are, and that every count comes from Neurons.

## Charts

Each chart is one widget whose code is one `<claude.Visualize>` tag, sent on one line, then placed with an embed in the same `batch` as its section's words (`topic.charts` has the two-member call). Copy the module, then change only the rows:

- **Rows** are the tool's values. Write every string as a JSON string (the tool's text through `JSON.stringify`), never between quotes you type: asset names come from shared workspaces. Bands in lower case.
- **Colours** are fixed in the modules: the hub's band colours, Minimal `#e0414a`, Moderate `#ff8b22`, Strong `#04bf85`, Outstanding `#166534`, with white text, `#ffffff`, on every band fill, as the hub's badges; words and lines in the doc's theme colours. The colour lookup compares the lower-cased band (Claude Docs refuses `obj[key]` in a module), so a capitalised band still gets its colour. Never swap in the doc's own palette: a band looks the same in the hub, the chat and the doc. The doc notes the literal colours; keep them.
- **Look at each chart** after placing it: `read` the widget with `{"kind":"screenshot"}`. Move any label it reports as overlapping, and look again.

### Module 1: band chart

The mean NIS as a marker on a 1–10 track tinted by band, over one stacked bar of assets per band with "count · share%" on each part, and a key with each band's range, count and share. Rows: one per band, in band order, with `assets` and `share` from `bandCounts` and `bandShares`; the mean from `meanNis`, `meanBand` and the asset count.

```jsx
export default () => <claude.Visualize data-claude-component='nis-bands' sources={{bands:{kind:'data', data:[{band:"minimal", range:"Score 1 – 2.9", from:1, to:3, assets:0, share:0},{band:"moderate", range:"Score 3 – 4.9", from:3, to:5, assets:0, share:0},{band:"strong", range:"Score 5 – 6.9", from:5, to:7, assets:3, share:27},{band:"outstanding", range:"Score 7 – 10", from:7, to:10, assets:8, share:73}]}, mean:{kind:'data', data:[{nis:7.3, band:"outstanding", assets:11}]}}}>{({bands, mean, datum}) => { const fill = s => { const b = String(s).toLowerCase(); return b === 'minimal' ? '#e0414a' : b === 'moderate' ? '#ff8b22' : b === 'strong' ? '#04bf85' : b === 'outstanding' ? '#166534' : 'var(--cds-text-secondary)'; }; const ink = s => { const b = String(s).toLowerCase(); return '#ffffff'; }; const name = s => { const b = String(s).toLowerCase(); return b === 'minimal' ? 'Minimal' : b === 'moderate' ? 'Moderate' : b === 'strong' ? 'Strong' : b === 'outstanding' ? 'Outstanding' : String(s); }; const m = mean.at(0), x0 = 40, w = 680, sx = v => x0 + (v - 1) / 9 * w; const total = bands.reduce((s, r) => s + r.assets, 0); let run = 0; const segs = bands.map(r => { const start = run; run += r.assets; return {r, start}; }); const bx = n => x0 + n / Math.max(1, total) * w; const ticks = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]; return <svg viewBox='0 0 760 280' role='img' aria-label='Mean NIS and assets per band' fontSize='12'><text data-claude-text-id='title' x='40' y='28' fontSize='16' fontWeight='600' fill='var(--cds-text-primary)'>Mean NIS and assets per band</text><text data-claude-text-id='track-label' x='40' y='60' fill='var(--cds-text-secondary)'>Mean NIS on the 1 – 10 scale</text><g data-claude-anchor='mean-track'>{bands.map(r => <rect key={r.band} x={sx(r.from)} y='70' width={sx(r.to) - sx(r.from)} height='14' fill={fill(r.band)} fillOpacity='0.55'><title>{`${name(r.band)}: ${r.range}`}</title></rect>)}<line x1={sx(m.nis)} x2={sx(m.nis)} y1='64' y2='90' stroke='var(--cds-text-primary)' strokeWidth='3' {...datum(m, 'nis')}><title>{`Mean NIS ${m.nis}, ${name(m.band)}, over ${m.assets} assets`}</title></line><text x={sx(m.nis) + 6} y='62' fontWeight='600' fill='var(--cds-text-primary)' {...datum(m, 'nis')}>{`${m.nis} · ${name(m.band)}`}</text>{ticks.map(t => <text key={t} x={sx(t)} y='104' textAnchor='middle' fill='var(--cds-text-secondary)'>{t}</text>)}</g><text data-claude-text-id='bar-label' x='40' y='150' fill='var(--cds-text-secondary)'>Assets per band</text><g data-claude-anchor='band-bar'>{segs.filter(g => g.r.assets > 0).map(g => <g key={g.r.band}><rect x={bx(g.start)} y='160' width={bx(g.start + g.r.assets) - bx(g.start)} height='36' fill={fill(g.r.band)} {...datum(g.r, 'assets')}><title>{`${name(g.r.band)} (${g.r.range}): ${g.r.assets} assets, ${g.r.share}%`}</title></rect><text x={bx(g.start) + 10} y='183' fontWeight='600' fill={ink(g.r.band)} {...datum(g.r, 'assets')}>{`${g.r.assets} · ${g.r.share}%`}</text></g>)}</g><g data-claude-anchor='band-key'>{bands.map((r, i) => <g key={r.band}><rect x={40 + i * 175} y='226' width='12' height='12' rx='3' fill={fill(r.band)}/><text x={58 + i * 175} y='237' fontWeight='600' fill='var(--cds-text-primary)'>{name(r.band)}</text><text x={58 + i * 175} y='256' fill='var(--cds-text-secondary)' {...datum(r, 'assets')}>{`${r.range} · ${r.assets} (${r.share}%)`}</text></g>)}</g></svg>; }}</claude.Visualize>;
```

No table under it: the key prints each band's range, count and share, and the reader can open the chart's data.

### Module 2: histogram

Nine one-point bins, 1–2 to 9–10, every bin drawn, each in its band's colour with its count on top, the mean as a dashed line. Rows: one per bin of the tool's `histogram`, in its order, renamed for the module: `bin` is "<from>–<to>" ("5–6"), `assets` is the bin's `count`; `from`, `to` and `band` as given.

```jsx
export default () => <claude.Visualize data-claude-component='nis-histogram' sources={{bins:{kind:'data', data:[{bin:"1–2", from:1, to:2, assets:0, band:"minimal"}, {bin:"2–3", from:2, to:3, assets:0, band:"minimal"}, {bin:"3–4", from:3, to:4, assets:0, band:"moderate"}, {bin:"4–5", from:4, to:5, assets:0, band:"moderate"}, {bin:"5–6", from:5, to:6, assets:1, band:"strong"}, {bin:"6–7", from:6, to:7, assets:2, band:"strong"}, {bin:"7–8", from:7, to:8, assets:5, band:"outstanding"}, {bin:"8–9", from:8, to:9, assets:2, band:"outstanding"}, {bin:"9–10", from:9, to:10, assets:1, band:"outstanding"}]}, mean:{kind:'data', data:[{label:"Mean", nis:7.3}]}}}>{({bins, mean, datum}) => { const fill = s => { const b = String(s).toLowerCase(); return b === 'minimal' ? '#e0414a' : b === 'moderate' ? '#ff8b22' : b === 'strong' ? '#04bf85' : b === 'outstanding' ? '#166534' : 'var(--cds-text-secondary)'; }; const name = s => { const b = String(s).toLowerCase(); return b === 'minimal' ? 'Minimal' : b === 'moderate' ? 'Moderate' : b === 'strong' ? 'Strong' : b === 'outstanding' ? 'Outstanding' : String(s); }; const m = mean.at(0), x0 = 40, w = 680, base = 250, h = 150, sx = v => x0 + (v - 1) / 9 * w; const peak = Math.max(1, ...bins.map(r => r.assets)), sy = v => base - v / peak * h; return <svg viewBox='0 0 760 300' role='img' aria-label='Neurons Impact Score distribution (1 – 10)' fontSize='12'><text data-claude-text-id='title' x='40' y='28' fontSize='16' fontWeight='600' fill='var(--cds-text-primary)'>Neurons Impact Score distribution (1 – 10)</text><g data-claude-anchor='bins'>{bins.map(r => <g key={r.bin}><rect x={sx(r.from) + 3} y={sy(r.assets)} width={sx(r.to) - sx(r.from) - 6} height={base - sy(r.assets)} fill={fill(r.band)} {...datum(r, 'assets')}><title>{`NIS ${r.bin}: ${r.assets} assets, ${name(r.band)}`}</title></rect><text x={(sx(r.from) + sx(r.to)) / 2} y={sy(r.assets) - 6} textAnchor='middle' fill='var(--cds-text-primary)' {...datum(r, 'assets')}>{r.assets}</text><text x={(sx(r.from) + sx(r.to)) / 2} y={base + 18} textAnchor='middle' fill='var(--cds-text-secondary)'>{r.bin}</text></g>)}</g><line x1={sx(m.nis)} x2={sx(m.nis)} y1='68' y2={base} stroke='var(--cds-chart-reference)' strokeDasharray='5 4' {...datum(m, 'nis')}/><text x={sx(m.nis) + 6} y='64' fill='var(--cds-text-primary)' {...datum(m, 'nis')}>{`${m.label} ${m.nis}`}</text></svg>; }}</claude.Visualize>;
```

No table under it: each bar prints its count and bin. Where the band chart isn't just above, one line of text under it names the colours: "Minimal 1–2.9 · Moderate 3–4.9 · Strong 5–6.9 · Outstanding 7–10".

### Ranked list (a table, not a chart)

A doc chart draws only shapes and words, never an image, so the ranking is one table that does both jobs: rank, thumbnail, name, NIS and band. Never a ranked chart plus a second list of the same assets. Rows: one per asset of the audit's `media` list that has a NIS, highest NIS first (ties keep the tool's order), numbered from 1. The thumbnails come with the finding examples' (Thumbnails, below): add every ranked asset's id to the same fetch, upload and blob batch.

```markdown
| # | Creative | Asset | NIS | Band |
| --- | --- | --- | --- | --- |
| 1 | ![Adobe_A · NIS 9.1](blob/<id>) | Adobe_A · uploaded 10:46 | 9.1 | Outstanding |
| 2 | ![Adobe_A · NIS 8.5](blob/<id>) | Adobe_A · uploaded 10:10 | 8.5 | Outstanding |
| 4 | ![Adobe_Firefly · NIS 7.4](blob/<id>) | Adobe_Firefly | 7.4 | Outstanding |
```

Then make the thumbnails small: a doc image fills its cell, so set the # column's cells to `colwidth` 44 and the Creative column's to 120 (Claude Docs: one `set` with `"attrs":{"colwidth":[120]}` on every cell of the column, ids from a read of the table). Without it each row is a full-size image and the table scrolls sideways. The band is a word: a doc table can't take the hub's band colours. Thumbnails that can't be fetched leave the Creative column out, with the line Thumbnails step 5 gives.

**Asset names.** The hub names a duplicated asset by adding " (variant)" to its source's name, so names stack ("Adobe_A (variant) (variant)"). Wherever the doc names an asset (the ranked list, the thumbnail tables, the text), drop the trailing " (variant)" repeats. When two assets then share a name, add the upload time from that list (or their `media` entry's `createdAt`) ("Adobe_A · uploaded 10:46", UTC; the date too, "uploaded 27 Jan 10:46", when the uploads fall on different days). Match an example to its `media` entry by id. Never invent a run number.

## Thumbnails

Every example creative appears as its image, in a table, never as a list of names or a full-width image with a caption.

1. **Words first.** Write every section, each finding ending with its "Examples…" line and its recommendations.
2. **Fetch.** `get_analysis_image_links` with the audit's example ids (the tool lists them) and every ranked asset's id and `views: ["original"]`, all in one call; it links 40 files per call and says which ids to call again with for the rest. Run each command it returns straight away, exactly as given, in the code sandbox: the links last 15 minutes. The doc can't take a link; it takes only files you upload.
3. **Upload.** All saved files to the doc in one asset upload (your artifact tool's upload with the doc's link), so the person sees at most one approval. Then one `batch` that creates a blob per file, with no `origin` (the link was a credential).
4. **Insert.** One `update` with an `insert` after each finding's "Examples…" line: a table, one column per example in the tool's order, names as the header row, the images in the next row, "NIS · band" in the row under them. An asset in several findings reuses its blob. Escape a `|` in a name as `\|`.

```markdown
| Adobe_Firefly · uploaded 06:39 | Adobe_Firefly · uploaded 10:19 | Adobe-vases |
| --- | --- | --- |
| ![Adobe_Firefly · NIS 7.4](blob/<id>) | ![Adobe_Firefly · NIS 7.3](blob/<id>) | ![Adobe-vases · NIS 7.2](blob/<id>) |
| NIS 7.4 · Outstanding | NIS 7.3 · Outstanding | NIS 7.2 · Outstanding |
```

5. **If the download can't reach the host** (connection refused, a 403 from a proxy, "host not allowed"), it's the sandbox's network: tell the person which host to allow, as the tool's reply names it (in the app's settings, under Capabilities), and put one line where each table goes: "Thumbnails couldn't be added here: <names>."

## Before you hand it over

- The band chart and the histogram are live charts in the band colours, each value printed on its mark, with no table under either repeating its numbers.
- The ranked list is one table with rank, thumbnail, name, NIS and band; no ranked chart beside it.
- No stacked " (variant)" in a name; names that collide carry their upload time.
- Every finding has its thumbnail table with name, image and "NIS · band", or the line saying why not.
- No link or token in the doc.
