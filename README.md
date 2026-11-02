# How do I make videos with Claude Code?

Connect a video model to Claude Code over MCP, then ask in plain words. Run `claude mcp add --transport http wireflow https://www.wireflow.ai/api/mcp`, then `/mcp` to sign in with your Wireflow account. Claude writes the prompt, picks a model, tells you the credit cost when you ask and runs it; the MP4 comes back as a link.

Wireflow is our product: this repo is made by the Wireflow team, so read [Other ways to do this](#other-ways-to-do-this) with that in mind. Every other option links to its own vendor page.

[![Frame from a 5 second clip of a paper boat on a neon-lit puddle at night, made with LTX Video by this repo's workflow, click to play](examples/paper-boat-poster.jpg)](https://cdn.wireflow.ai/b04d5200-d70d-11f1-b8d8-b5ea86521a2f.mp4)

Made by this repo's workflow and script on 3 Nov 2026: LTX Video, 7 credits, run `074cb109-14e4-413b-ab8f-d17fd5c7665f` (click the frame for the MP4). At 7 credits a video, LTX Video is the cheapest text-to-video model in the [model list](https://www.wireflow.ai/api/v1/models), and it shows: 768x512 and soft detail.

## Set it up

The steps below are copied from the Wireflow docs pages linked under each host, read on 3 Nov 2026. Video models are on paid Wireflow plans ([pricing](https://www.wireflow.ai/pricing)).

### Claude Code

From [How do I set up an MCP server in Claude Code to generate videos?](https://www.wireflow.ai/docs/generate-videos-in-claude-code) (page updated 29 Oct 2026):

1. Add the server. The scope decides where it loads:

   | Scope | Where it loads | Command |
   |---|---|---|
   | `local` (default) | This project, for you only | `claude mcp add --transport http wireflow https://www.wireflow.ai/api/mcp` |
   | `user` | All your projects on this machine | `claude mcp add --transport http wireflow --scope user https://www.wireflow.ai/api/mcp` |
   | `project` | This project, shared through version control in `.mcp.json` | `claude mcp add --transport http wireflow --scope project https://www.wireflow.ai/api/mcp` |

2. Run `/mcp` inside Claude Code, choose `wireflow` and sign in with your Wireflow account in the browser. The consent screen lists the permissions Claude Code asked for.
3. Run `claude mcp list` and check that `wireflow` shows Connected.

This repo already has the project file, [`.mcp.json`](.mcp.json): open the folder in Claude Code, approve the `wireflow` server, then run `/mcp`. A shared `.mcp.json` needs no key, because each teammate signs in with their own Wireflow account.

### Claude app

From [How do I use Wireflow in Claude to make images and videos?](https://www.wireflow.ai/docs/agents/claude-app) (page updated 26 Oct 2026):

1. Open **Customize > Connectors**.
2. Click **+ Add**, then **Add custom connector**.
3. Enter a name, such as `Wireflow`.
4. Add `https://www.wireflow.ai/api/mcp` as the remote MCP server URL, then click **Continue**.
5. Review the authentication settings Claude detected and click **Continue**. Add no API key: Wireflow signs you in with OAuth.
6. Click **Add**, then sign in with your Wireflow account and approve the consent screen.

If you log in to Claude Code with a Claude.ai account, connectors you added in Claude.ai are available in Claude Code automatically. On Team and Enterprise plans only admins add connectors.

### Cursor

From [How do I add an image and video generation MCP server to Cursor?](https://www.wireflow.ai/docs/generate-images-and-videos-in-cursor) (page updated 24 Oct 2026):

1. Put the `wireflow` entry under `mcpServers` in `~/.cursor/mcp.json` (every project) or `.cursor/mcp.json` (one project). This repo ships [`.cursor/mcp.json`](.cursor/mcp.json).
2. Open **Customize** in the Cursor sidebar and check that the server's toggle is on.
3. When Cursor opens the Wireflow sign-in, sign in and approve the consent screen.

### Codex

From [How do I add an MCP server to Codex CLI?](https://www.wireflow.ai/docs/generate-images-and-videos-with-codex) (page updated 24 Oct 2026):

1. Run `codex mcp add wireflow --url https://www.wireflow.ai/api/mcp`, or copy [`.codex/config.toml`](.codex/config.toml) (project config is read in trusted projects only; the same table works in `~/.codex/config.toml`).
2. Run `codex mcp login wireflow` and sign in with your Wireflow account.
3. Run `codex mcp list` to check, or type `/mcp` inside Codex.

### If Claude Code can't see the tools

| What you see in `claude mcp list` | What to do |
|---|---|
| `wireflow` shows Needs authentication | Run `/mcp`, choose `wireflow` and sign in again |
| `wireflow` is not in the list | It was added in local scope in another project: add it here, or with `--scope user` |
| `wireflow` shows Failed to connect | `claude mcp get wireflow` and check the URL is exactly `https://www.wireflow.ai/api/mcp` |
| The tools load but Claude does not use them | Start your prompt with "Use Wireflow" |

## Run it

Describe the clip, name a model if you have one in mind, and ask for the cost before the run. This prompt is from the Claude Code docs:

```text
Use Wireflow to make a short video of a paper boat drifting across a neon-lit puddle at night with Kling Video. Tell me the credit cost and wait for my yes before you run it. When it is done, save the video to ./wireflow-outputs.
```

Claude typically works through it with these tools:

| Step | Wireflow tool | Spends credits? |
|---|---|---|
| Find the model and its price | `list_models` | No |
| Check your balance | `get_credit_balance` | No |
| Build the workflow as a private draft | `create_workflow_from_prompt` or `clone_template` | No |
| Start the run and get an execution id | `run_workflow` | Yes |
| Read the status and the output URLs | `get_execution` | No |

To cut or tighten a clip afterwards, ask Claude to read it with `get_video_timeline`, show stills with `preview_video_frames` (no credits) and change the cut with `edit_video_timeline`, which needs the opt-in `workflows:write` permission ([Claude app setup](https://www.wireflow.ai/docs/agents/claude-app)).

Prefer a ready-made shot plan? The [ai-video-generation skill](https://github.com/wireflowINC/skills/tree/main/skills/ai-video-generation) turns a text prompt or a start image into a 5 second 16:9 clip with MiniMax Hailuo 03 Max Turbo and quotes before it spends: `npx skills add wireflowINC/skills --skill ai-video-generation`. For a product photo as the first frame, see [ecommerce-product-video](https://github.com/wireflowINC/skills/tree/main/skills/ecommerce-product-video).

### What a clip costs

Credits for a 5 second clip at default settings, from the [model list](https://www.wireflow.ai/api/v1/models) (read 3 Nov 2026). Resolution, length and quality change the price, and you are charged for the real length.

| Video model | Credits |
|---|---|
| Kling Video | 135 |
| Seedance 2.5 | 135 |
| Seedance 2.0 | 90 |
| Wan 2.5 | 80 |
| Wan 3.0 | 58 |
| MiniMax Hailuo 03 Max Turbo | 40 |
| LTX Video | 7 per video |

### From code (a build step, a batch, your own app)

Claude Code can also run a saved workflow from a script, which suits many clips or a build step. Create an API key in Wireflow under **Settings > API Keys** with `workflows:write`, `workflows:execute` and `executions:read` (or **Full access**). Node 18 or newer, no packages.

```bash
export WIREFLOW_API_KEY="your key"          # PowerShell: $env:WIREFLOW_API_KEY = "your key"

# 1. Save this repo's workflow (one Text Input wired into LTX Video) in your account. Free.
node scripts/create-workflow.mjs workflows/text-to-video-ltx.json

# 2. Run it. Video can take minutes; the script waits (default 20 minutes) and saves the MP4.
node scripts/wireflow-run.mjs --workflow <your workflow id> --input prompt="slow push-in on a paper boat on a neon-lit puddle at night"

# Stopped waiting? Nothing is lost: pick the run up again.
node scripts/wireflow-run.mjs --workflow <your workflow id> --poll <run id>
```

Swap LTX Video for Kling Video or Seedance on the canvas and the commands stay the same. The script uses `POST /api/v1/workflows/{id}/run` and its poll route ([Run Workflows](https://www.wireflow.ai/docs/api/run)).

## What's in this repo

| File | What it is |
|---|---|
| [`.mcp.json`](.mcp.json) | Claude Code project config for the Wireflow MCP server (`type: http`) |
| [`.cursor/mcp.json`](.cursor/mcp.json) | Cursor project config |
| [`.codex/config.toml`](.codex/config.toml) | Codex config table, asks before tools that write |
| [`workflows/text-to-video-ltx.json`](workflows/text-to-video-ltx.json) | The tested workflow: Prompt into LTX Video |
| [`scripts/create-workflow.mjs`](scripts/create-workflow.mjs) | Lints the workflow file, saves it in your account, prints its id |
| [`scripts/wireflow-run.mjs`](scripts/wireflow-run.mjs) | Runs your workflow or app, waits or resumes, downloads the MP4, writes `manifest.json` |

## Other ways to do this

Facts below are from each vendor's own pages, as recorded in our tool fact files on the dates shown.

- **[Remotion](https://www.remotion.dev/)**: "Create videos and motion graphics with React", rendered with its server-side rendering API or AWS Lambda, so the video is code you own. It uses the Remotion License, which says "a company license is required for for-profit organizations of a certain size"; read the [licence file](https://github.com/remotion-dev/remotion/blob/main/LICENSE.md) (checked 11 Oct 2026).
- **[Higgsfield MCP](https://higgsfield.ai/mcp)**: a server at `https://mcp.higgsfield.ai/mcp` with connection options for Claude and Claude Code, 30+ image and video models and no API key required, plus ready-made skills (Higgsfield's MCP page, checked 31 Oct 2026).
- **[Pika MCP](https://pika.art/mcp)**: install steps for Claude Code and Codex, with skills that turn a URL, a GitHub repo or a brief into an explainer video; Starter is $10 a month with 900 credits (Pika's MCP and pricing pages, checked 12 Oct 2026).
- **[Comfy Cloud MCP](https://docs.comfy.org/agent-tools/mcp)**: `claude mcp add --transport http comfy-cloud https://cloud.comfy.org/mcp`; searching templates and models is free, and generations need an active Comfy Cloud subscription, with 5 free runs for new users (Comfy docs, checked 11 Oct 2026).
- **[fal MCP](https://fal.ai/docs/documentation/setting-up/mcp)**: a remote OAuth server that finds models, checks pricing and runs generations across fal's catalog; video is paid per second, for example Kling Video v2.5 Turbo Pro at $0.07 a second (fal docs and pricing, checked 11 Oct 2026).

## Tested

| | |
|---|---|
| Date | 3 Nov 2026 (2026-11-02 22:33 UTC) |
| Host | Wireflow REST API, called by `scripts/wireflow-run.mjs` on Node 24, Windows 11, after `scripts/create-workflow.mjs` saved the workflow |
| Model | LTX Video, one 5 second 768x512 MP4, about 90 seconds start to file |
| Run id | `074cb109-14e4-413b-ab8f-d17fd5c7665f` |
| Credits | 7, from the account's credit history |

The MCP setup steps are copied from the Wireflow docs on 3 Nov 2026; the test did not sign in over MCP. We re-check the steps, the model list and this block once a month.

## License

[MIT](LICENSE). The example clip was made by the workflow in this repo.
