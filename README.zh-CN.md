# codex-imagegen

[English](README.md) | 简体中文

让你的编程智能体也能生成图片。这个技能调用的是 [Codex CLI](https://github.com/openai/codex) 自带的 `image_gen` 工具，直接用你的 ChatGPT 账号登录，不用配置 `OPENAI_API_KEY`。

整个技能就是一个 shell 脚本，再加一个 `SKILL.md`，告诉智能体什么时候该用、怎么用。

## 支持的智能体

- [Claude Code](https://claude.com/claude-code)
- [pi](https://github.com/badlogic/pi-mono)

其他支持 `SKILL.md` 技能、又能执行 shell 命令的智能体，一般也能用。Codex 本身就能直接调用 `image_gen`，不需要装这个技能。

## 准备工作

- 安装 [Codex CLI](https://github.com/openai/codex)，并用 `codex login` 登录一个能生成图片的 ChatGPT 账号
- 安装 `bash` 和 [`jq`](https://jqlang.org/)
- 想输出 `.jpg`/`.jpeg` 的话需要 macOS（脚本用 `sips` 转格式）。PNG 在哪个系统上都行。

## 安装

用 [`skills`](https://github.com/vercel-labs/skills) 一行命令装好：

```bash
npx skills@latest add BlingtronDev/codex-imagegen -g
```

加 `-g` 是装到全局，所有项目都能用；不加就只装到当前项目。

也可以手动装：先把仓库克隆下来，再链接到智能体的技能文件夹。

```bash
git clone https://github.com/BlingtronDev/codex-imagegen.git ~/.agents/skills/codex-imagegen

# Claude Code
mkdir -p ~/.claude/skills
ln -s ../../.agents/skills/codex-imagegen ~/.claude/skills/codex-imagegen

# pi
mkdir -p ~/.pi/agent/skills
ln -s ../../../.agents/skills/codex-imagegen ~/.pi/agent/skills/codex-imagegen
```

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
