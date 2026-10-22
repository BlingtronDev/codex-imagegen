---
name: codex-imagegen
description: Generate or edit raster images (photos, illustrations, textures, sprites, mockups, icons, transparent cutouts) by calling Codex's built-in image generator from the shell. Use when the user asks to create, draw, render or edit a bitmap image, or a task needs an AI-generated picture. Not for diagrams, charts or icons better built as SVG/HTML/code.
---

# Codex Image Generation

`scripts/codex-imagegen` runs `codex exec` with Codex's built-in `image_gen` tool, which uses the user's ChatGPT login (no API key), and copies the result to a path you choose.

**If you are Codex, do not use this skill.** Use your built-in `image_gen` tool directly.

## When to use

- Use it for a new image: a concept, illustration, photo-style shot, product or UI mockup, game asset, cover, or texture.
- Use it to edit an existing image: add, remove, or replace something, change the style or lighting, or cut out the background.
- Use it for a new image guided by reference images for style or composition.

Do not use it for diagrams, charts, flowcharts, simple icons, or anything that should stay editable as SVG/HTML/canvas/code. Build those directly.

## Running it

The script is at `scripts/codex-imagegen` inside this skill's directory (the folder that contains this `SKILL.md`). Replace `<skill-dir>` below with that folder's absolute path.

```bash
<skill-dir>/scripts/codex-imagegen -o OUTPUT.png "PROMPT"
<skill-dir>/scripts/codex-imagegen -i input.png -o output-v2.png "Change only X; keep everything else unchanged"
<skill-dir>/scripts/codex-imagegen -i style.png -o new.png "Image 1 is a style reference. Draw ..."
```

Flags:

- `-o` sets the output path (`.png`, `.jpg` or `.jpeg`).
- `-i` attaches an input image. You can repeat it.
- `-f` overwrites an existing output file.

On success the script prints the absolute path of the saved image. On failure it exits non-zero and prints Codex's reason.

- **Each image takes 30–90 seconds.** Give the shell command a timeout of at least 300 seconds.
- **Each call makes exactly one image.** For several images or variants, make one call per image. Independent calls can run in parallel, each with its own `-o`.
- **Do not overwrite existing assets** unless the user asked for that. Write a sibling such as `hero-v2.png`.
- **Use `.png` for transparent backgrounds.** JPEG drops the alpha channel.
- **Save project assets inside the project**, at the path where the project will use them.

After generating, look at the image if you can view images. If it misses the request, make one targeted follow-up. You can pass the result back with `-i` and describe only the change. Tell the user the saved path.

## Writing the prompt

- **Order:** scene/backdrop → subject → key details → constraints → intended use, such as "website hero" or "game sprite".
- **Detailed requests:** if the request is already specific, write it as a clean spec and don't invent extra elements. If it is vague, add only composition, lighting, and style cues that help.
- **Photorealism:** say `photorealistic` and name real texture, lens, and lighting.
- **Text in the image:** put the exact words in quotes, require verbatim rendering, and describe the font, size, color, and placement.
- **Edits:** say "change only X; keep Y unchanged". Repeat the invariants on every iteration so the image does not drift.
- **Several input images:** give each one a role, for example "Image 1: edit target; Image 2: style reference".
- **Transparent cutouts:** ask for "a genuinely transparent background, clean edges, no halo".
- **Size and shape:** describe the aspect ratio in words, such as "wide 16:9 landscape" or "square". There is no size flag.

More examples are in Codex's own guide: `~/.codex/skills/.system/imagegen/references/prompting.md` and `sample-prompts.md`.
