# Neurons metrics

From the Neurons knowledge base (https://knowledge.neuronsinc.com) and the metric descriptions in the
Neurons app. Link the matching article when you explain a metric. Don't add definitions, scales or
thresholds that aren't here.

## Neurons Impact Score (NIS)

- One score per asset from **1 to 10**: how strongly the creative is likely to capture attention,
  engage viewers emotionally and be remembered.
- Built per prioritised metric: the raw score is compared with the benchmark range for the asset's
  objectives, placed in a bucket (low, medium, high), adjusted for how close it is to the ideal range,
  and converted to 1–10. NIS is the average of those metric scores; metrics that don't apply are
  skipped, not penalised.
- Which metrics count depends on the objectives: creative type and purpose (brand building or
  conversion), then industry, use case and platform.
- **7 or above** means the asset is optimised ("Good Job" in the app); below that the app suggests
  "Optimize".
- Bands in the Neurons app: **1–2.9 Minimal, 3–4.9 Moderate, 5–6.9 Strong, 7–10 Outstanding**.
  The app shows the score cut to one decimal, never rounded up.
- Article: https://knowledge.neuronsinc.com/understanding-the-neurons-impact-score-nis

## Benchmarks

- Built from over 10,000 images and 7,000 videos, grouped by objective and category (industry, use
  case). Each metric's range is split into five buckets.
- The recommended range is usually the top two buckets (roughly the top 20% of high-impact assets in
  that segment). **Cognitive Demand is the exception: its recommended range is medium.**
- Article: https://knowledge.neuronsinc.com/how-our-benchmarks-are-calculated

## Asset-level metrics

**Clarity is deprecated.** Neurons no longer uses it: don't report, interpret, compare or recommend
on Clarity, and don't use the clarity heatmap, even when a tool or an older analysis still returns
them. If someone asks about it, say it's deprecated and point to Cognitive Demand and Focus instead.

| Metric | Scale and bands | Direction | Definition |
|---|---|---|---|
| Focus | 0–100. Low 0–24, high 75–100 | Higher means attention lands on a few narrow areas | An index of focused attention: how much of the asset draws attention, and how much viewers agree on where to look. Low means scattered attention. |
| Cognitive Demand | 0–100. Low 0–24, high 75–100 | **Not "higher is better"**: the benchmark target is medium | The amount of visual information viewers have to process. Too low may shorten viewing; too high is stressful and harder to remember. |
| Engagement | 0–100. The knowledge base gives two different band sets, so don't quote bands | Higher is better | The positive emotional response to the asset from viewers' gut reactions (interest, engagement, happiness). |
| Memory | 0–100. Low 0–40, medium 41–70, high 71–100 | Higher is better | Ad recall probability: how likely viewers are to remember the asset after brief exposure (under 2 seconds) followed by distraction. Driven by faces, emotional impact, contrast, clear focal points and prominent text. |
| Trust | 0–100. Low 0–40, medium 41–60, high 61–100 | Higher is better | How credible, sincere and emotionally aligned the creative feels. Judged against these bands (a guideline), not an industry benchmark. API only. |
| Avoidance | 0–100. Low 0–25, medium 26–40, high 41–100 | **Lower is better** | The degree of emotional rejection, disinterest or irritation viewers feel (annoying, boring). Judged against these bands (a guideline), not an industry benchmark. High Avoidance isn't always fatal: some such content still scores well on recall. API only. |
| Intent | 0–100. Low 0–40, medium 41–60, high above 60 | Higher is better | Whether viewers feel motivated to act after seeing the creative ("explore", "try", "buy"): immediate readiness to act, distinct from attention or engagement. Suits conversion. Documented for video, scored frame by frame. API only. |

Articles: https://knowledge.neuronsinc.com/neurons-ai-metrics-and-explanations,
https://knowledge.neuronsinc.com/neurons-engagement-prediction,
https://knowledge.neuronsinc.com/neurons-memory-prediction,
https://knowledge.neuronsinc.com/neurons-intent-prediction,
https://knowledge.neuronsinc.com/neurons-trust-prediction,
https://knowledge.neuronsinc.com/neurons-avoidance-prediction

## What moves the scores

Use these as the basis for creative actions. They are the drivers Neurons publishes, not guarantees.

- **Memory** rises with faces and people, strong emotional impact, contrast and bold colour, a clear
  focus on the key product or object, prominent meaningful text, and simple designs with clear focal
  points. In video, also vivid colour, distinctive dynamic content and rich scenes.
- **Engagement** rises with emotive faces, people, food and nature, strong contrast and bold colour,
  and consistent, focused visuals with one clear key object and simple messaging. In video, also
  sustained focus and conceptually rich scenes.
- **Intent** is driven most by Engagement, then early Focus, moderate (not excessive) Cognitive Demand,
  and clear, uncluttered product exposure early on.
- **Cognitive Demand**: simplify what the heatmap shows as red when it's above the medium target; keep
  it lower towards the end so a call to action lands.
- Video scores are built frame by frame. The models don't account for audio, motion or sequence.

## Areas of interest (AOIs)

- An AOI is an element drawn on the asset so it can be scored. Giving it a type (see
  `references/objectives.md`) turns on AOI-level benchmarks.
- AOI metrics:
  - **Total Attention**: the percentage of visual attention inside the AOI over the whole exposure.
    **Start** and **End Attention**: the same for the first and last 2 seconds.
  - **Percentage Seen** (images): the share of viewers who look at the AOI at least once.
  - **Time Spent** (images): predicted time on the AOI during the 5-second exposure.
  - **Availability** (video): how long the AOI is on screen.
  - **Relative Attention** (video): attention on the AOI while it is on screen, i.e. how
    attention-grabbing it is when shown.
- Articles: https://knowledge.neuronsinc.com/what-is-an-aoi,
  https://knowledge.neuronsinc.com/metrics-and-explanations

## Start and End

- Images are scored over a standard 5-second exposure: **Start** is the first 2 seconds, **End** the
  last 2. For video, Start and End are the first and last 2-second segments.
- Attention and Focus have Start and End variants for images and video. Cognitive Demand, Engagement
  and Memory have them for video only.
- Low Start Attention on the brand means viewers may scroll past without linking the content to the
  brand. Higher complexity can help at the start, but keeping Cognitive Demand lower at the end helps a
  call to action land. A drop in End Memory suggests the brand message gets lost.
- Article: https://knowledge.neuronsinc.com/start-end-metrics-measuring-temporal-impact

## Heatmaps

The colours don't mean the same thing on every map:

- **Attention heatmap**: green to red by cumulative fixation time. **Red is more attention.**
- **Attention fogmap**: the same data reversed. Fog clears where people look; whatever stays fogged
  is likely to be missed.
- **Cognitive Demand heatmap**: **red is high complexity**, green low. Use it to see what to simplify.
- **Engagement and Memory heatmaps** (images only): **green contributes most** to the score, red least.
- Article: https://knowledge.neuronsinc.com/neurons-ai-heatmaps-and-interpretation

## API names

`focus` / `focus_total` (total), `focus_first_two` (start), `focus_last_two` (end), `cognitive_demand`,
`engagement_frt`, `memory`, `trust`, `avoidance`, `intent`, `overall_impact_score` (NIS).
`clarity` is deprecated: ignore it if it appears.
`_start` and `_end` suffixes are the Start and End variants. Use the labels above, not these names,
when talking to people.
