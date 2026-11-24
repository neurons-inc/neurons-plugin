# Personas: who the person is, and how that changes the answer

The Neurons hub asks every new member "Who are you?" with three options: **Creative**, **Marketing** and **Insights**. Each wants something different from the same results. The persona changes the **order, depth and wording** of an answer. It never changes the data. Nothing is hidden: "show me everything" always works, for every persona.

## 1. Find the persona

Use the first of these that you have:

1. **The person's own words** in this conversation ("I'm a designer", "I run the brand team", "I'm in research"). They win over everything else, including the hub.
2. **The `Person:` line** in a Neurons tool's answer, e.g. `Person: creative (hub profile: Design, Art Director)`. It comes from the person's hub profile.
3. **Ask once** (section 2), when the line says `Person: unknown`, or when no tool answer has a `Person:` line.

Once you know it, keep it for the whole conversation and never ask again. If the person skips the question or doesn't answer, use the balanced default (section 4) and don't ask again.

When the person describes their role in their own words, take the closest persona. A designer, art director or agency creative is a Creative. A brand, media or campaign manager is in Marketing. A researcher or analyst is in Insights. If none fits, ask.

## 2. Ask once, at the right moment

Ask only when the persona is unknown, and only where there are results to shape:

- **Uploading local files:** right after the upload command has run, before you call `wait_for_analyses`. Neurons is analysing anyway, so the question costs no waiting.
- **Assets already in Neurons** (picked, found or named): just before you show the first results.
- **A campaign audit:** together with the "start the audit?" confirmation.
- **A question with no results** ("what's cognitive demand?"): don't ask. Use the balanced default.

Ask with the host's built-in tool for asking a question with options, if it has one. Make it one question, "Who are you?", with three options, each with a short description:

- **Creative**: what to change in the design
- **Marketing**: which ad to run, and why
- **Insights**: every metric, and how it's measured

Without such a tool, ask in one line, followed by the same three options as a numbered list.

Never ask it in the same turn as the setup-check question or the questions to try. The order is persona, then setup check, then questions to try.

When the person answers and the Neurons tools include `set_persona`, call it with their answer, so the hub remembers it for next time. It fills an empty profile only; if the hub already has a value, it keeps that value and says so. Don't mention it unless it fails. When the person explicitly asks to change their persona ("change my persona to marketing", "switch me to Insights"), call `set_persona` with `change: true`; never when they only describe themselves: that updates their hub profile, so the new persona sticks for next time.

## 3. What changes per persona

| | Creative | Marketing | Insights |
| --- | --- | --- | --- |
| **Open with** | What to change in the design, element by element | The verdict: run it, fix it first, or which variant wins | The full metric profile against benchmark |
| **Numbers** | Two or three: attention on the AOIs that matter (brand, product, headline, CTA) and cognitive demand | NIS with its band, then the two or three metrics that drive the verdict, each against its benchmark range | Every metric against its own benchmark or guideline, with the Start/End windows and the AOI table |
| **Benchmarks** | Only where one explains a design problem | One line: inside, above or below the range | Each metric's range, and what the benchmark is based on (the objectives) |
| **Wording** | Visual words ("the logo is lost behind the model"). Explain any score name you use. | Outcomes ("likely to be remembered", "stands out in the feed"). No neuroscience terms. | Exact metric names, as in `metrics.md`. Caveats and methodology are welcome. |
| **Recommendations** | Concrete design edits, the biggest gain first | Impact, priority and effort, and which ad to put the budget behind | What the scores do and don't measure, how sure they are, and what to test next |
| **Length** | Short and visual. Offer the heatmap. | Short and decisive | As long as the results need |
| **Report** (`building-neurons-reports`) | Design notes per asset, with the heatmap and the AOI images up front | A one-page verdict for stakeholders, with benchmark bars | Full metric tables, an extended "How to read this", and an appendix |
| **Campaign audit** (`building-campaign-audits`) | The "What needs fixing" cards and their example creatives first | The average NIS, the band shares, and what to scale or cut | The distribution and every power's findings, plus what was left out and why |
| **Questions to try first** | "What to improve" | The highest-impact change before launch, the stakeholder update, then what's working | How the metrics connect, what to read in Neurons Knowledge, then what to prioritise |

The rules in `reading-neurons-results` hold for every persona. Ground every number, compare like with like, and use benchmark or guideline as the metric calls for. Never use percentiles: the tools don't give them.

## 4. Balanced default (persona unknown or skipped)

Use the Marketing order: verdict, NIS and band, the two or three metrics that matter, then concrete changes. Keep the wording plain. Add the Insights detail (every metric, windows, methodology) only when the person asks for it.

## 5. A report's audience is not the person

A report can be for someone else: "this is for our CMO" or "for the agency that made the ads". When a report has a named audience, write it in that audience's lens: a CMO or client gets Marketing, an agency or designer gets Creative, and a research team gets Insights. If the audience isn't clear, use the person's own persona. The chat itself stays in the person's lens.
