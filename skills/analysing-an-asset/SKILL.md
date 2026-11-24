---
name: analysing-an-asset
description: Use when the person wants Neurons to analyse image or video files they have locally ("run this ad through Neurons", "analyse ~/Desktop/banner.png", "analyse the ads in this folder", "how will this video do?"). Covers uploading several files at once without passing them through the conversation, choosing the company, waiting for the analyses, going straight to the results, and checking the setup Neurons chose (objectives and AOIs) with an optional fix.
---

# Analysing an asset with Neurons

One analysis per file, uploaded together, then straight to the results, with a short check of the setup and an optional fix. Use the Neurons tools in this order. Without a shell, or in a sandbox without internet access, skip steps 1 and 2: call `upload_files` for images, the person drops them in the chat and their browser uploads them, then go on to step 3 with the analysis ids their next message lists; videos need a shell with internet access. Never read a file's contents into the conversation, and never base64-encode it: the upload goes straight from the person's machine to Neurons. `upload_image_data` takes base64 images, but only for pages and apps that already hold the bytes (an artifact calling Neurons through its `mcp` capability): never call it from the chat, where `create_upload` and `upload_files` are the way.

## 1. Check the files

Check all the files with one shell command: that each exists, and its size in bytes (`wc -c '<path>' ...`). Take the type from the extension: `.jpg`/`.jpeg` image/jpeg, `.png` image/png, `.webp` image/webp, `.mp4` video/mp4, `.mov` video/quicktime, `.webm` video/webm, `.mkv` video/x-matroska, `.avi` video/x-msvideo. Neurons takes images up to 20 MB and videos from 1 to 500 MB. If a file is missing, another type or out of range, tell the person and leave it out.

## 2. Upload

1. Call `create_upload` once with all the files: name, type and exact size each. One answer holds links for about 50 images or 9 videos; for more, it says which files come next: run its command first, then call it again with those.
   - Uploads go into the `MCP` folder of the person's private workspace in one company. If they belong to several, `create_upload` lists them instead: ask which one, then call it again with that `company_url`.
2. Run the one command it returns exactly as given, with only each `<path to …>` replaced by the real single-quoted path. Don't build or change an upload command yourself. It sends every file straight to Neurons and prints one line per file with its name and analysis id.
   - **Images** are analysed while they upload, so allow a minute or two.
   - **Videos** upload in chunks, then start their analysis; their ids are also in the `create_upload` answer. A large video can take a while to send: the command prints its line only when the whole video is in.
   - Don't ask about objectives before uploading: Neurons sets each upload up itself.
3. The tokens in the command (the `X-Neurons-Upload` headers) are credentials for these uploads. Don't repeat them to the person or save them anywhere.
4. Each link works once. If a file fails, act on its message (an expired link: ask for a new one; wrong type or size: re-check step 1; an expired video link: do what its message says, `start_analysis` with the analysis id it gives or a new link; a video that stopped part way: run the command again, it carries on where it stopped). A file that says its link was already used is uploaded already: don't run the command again for it.
5. **If the sandbox can't reach the upload host** (connection refused, a 403 from a proxy, "host not allowed"), it's the sandbox's network, not the files or Neurons. Offer two ways on, in this order:
   1. **The in-chat uploader, for images:** call `upload_files` (with the same `company_url`). The person drops the images there and their browser uploads them, with no settings to change. Their next message lists the analysis ids: go on to step 3 with them. It shows in hosts with MCP apps. It doesn't take videos yet.
   2. **Allow the host** (the only way for videos): the person, or an admin for Team and Enterprise, adds the hosts `create_upload` names to the allowed domains (in the app's settings, under Capabilities). Then run the same command again.

## 3. Wait (and ask who the person is, if you don't know)

If you don't know the person's persona yet (Creative, Marketing or Insights), ask now, once, before the first `wait_for_analyses` call: Neurons is analysing anyway, so the question costs no waiting. You don't know it when the person hasn't said and `create_upload` (or `upload_files`) has no `Person:` line naming one. The `reading-neurons-results` skill's `references/personas.md` gives the wording, the three options and what to do with the answer. Don't ask when you already know, and never ask twice.

Call `wait_for_analyses` once with all the analysis ids. Each call waits up to about 25 seconds and reports every file: analysing, setting up, done or failed. Call it again with the ids still running until all are finished. Images are usually done within a minute; videos take several minutes, so tell the person it's running rather than going quiet. If a file failed, say so and offer to upload it again.

## 4. Results first

Don't show the setup or ask "anything off?" first: go straight to the results. Call `get_analysis_results` once with all the files' ids (it loads them in parallel; past 10 it says how to read the rest), and `compare_analyses` across the batch when there are 2 to 10. For more than 10, compare them with `campaign_stats` or `compare_groups` instead. Then follow the `reading-neurons-results` skill:

- Shape the answer to the person's persona, as the "Open with" row of `references/personas.md` in `reading-neurons-results` says. Whatever the persona, include the Neurons Impact Score and what it means for the objectives, and the one or two things most worth changing, with evidence from the results.
- When the files have different objectives, say so before comparing them: their benchmarks differ.
- Leave images out of the conversation unless the person explicitly asks to see one. If the person wants a report, page, deck or canvas, build it with the `building-neurons-reports` skill, which backs each finding with the asset's heatmaps.
- Keep the analysis ids in mind for follow-up questions. Results already in this conversation stay valid: quote them, and don't fetch the same analysis again unless its setup changed or its AI insight was still being written.
- If the person wants the uploads organised (they land in the `MCP` folder), offer to move them into a folder with `move_assets`, creating it first with `create_folder` if needed; `find_assets` gives the folder and workspace ids.

## 5. Setup check, in the same answer

Neurons sets each upload up automatically: the objectives it's benchmarked against (purpose, industry, use case, platform) and its areas of interest (AOIs: logo, product, headline, CTA, body text). Objectives decide the benchmark and which metrics count towards the Neurons Impact Score; AOIs decide the AOI scores.

1. Call `get_setup` with all the ids and `include_overlay: true`: you get the setup and every image with its AOIs drawn on (scaled down past 4 images). It answers 25 per call; for a larger batch it says how to read the rest, and the check covers every one.
2. After the results, add the short check `get_setup`'s description lays out, as text, covering every image in the batch (short means few words, not few images; in a bulk upload, look at each overlay): only what looks off and what it changes in the results, or one line that the setup looks right. Report only what you see on the overlay for this asset; never reuse an example from these instructions.
3. Then offer the next step as clickable choices, one question for all the files. If the host gives you a tool for asking the person a question with options, use it. When something looks off, adapt the options to what you found, for example:
   - **Fix it for me**: you apply the fix you described (step 6), outlines included: moving a box onto the right element or drawing a missing one is a `fix_setup` call, not something only the editor can do.
   - **Open the AOI editor**: call `show_aois` with the images and `issues`: per analysis that looks off, the one short plain line you wrote (e.g. "CTA AOI covers the headline, not the button"). The editor shows it as a banner above that image. Then wait with `wait_for_setup_review` as in step 6.
   - **Looks right**: the person confirmed the setup, so call `fix_setup` with those analyses and no changes.

   When nothing looks off, say so in one line and still offer a choice: the person may want AOIs of their own, such as a product, brand or text area.
   - **Set up my own AOIs**: call `show_aois` with up to 4 of the images (no `issues`; ask which ones when there are more), then wait with `wait_for_setup_review` as in step 6. Where the editor doesn't render (hosts without MCP apps, such as terminal, coding and file-working agents), ask which AOIs they want instead, draw them with `fix_setup` (`add`), then show the overlay so they can nudge it.
   - **Continue to results**: go on with the results and ask nothing more about the setup. It stays as Neurons made it, unreviewed, and the person can still ask to change it.

   Without such a tool, end your answer with a short numbered list of the same options. Don't tell the person to "widen it in the AOI editor" without offering to open it.
4. For one asset, `get_analysis_results` ends with **questions to try**, the same questions Neurons Lens offers. Make them the next step once the setup question is settled (on **Continue to results**, straight away), as the instructions say: Lens's headings, every question word for word, and as the options exactly the ones `get_analysis_results` names, in its order: the order of the list, so the picker reads like the list. Answer a picked question as the `reading-neurons-results` skill says under "Questions to try and Neurons knowledge".
5. For videos, offer to watch the results in the chat as an option, **Watch the video results**, never as a sentence of prose ("I can open the video results view next…") and never as a question of its own. Put it in the question you ask anyway, as `get_analysis_results` says: among the questions to try options (one video), or else among the setup question's options, or else in one "What next?" question with the other next steps. Use the host's tool for asking a question with options; only without one, make it one item of the numbered list. On that choice, call `show_video_results`. Leave it out in hosts without MCP apps, such as terminal, coding and file-working agents, and never open the view unasked.

## 6. Optional fix

Editing is optional: act only when the person asks or accepts a nudge.

- **Objectives, AOI names, types, hiding:** `fix_setup`, one call for all the files, with only the changes they asked for (objective values as `get_setup` shows them; AOI changes by AOI id).
- **Outlines, new AOIs, done for them:** when the person asks you to fix an outline ("set the wordmark for me"), do it with `fix_setup`: `box` on the AOI to move or resize it, or `add` for a new one, in fractions of the image (0 to 1) from its top-left corner. Start from the box `get_setup` lists for that AOI and the overlay. You draw from a picture, so then show the result with `show_aois` (or the overlay where the editor doesn't render) and let the person nudge it. Moving, resizing or redrawing an image's outlines is yours to do, small ones and several at once included: never tell the person you can't redraw one. When the spot is fiddly, make your best fit from the overlay and let them nudge it. (A video's AOIs are changed in the hub.)
- **Outlines, new AOIs, by the person:** `show_aois` (up to 4 images per call, with the same `issues` as your check) opens the AOI editor in hosts that show MCP apps. The editor can't start your turn: right after `show_aois`, call `wait_for_setup_review` with the arguments its result gives and keep calling it with the ids still open, without asking the person to type anything. After about 8 rounds, ask whether they're still editing.
- **Hosts without MCP apps:** the editor doesn't render there (you get the overlay image only), so fixes go through `fix_setup` or the hub link from `get_setup`. Video AOIs can only be changed in the hub.
- If `fix_setup` says the AOI service isn't answering, say so and offer the hub link or a retry.
- After any fix, call `get_analysis_results` again and say what changed against before: the Neurons Impact Score and the AOI metrics.

A setup counts as reviewed only when a person confirmed it: Approve, Approve all or Save AOIs in the editor, or the person saying it's right (then `fix_setup` with no changes). Never confirm a setup yourself because your check found nothing.

## Without the setup tools

If `get_setup` says the setup isn't available (a local dev server on the customers API), fall back to `start_analysis` per file: show the suggested objectives, confirm them with the person, apply them with `set_objectives`, then `wait_for_analysis` and `get_analysis_results`.
