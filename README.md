# Neurons plugin for Claude

Brings Neurons into Claude Code, Cowork and Claude chat:

- **Neurons MCP server** (`.mcp.json`): the person's own analyses, through the Neurons server
  (`https://mcp.neuronsinc.com/mcp`).
- **`analysing-an-asset` skill**: analyse a local image or video: upload it without passing it
  through the conversation, wait for the batch, show and fix the setup Neurons chose (objectives and AOIs), fetch results.
- **`reading-neurons-results` skill**: how to read and compare Neurons scores and turn them into
  creative recommendations. Its rules follow Neurons Lens (the results chat in the hub), and its
  metric definitions come from the Neurons app.
- **`building-neurons-reports` skill**: how to turn results into a report, page or deck where every
  key finding has its heatmap, fog map or AOI image with a caption, benchmark bars and ranked
  recommendations.
- **`building-campaign-audits` skill**: judge a campaign or any large set of assets as a whole, as
  the hub's Campaign Insights does: choose the assets, quick NIS statistics, an audit started only
  after the person confirms, and a layout with the mean NIS, the NIS distribution, band counts and
  one card per power.

## Install

Neurons is listed in the Claude directory. In Claude, open **Settings → Connectors** (or the plugin
directory in Claude Code) and install **Neurons**. You need a Neurons account with the connector
enabled for your company.

## Which server it connects to

The plugin connects to the Neurons server, `https://mcp.neuronsinc.com/mcp`, which asks you to
sign in with your Neurons account:

- **Claude Code:** the first time a tool is used, or from `/mcp`.
- **Claude Desktop, Cowork and claude.ai:** on the plugin's **Connectors** tab, with **Connect**.
  Installing the plugin doesn't start sign-in on its own.

## Uploads and downloads

The plugin has no local helper. To analyse local files (images, videos and GIF files, up to 25 at once), Claude runs the one
command `create_upload` returns, so the files go from your machine straight to Neurons, into the `MCP`
folder of your private workspace in the company you choose. Heatmaps for reports
are downloaded the same way. This needs a shell (Claude Code, the desktop Code tab or Cowork). In chat,
the in-chat uploader takes images (JPEG, PNG and WebP); videos and GIF files need a shell.

Cowork and claude.ai run those commands in a sandbox that only reaches the domains your organization
allows (claude.ai **Settings → Capabilities**; an admin sets it on Team and Enterprise). Allow:

| Domain | For |
| --- | --- |
| `mcp.neuronsinc.com` (or the server you connect to) | uploading images and videos, downloading originals, heatmaps and fog maps for reports |

Without them, uploads fail and reports go out without heatmaps. The tools name the host to allow when
a command is blocked.

## Layout

```
.
├── .claude-plugin/plugin.json   Claude manifest
├── .mcp.json                    Claude server config
├── plugin.json                  ChatGPT and Codex manifest
├── mcp.json                     ChatGPT and Codex server config
├── assets/icon.png
├── LICENSE
└── skills/
    ├── analysing-an-asset/SKILL.md
    ├── building-campaign-audits/
    │   ├── SKILL.md
    │   └── references/nis-histogram.md
    ├── building-neurons-reports/
    │   ├── SKILL.md
    │   └── references/report-skeleton.md
    └── reading-neurons-results/
        ├── SKILL.md
        └── references/{metrics,objectives}.md
```

## Source and licence

This plugin is published from Neurons' own repository after each production release; changes made
here directly are overwritten. Licensed under the MIT licence (see `LICENSE`).
