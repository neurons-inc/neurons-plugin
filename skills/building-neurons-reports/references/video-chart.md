# The video metrics chart

The chart the hub's video Metrics page shows, for a report: Focus, Cognitive Demand, Engagement and Memory for every frame (24 per second), with the video above it where the format can play one.

## The data

Draw it only from the frame scores files `get_video_timeline` downloads: one call for every video in the report with `download_frames: true`, then run the command it returns exactly as given. It saves `<asset>-frames.json` per video, with:

- `fps` (24), `duration` in seconds, and `frames`: one row per frame, `[frame, focus, cognitive_demand, engagement, memory]`, the frame counted from 0 (it shows from `frame / fps` to `(frame + 1) / fps`), `null` where Neurons wrote no score.
- `peaks` and `lows`: the moments, each a single frame (`metric`, `frame`, `start`, `end`, `timestamp` such as `0:23.4`, `value`), and `averages` over every frame.

The file holds thousands of rows: read it in the shell or splice it into the page with the shell (below), and never paste its rows into the conversation or retype them. Never draw a line through a video's Start, whole-ad and End values from `get_analysis_results` (three separate windows, not a timeline), never fill in frames the file doesn't have, and never put a benchmark band on this chart: the benchmarks belong to the whole-ad values.

Without a shell, or when the download fails and can't be fixed, chart the seconds instead: call `get_video_timeline` with `seconds_per_row: 1`; the table in its text has one row per second (the time it starts, `m:ss`, and the four scores), and `structuredContent.videos[]` has each video's `peaks`, `lows` and `averages`. Plot each second at its middle and say in the caption that the chart is per second.

## The look

- Y axis 0–100, gridlines at 0, 25, 50, 75 and 100. X axis is time, labelled `00:00`, `00:05`… (one label every 1, 2, 5, 10, 15 or 30 seconds, whichever keeps them apart).
- One line per metric, each frame plotted at its middle (`(frame + 0.5) / fps`), in the hub's colours: Focus `#E3A500` (yellow), Cognitive Demand `#8255E8` (purple), Engagement `#2F7FE0` (blue), Memory `#E5476B` (pink). The legend sits under the chart, in that order.
- Mark every moment in `peaks` with ▲ just above its point and every one in `lows` with ▼ just below it, in the line's colour. Label the first peak and the first low of each metric (its strongest) with its timestamp; more labels crowd the chart, and the finding cards name the rest.
- In an HTML page, a playhead (a vertical line with its time and frame, `00:05:250 · frame 126`) follows the video frame by frame, and the values of all four lines at that frame show beside it.
- Name moments by their `timestamp` (`0:23.4`), in labels, captions and finding cards alike.

## HTML page: the video above the chart, in sync

Inside the report's `<div class="nv">`, the chart takes its text, grid and playhead colours from the palette in `report-visuals.md`, so it follows light and dark like the other visuals; the four metric colours stay as above.

Put the original (or the attention heatmap video) from `get_analysis_video_links` above the chart, by the file name its command saved. One figure per video. The video's controls play and seek; clicking or dragging on the chart seeks the video too. With more than one of a video's files saved, the buttons switch between them at the same moment. Write the page with the placeholder `/*FRAMES:ad-frames.json*/null` (the frames file's saved name) as `data`, then splice the file in with the shell, as shown after the pattern.

```html
<style>
  .nvt { margin: 0; font: 13px/1.4 Inter, system-ui, sans-serif; color: var(--ink, #333); }
  .nvt video { display: block; width: 100%; max-height: 420px; background: #111; border-radius: 10px; }
  .nvt svg { display: block; width: 100%; height: auto; cursor: crosshair; touch-action: none; }
  .nvt svg text { font-size: 11px; fill: var(--muted, #8a8a8a); }
  .nvt .g { stroke: var(--grid, #ececf0); }
  .nvt .ph { stroke: var(--head, #380f57); stroke-width: 1.5; }
  .nvt .phl { fill: var(--head, #380f57); } .nvt .pht { fill: var(--surface, #fff); font-weight: 600; }
  .nvt .vals { font-weight: 600; }
  .nvt-views { display: flex; gap: 6px; margin-bottom: 8px; }
  .nvt-views button { padding: 4px 12px; border: 1px solid #c5abfd; border-radius: 8px; background: var(--surface, #fff); color: var(--head, #380f57); cursor: pointer; }
  .nvt-views button[aria-pressed="true"] { background: var(--pale, #e2d5fe); font-weight: 600; }
  .nvt-legend { display: flex; flex-wrap: wrap; gap: 14px; justify-content: center; margin-top: 4px; }
  .nvt-legend i { display: inline-block; width: 10px; height: 10px; margin-right: 6px; border-radius: 3px; }
</style>
<figure class="nvt">
  <div class="nvt-views"><button data-src="ad-original.mp4" aria-pressed="true">Original</button><button data-src="ad-attention.mp4" aria-pressed="false">Attention</button></div>
  <video src="ad-original.mp4" controls playsinline preload="metadata"></video>
  <svg role="img" aria-label="Focus, Cognitive Demand, Engagement and Memory for every frame"></svg>
  <div class="nvt-legend"></div>
  <figcaption>Neurons' scores for every frame. ▲ peak, ▼ low.</figcaption>
</figure>
<script>
(() => {
  // The frames file get_video_timeline downloaded, spliced in by the shell (see below); never typed by hand.
  const data = /*FRAMES:ad-frames.json*/null;
  const fig = document.currentScript.previousElementSibling; // the <figure> just above
  const video = fig.querySelector('video'), svg = fig.querySelector('svg');
  const M = [['focus', 'Focus', '#E3A500'], ['cognitive_demand', 'Cognitive Demand', '#8255E8'], ['engagement', 'Engagement', '#2F7FE0'], ['memory', 'Memory', '#E5476B']];
  const W = 760, H = 270, L = 34, R = 14, T = 26, B = 26, pw = W - L - R, ph = H - T - B, D = data.duration, F = data.fps, rows = data.frames;
  const x = (t) => L + (Math.min(Math.max(t, 0), D) / D) * pw, y = (v) => T + (1 - v / 100) * ph;
  const p2 = (n, w = 2) => String(n).padStart(w, '0');
  const clock = (t) => `${p2(Math.floor(t / 60))}:${p2(Math.floor(t) % 60)}`;
  const precise = (t) => `${clock(t)}:${p2(Math.round(t * 1000) % 1000, 3)}`;
  const at = (t) => { // the row of the frame showing at t, found by its frame number, so a missing frame shifts nothing
    const f = Math.floor(t * F + 1e-6); let lo = 0, hi = rows.length - 1;
    while (lo < hi) { const mid = (lo + hi + 1) >> 1; if (rows[mid][0] <= f) lo = mid; else hi = mid - 1; }
    return rows[lo];
  };
  const step = [1, 2, 5, 10, 15, 30, 60].find((s) => (s / D) * pw >= 56) ?? 120;
  let s = '';
  for (const v of [0, 25, 50, 75, 100]) s += `<line class="g" x1="${L}" x2="${L + pw}" y1="${y(v)}" y2="${y(v)}"/><text x="${L - 6}" y="${y(v) + 4}" text-anchor="end">${v}</text>`;
  for (let t = 0; t <= D; t += step) s += `<text x="${x(t)}" y="${H - 8}" text-anchor="middle">${clock(t)}</text>`;
  M.forEach(([, , c], i) => {
    let d = '', open = false; // a gap where a frame has no score
    for (const r of rows) { const v = r[i + 1]; if (v == null) { open = false; continue; } d += `${open ? 'L' : 'M'}${x((r[0] + 0.5) / F).toFixed(1)},${y(v).toFixed(1)}`; open = true; }
    s += `<path d="${d}" fill="none" stroke="${c}" stroke-width="1.75" stroke-linejoin="round"/>`;
  });
  for (const m of [...data.peaks, ...data.lows]) {
    const c = M.find(([k]) => k === m.metric)[2], up = m.kind === 'peak', cx = x((m.start + m.end) / 2), cy = y(m.value);
    const tip = up ? cy - 14 : cy + 14, base = up ? cy - 6 : cy + 6;
    const first = (up ? data.peaks : data.lows).find((n) => n.metric === m.metric) === m;
    s += `<path d="M${cx},${tip}L${cx - 5},${base}L${cx + 5},${base}Z" fill="${c}"><title>${m.metric} ${m.kind} ${m.timestamp}: ${m.value.toFixed(1)}</title></path>`;
    if (first) s += `<text x="${cx}" y="${up ? tip - 4 : tip + 12}" text-anchor="middle" style="fill:${c};font-weight:600">${m.timestamp}</text>`;
  }
  s += `<g class="playhead"><line class="ph" y1="${T - 4}" y2="${T + ph}"/><rect class="phl" y="2" width="150" height="18" rx="5"/><text class="pht" y="15" text-anchor="middle"></text><text class="vals" y="${T + 12}"></text></g>`;
  svg.setAttribute('viewBox', `0 0 ${W} ${H}`);
  svg.innerHTML = s;
  fig.querySelector('.nvt-legend').innerHTML = M.map(([, n, c]) => `<span><i style="background:${c}"></i>${n}</span>`).join('');
  const [line, label, labelText, vals] = svg.querySelectorAll('.playhead > *');
  const draw = () => {
    const t = video.currentTime, px = x(t), row = at(t), left = px + 160 > W;
    line.setAttribute('x1', px); line.setAttribute('x2', px);
    label.setAttribute('x', Math.min(Math.max(px - 75, 0), W - 150)); labelText.setAttribute('x', Math.min(Math.max(px, 75), W - 75));
    labelText.textContent = `${precise(t)} · frame ${row[0]}`;
    vals.setAttribute('x', left ? px - 8 : px + 8); vals.setAttribute('text-anchor', left ? 'end' : 'start');
    vals.innerHTML = M.map(([, , c], i) => `<tspan x="${left ? px - 8 : px + 8}" dy="${i ? 15 : 0}" fill="${c}">${row[i + 1] == null ? '–' : row[i + 1].toFixed(1)}</tspan>`).join('');
  };
  const loop = () => { draw(); if (!video.paused) requestAnimationFrame(loop); };
  for (const e of ['play', 'seeked', 'timeupdate', 'loadedmetadata']) video.addEventListener(e, loop);
  const seek = (e) => { const r = svg.getBoundingClientRect(); video.currentTime = Math.min(Math.max((((e.clientX - r.left) * (W / r.width) - L) / pw) * D, 0), D); };
  svg.addEventListener('pointerdown', (e) => { svg.setPointerCapture(e.pointerId); seek(e); svg.onpointermove = seek; });
  svg.addEventListener('pointerup', () => (svg.onpointermove = null));
  for (const b of fig.querySelectorAll('.nvt-views button')) b.addEventListener('click', () => {
    const t = video.currentTime, playing = !video.paused;
    video.src = b.dataset.src; video.currentTime = t; if (playing) video.play();
    for (const o of fig.querySelectorAll('.nvt-views button')) o.setAttribute('aria-pressed', o === b);
  });
  draw();
})();
</script>
```

Then splice each video's frames file into the page, in the folder where the downloads saved it. Name the page and the file by the names you and the download command chose, single-quoted, never by an asset name from the results:

```bash
python3 - 'report.html' 'ad-frames.json' <<'PY'
import sys
page, frames = sys.argv[1], sys.argv[2]
html = open(page).read()
marker = f"/*FRAMES:{frames}*/null"
assert marker in html, "no placeholder for " + frames
open(page, "w").write(html.replace(marker, open(frames).read(), 1))
PY
```

A published page can instead ship the JSON next to it and `fetch` it, but splicing keeps the page one file that also opens from disk.

Leave out the view buttons when only one file is saved. The `<style>` goes in once per page. For several videos, repeat the figure with its script right after it: each script finds the figure above it.

## Docs and decks: a static chart image

A doc or a deck can't play the video, so the chart goes in as an image: the same axes, colours, markers and legend, without the playhead. Draw it in the shell from the frames file, save it as a PNG (about 1600 × 560), and put it in like the heatmaps (section 5 of SKILL.md). With Python and matplotlib:

```python
import json, matplotlib; matplotlib.use("Agg"); import matplotlib.pyplot as plt
v = json.load(open("ad-frames.json"))  # the frames file get_video_timeline's command saved
M = [("focus", "Focus", "#E3A500"), ("cognitive_demand", "Cognitive Demand", "#8255E8"), ("engagement", "Engagement", "#2F7FE0"), ("memory", "Memory", "#E5476B")]
fig, ax = plt.subplots(figsize=(10, 3.5), dpi=160)
for i, (k, name, c) in enumerate(M, start=1):
    pts = [((r[0] + 0.5) / v["fps"], r[i]) for r in v["frames"] if r[i] is not None]
    ax.plot([p[0] for p in pts], [p[1] for p in pts], color=c, lw=1.5, label=name)
    for kind, mark, dy in (("peaks", "^", 4), ("lows", "v", -4)):
        ms = [m for m in v[kind] if m["metric"] == k]
        mid = lambda m: (m["start"] + m["end"]) / 2
        ax.scatter([mid(m) for m in ms], [m["value"] + dy for m in ms], marker=mark, color=c, s=28, zorder=3)
        if ms: ax.annotate(ms[0]["timestamp"], (mid(ms[0]), ms[0]["value"] + 3 * dy), ha="center", fontsize=7, color=c)
ax.set_ylim(0, 100); ax.set_yticks([0, 25, 50, 75, 100]); ax.set_xlim(0, v["duration"])
ax.xaxis.set_major_formatter(lambda t, _: f"{int(t)//60:02d}:{int(t)%60:02d}")
ax.grid(axis="y", color="#ececf0"); [ax.spines[s].set_visible(False) for s in ("top", "right")]
ax.legend(loc="upper center", bbox_to_anchor=(0.5, -0.12), ncol=4, frameon=False)
fig.savefig("video-metrics.png", bbox_inches="tight")
```

Use the file name the download command saved, single-quoted; never pass asset names through the shell. Without Python, draw the same chart as an SVG and convert it, or use the doc's own chart with the four series (a doc's chart may need the frames thinned: keep every frame's highest and lowest value per tenth of a second rather than dropping frames). Beside the chart, put the `get_video_stills` stills of the marked moments (original, attention and fog at each, side by side) and name those moments in the finding cards by their timestamp ("Memory peaks at 0:23.4, when the product is on screen").
