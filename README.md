# codex-imagegen

English | [简体中文](README.zh-CN.md)

An agent skill that lets your coding agent generate images through [Codex CLI](https://github.com/openai/codex)'s built-in `image_gen` tool. It uses your ChatGPT login, so no `OPENAI_API_KEY` is needed.

The skill is a single shell script plus a `SKILL.md` that tells the agent when and how to call it.

## Supported agents

Works with Claude Code, Cursor, GitHub Copilot, Gemini CLI, OpenCode, Windsurf, pi and 68 more: any agent the [`skills` CLI](https://github.com/vercel-labs/skills/blob/main/docs/agents.md) can install to that can run shell commands.

<details>
<summary>All 75 agents</summary>

<table>
<tr><td>AdaL</td><td>AiderDesk</td><td>Amp</td><td>Antigravity</td><td>Antigravity CLI</td></tr>
<tr><td>AstrBot</td><td>Augment</td><td>Autohand Code CLI</td><td>Claude Code</td><td>Cline</td></tr>
<tr><td>Code Studio</td><td>CodeArts Agent</td><td>CodeBuddy</td><td>Codemaker</td><td>Command Code</td></tr>
<tr><td>Continue</td><td>Cortex Code</td><td>Crush</td><td>Cursor</td><td>Deep Agents</td></tr>
<tr><td>Devin for Terminal</td><td>Dexto</td><td>Droid</td><td>Eve</td><td>Firebender</td></tr>
<tr><td>ForgeCode</td><td>fx</td><td>Gemini CLI</td><td>GitHub Copilot</td><td>Goose</td></tr>
<tr><td>Grok Build</td><td>Hermes Agent</td><td>IBM Bob</td><td>iFlow CLI</td><td>inference.sh</td></tr>
<tr><td>Jazz</td><td>Junie</td><td>Kilo Code</td><td>Kimchi</td><td>Kimi Code CLI</td></tr>
<tr><td>Kiro CLI</td><td>Kode</td><td>Lingma</td><td>Loaf</td><td>MCPJam</td></tr>
<tr><td>MiniMax Code</td><td>Mistral Vibe</td><td>Moxby</td><td>Mux</td><td>Neovate</td></tr>
<tr><td>Ona</td><td>OpenClaw</td><td>OpenCode</td><td>OpenHands</td><td>pi</td></tr>
<tr><td>Pochi</td><td>Posit Assistant</td><td>PromptScript</td><td>Qoder</td><td>Qwen Code</td></tr>
<tr><td>Reasonix</td><td>Replit</td><td>Roo Code</td><td>Rovo Dev</td><td>Sarvam Code</td></tr>
<tr><td>Tabnine CLI</td><td>Terramind</td><td>Tinycloud</td><td>Trae</td><td>Warp</td></tr>
<tr><td>Windsurf</td><td>ZCode</td><td>Zed</td><td>Zencoder</td><td>Zenflow</td></tr>
</table>

</details>

Codex is left out on purpose: it can call `image_gen` directly, so it does not need this skill.

## Requirements

- [Codex CLI](https://github.com/openai/codex), signed in with a ChatGPT account that has image generation (`codex login`)
- `bash` and [`jq`](https://jqlang.org/)
- macOS, for `.jpg`/`.jpeg` output (converted with `sips`). PNG output works anywhere.

## Install

```bash
npx skills@latest add BlingtronDev/codex-imagegen -g
```

The CLI detects the agents you have installed and links the skill into each one. `-g` installs it for your user so every project can use it; leave it off to install into the current project only. To pick agents yourself, add `-a`, for example `-a claude-code cursor`.

## Usage

Once installed, ask your agent for an image ("make a 16:9 hero image of ...") and it will pick up the skill. You can also run the script yourself:

```bash
# New image
scripts/codex-imagegen -o hero.png "A wide 16:9 photorealistic shot of ..."

# Edit an image
scripts/codex-imagegen -i hero.png -o hero-v2.png "Change only the sky to sunset; keep everything else unchanged"

# Use reference images
scripts/codex-imagegen -i style.png -o new.png "Image 1 is a style reference. Draw ..."
```

| Flag | Meaning |
| --- | --- |
| `-o OUTPUT` | Output path (`.png`, `.jpg` or `.jpeg`). Default: `./codex-image-<timestamp>.png` |
| `-i IMAGE` | Input image to edit or use as a reference. Repeatable. |
| `-f` | Overwrite `OUTPUT` if it exists. |
| `-h` | Show help. |

On success the script prints the absolute path of the saved image. On failure it exits non-zero and prints the reason Codex gave. Each image takes about 30–90 seconds.

## How it works

The script runs `codex exec` in a read-only sandbox inside a temporary directory, asks Codex to make exactly one image with `image_gen`, then reads the thread ID from Codex's JSON event stream and copies the newest image from `$CODEX_HOME/generated_images/<thread_id>/` to your output path.

## License

[MIT](LICENSE)
