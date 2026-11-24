# Report skeletons

Fill-in outlines for the three report types. Angle brackets are placeholders; `[visual N]` names a snippet in
`report-visuals.md`, which also says how a doc or a deck draws it (in a doc with charts: the doc's own chart from the
tool's rows, its values printed on its marks, with no table repeating it). Every number comes from a Neurons tool result in the conversation. In a
doc, every `<image>` and thumbnail is a file uploaded into the doc (SKILL.md section 5), never a link.

## Ads: one ad, or a few side by side

For one ad (a deep dive), drop the side-by-side section.

```markdown
# <Report title: what was compared and why>

## Objectives and setup

<Purpose, industry, use case, platform in plain words. Once if shared; per asset if not.>
<If objectives differ: "These ads have different objectives, so each is compared only against its
own benchmark, not ranked against the others.">

## Summary

- <Strongest asset for its objective, and the one reason why.>
- <The biggest problem across the set.>
- <The single most valuable change.>

## Side by side

[visual 7: ranked strip, number 1 first; unnumbered when objectives differ]

| | <Ad A> | <Ad B> |
| --- | --- | --- |
| Asset | <original image> | <original image> |
| Attention | <attention heatmap> | <attention heatmap> |
| Neurons Impact Score | [visual 1: gauge] <7.2 / 10, Outstanding> | [visual 1] <5.8 / 10, Strong> |
| <Prioritised metric> | [visual 8: bar] <value> (range <low–high>) | [visual 8] <value> (range <low–high>) |

## <Ad A>

[visual 1: gauge] NIS <x.x / 10, band>. Built from: <metric score>, <metric score>, <metric score>.

[visual 8: metric bars, the prioritised metrics first; Start, whole-ad and End as separate rows]

### <Claim, one sentence: "Attention goes to the shoe, not the logo.">

<image: the view from the finding table>

*<How to read the colours (from the link's caption). What to notice in this ad.>*

<Metric> <value> against <range or guideline> → <what to do>.

### <Next claim>

…

## Recommendations, ranked by impact

1. **<Change>** (<asset(s)>): moves <metric>, now <value> against <range>. See <image> for <asset>.
2. …

Keep: <the strengths not to break>.

## How to read this

- **Neurons Impact Score**: 1–10 across the metrics that matter for the objective; 7 or above is
  optimised. <knowledge base link>
- **Benchmark range**: <one line>. <link>
- **<Each map used>**: <how to read its colours>. <link>
```

## Groups: ours against a competitor's, or channel against channel

From `compare_groups` (`building-campaign-audits`, section 5). The person's own ads are the first group.

```markdown
# <Our ads vs Competitor: what was compared>

<If the tool warned: a "Check first" box with each warning, before any number.>

## Headline

[visual 1: one gauge per group, side by side, each captioned "over <nisN> of <count>"]
<"Competitor 6.1, Strong; ours 5.4, Strong: 0.7 behind.">

## Metric averages

[visual 3: radar with its table]

## Where we differ

[visual 4: difference chips, with the rule in one line above them]
<The two or three biggest gaps, in words.>

## By channel

[visual 5: split rows, only when split_by was used; only the channels both groups have]

## Fix first

[visual 6: fix-first list with thumbnails, in the tool's order]

## Recommendations

<Two to five, each naming the assets from the fix-first list and the metric it should move.>

## How to read this

<Every mean and difference comes from Neurons; benchmarks depend on each asset's objectives.
Notes: <analyses not read, assets without benchmarks, parts not available>.>
```

## Campaign: a set of assets as a whole

From `campaign_stats` or an audit (`building-campaign-audits`, section 4, which has the full order).

```markdown
# <Campaign title> · <N> creatives analysed

<Period or filter, purpose, platforms with their counts.>

## Impact breakdown

<Neurons' headline sentence, when an audit has one.>
[visual 1: gauge, "mean over N assets"]  [visual 2: band bar with counts and shares]

## Distribution

[the NIS histogram: references/nis-histogram.md in building-campaign-audits]

## Ranked

[visual 7: ranked strip of every asset, number 1 first, when the person wants the assets ranked and
they share one purpose; each NIS from get_analysis_results]

## <Power>: what's working / what needs fixing

<Each finding card as building-campaign-audits section 4 says, its example creatives as a small,
unnumbered visual 7 in the tool's order; in a doc, the thumbnail table SKILL.md section 5 describes.>

## How to read this

<What the NIS and its bands are; every count comes from Neurons.>
```
