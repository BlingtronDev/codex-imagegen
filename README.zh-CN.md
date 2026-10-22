# codex-imagegen

[English](README.md) | 简体中文

一个智能体技能（agent skill），让 Claude Code 等编程智能体通过 [Codex CLI](https://github.com/openai/codex) 内置的 `image_gen` 工具生成和编辑位图图像。它使用你的 ChatGPT 登录，不需要 `OPENAI_API_KEY`。

这个技能只有一个 shell 脚本，外加一个 `SKILL.md`，用来告诉智能体什么时候调用脚本、怎么调用。

## 环境要求

- [Codex CLI](https://github.com/openai/codex)，并用支持图像生成的 ChatGPT 账号登录（`codex login`）
- `bash` 和 [`jq`](https://jqlang.org/)
- 输出 `.jpg`/`.jpeg` 需要 macOS（用 `sips` 转换）。输出 PNG 在任何系统上都可以。

## 安装

用 [`skills`](https://github.com/vercel-labs/skills) 命令行工具安装：

```bash
npx skills@latest add BlingtronDev/codex-imagegen -g
```

`-g` 会为当前用户全局安装，所有项目都能用。不加 `-g` 则只安装到当前项目。

也可以手动安装：先克隆仓库，再链接到智能体的技能文件夹。以 Claude Code 为例：

```bash
git clone https://github.com/BlingtronDev/codex-imagegen.git ~/.agents/skills/codex-imagegen
mkdir -p ~/.claude/skills
ln -s ../../.agents/skills/codex-imagegen ~/.claude/skills/codex-imagegen
```

## 使用方法

安装后，直接让智能体生成图片（例如“做一张 16:9 的首页横幅图，内容是……”），它就会自动使用这个技能。你也可以自己运行脚本：

```bash
# 生成新图片
scripts/codex-imagegen -o hero.png "一张 16:9 的宽幅写实照片，内容是……"

# 编辑图片
scripts/codex-imagegen -i hero.png -o hero-v2.png "只把天空改成日落，其他部分保持不变"

# 使用参考图
scripts/codex-imagegen -i style.png -o new.png "图 1 是风格参考。画一张……"
```

| 参数 | 含义 |
| --- | --- |
| `-o OUTPUT` | 输出路径（`.png`、`.jpg` 或 `.jpeg`）。默认：`./codex-image-<时间戳>.png` |
| `-i IMAGE` | 要编辑或作为参考的输入图片。可以重复使用。 |
| `-f` | 如果 `OUTPUT` 已存在，就覆盖它。 |
| `-h` | 显示帮助。 |

成功时，脚本会输出所保存图片的绝对路径。失败时，脚本以非零状态码退出，并输出 Codex 给出的原因。每张图片大约需要 30–90 秒。

## 工作原理

脚本在一个临时目录里以只读沙箱运行 `codex exec`，让 Codex 用 `image_gen` 只生成一张图片。然后它从 Codex 的 JSON 事件流中读取 thread ID，把 `$CODEX_HOME/generated_images/<thread_id>/` 里最新的图片复制到你指定的输出路径。

## 许可证

[MIT](LICENSE)
