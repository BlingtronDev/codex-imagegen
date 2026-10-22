# codex-imagegen

English | [简体中文](README.zh-CN.md)

An agent skill that lets your coding agent generate images through [Codex CLI](https://github.com/openai/codex)'s built-in `image_gen` tool. It uses your ChatGPT login, so no `OPENAI_API_KEY` is needed.

The skill is a single shell script plus a `SKILL.md` that tells the agent when and how to call it.

## Supported agents

Any agent that the [`skills` CLI](https://github.com/vercel-labs/skills/blob/main/docs/agents.md) can install to and that can run shell commands. That is 75 agents so far, including Claude Code, Cursor, GitHub Copilot, Gemini CLI, OpenCode, Windsurf and pi:

AdaL, AiderDesk, Amp, Antigravity, Antigravity CLI, AstrBot, Augment, Autohand Code CLI, Claude Code, Cline, Code Studio, CodeArts Agent, CodeBuddy, Codemaker, Command Code, Continue, Cortex Code, Crush, Cursor, Deep Agents, Devin for Terminal, Dexto, Droid, Eve, Firebender, ForgeCode, fx, Gemini CLI, GitHub Copilot, Goose, Grok Build, Hermes Agent, IBM Bob, iFlow CLI, inference.sh, Jazz, Junie, Kilo Code, Kimchi, Kimi Code CLI, Kiro CLI, Kode, Lingma, Loaf, MCPJam, MiniMax Code, Mistral Vibe, Moxby, Mux, Neovate, Ona, OpenClaw, OpenCode, OpenHands, pi, Pochi, Posit Assistant, PromptScript, Qoder, Qwen Code, Reasonix, Replit, Roo Code, Rovo Dev, Sarvam Code, Tabnine CLI, Terramind, Tinycloud, Trae, Warp, Windsurf, ZCode, Zed, Zencoder, Zenflow.

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
