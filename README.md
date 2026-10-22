# codex-imagegen

An agent skill that lets coding agents such as Claude Code generate and edit raster images through [Codex CLI](https://github.com/openai/codex)'s built-in `image_gen` tool. It uses your ChatGPT login, so no `OPENAI_API_KEY` is needed.

The skill is a single shell script plus a `SKILL.md` that tells the agent when and how to call it.

## Requirements

- [Codex CLI](https://github.com/openai/codex), signed in with a ChatGPT account that has image generation (`codex login`)
- `bash` and [`jq`](https://jqlang.org/)
- macOS, for `.jpg`/`.jpeg` output (converted with `sips`). PNG output works anywhere.

## Install

With the [`skills`](https://github.com/vercel-labs/skills) CLI:

```bash
npx skills@latest add BlingtronDev/codex-imagegen -g
```

`-g` installs it for your user so every project can use it. Leave it off to install into the current project only.

Or install by hand. Clone the repo, then link it into your agent's skills folder. For Claude Code:

```bash
git clone https://github.com/BlingtronDev/codex-imagegen.git ~/.agents/skills/codex-imagegen
mkdir -p ~/.claude/skills
ln -s ../../.agents/skills/codex-imagegen ~/.claude/skills/codex-imagegen
```

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
