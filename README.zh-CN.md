# codex-imagegen

[English](README.md) | 简体中文

让你的编程智能体也能生成图片。这个技能调用的是 [Codex CLI](https://github.com/openai/codex) 自带的 `image_gen` 工具，直接用你的 ChatGPT 账号登录，不用配置 `OPENAI_API_KEY`。

整个技能就是一个 shell 脚本，再加一个 `SKILL.md`，告诉智能体什么时候该用、怎么用。

## 支持的智能体

支持 Claude Code、Cursor、GitHub Copilot、Gemini CLI、OpenCode、Windsurf、pi 等 75 个智能体。只要 [`skills` 命令](https://github.com/vercel-labs/skills/blob/main/docs/agents.md)能装进去、又能执行 shell 命令，就能用。

<details>
<summary>查看全部 75 个</summary>

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

Codex 不在名单里，是因为它自己就能直接调用 `image_gen`，用不着这个技能。

## 准备工作

- 安装 [Codex CLI](https://github.com/openai/codex)，并用 `codex login` 登录一个能生成图片的 ChatGPT 账号
- 安装 `bash` 和 [`jq`](https://jqlang.org/)
- 想输出 `.jpg`/`.jpeg` 的话需要 macOS（脚本用 `sips` 转格式）。PNG 在哪个系统上都行。

## 安装

```bash
npx skills@latest add BlingtronDev/codex-imagegen -g
```

它会自动找到你电脑上装了哪些智能体，把技能分别装进去。加 `-g` 是装到全局，所有项目都能用；不加就只装到当前项目。想自己指定装给哪些智能体，可以加 `-a`，比如 `-a claude-code cursor`。

## 怎么用

装好以后，直接跟智能体说你要什么图就行，比如“帮我做一张 16:9 的首页横幅图，画的是……”，它会自己用上这个技能。

你也可以自己运行脚本：

```bash
# 生成一张新图
scripts/codex-imagegen -o hero.png "一张 16:9 的写实风格宽幅照片，画的是……"

# 改一张现有的图
scripts/codex-imagegen -i hero.png -o hero-v2.png "只把天空换成日落，别的都别动"

# 参照别的图来画
scripts/codex-imagegen -i style.png -o new.png "图 1 是风格参考，照这个风格画……"
```

| 参数 | 作用 |
| --- | --- |
| `-o OUTPUT` | 图片存到哪里（`.png`、`.jpg` 或 `.jpeg`）。不写的话默认是 `./codex-image-<时间戳>.png` |
| `-i IMAGE` | 要修改或参考的图片，可以写多个。 |
| `-f` | `OUTPUT` 已经存在时直接覆盖。 |
| `-h` | 查看帮助。 |

成功的话，脚本会打印图片保存的完整路径；失败的话，会以非零状态码退出，并打印 Codex 给出的原因。一张图一般要等 30–90 秒。

## 原理

脚本会在一个临时目录里，用只读沙箱运行 `codex exec`，让 Codex 用 `image_gen` 画一张图。画完后，脚本从 Codex 输出的 JSON 事件里拿到 thread ID，再把 `$CODEX_HOME/generated_images/<thread_id>/` 里最新的那张图复制到你指定的位置。

## 许可证

[MIT](LICENSE)
