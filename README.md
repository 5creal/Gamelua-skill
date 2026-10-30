# 剧情 Lua 撰写

通用 Agent Skill。读一章导演文档，按《剧情表演命令》和前七章 + 第 16 章定稿写法，写出 `Assets/Res/Lua/Scenario` 下的小节脚本。

技能就是一个带 `SKILL.md` 的目录。Cursor、Codex、Claude Code 以及其他认这套格式的工具，都是把这个目录放到自己的 skills 目录里。

## 安装

克隆时文件夹名用 `scenario-lua`。

本机通用目录。Codex 现在从这里读，Claude Code 也认：

```powershell
git clone git@github.com:5creal/Gamelua-skill.git "$env:USERPROFILE\.agents\skills\scenario-lua"
```

Codex 的旧目录仍然有效：

```powershell
git clone git@github.com:5creal/Gamelua-skill.git "$env:USERPROFILE\.codex\skills\scenario-lua"
```

Cursor：

```powershell
git clone git@github.com:5creal/Gamelua-skill.git "$env:USERPROFILE\.cursor\skills\scenario-lua"
```

已安装过时，进该目录 `git pull` 即可。

只给某一个工程用时，克隆到该工程里：

| 工具 | 目录 |
|---|---|
| Codex | `.agents/skills/scenario-lua` |
| Cursor | `.cursor/skills/scenario-lua` |
| Claude Code | `.claude/skills/scenario-lua` |

同一个克隆不要复制三份。需要多个工具一起用时，克隆到一处，再把另外的路径做成指向它的目录链接。

装好后，在对话里给出章节导演文档（或说明桌面 `章节剧情` 下的第几章），并说明要写的工程路径即可。

## 文件

| 文件 | 内容 |
|---|---|
| `SKILL.md` | 从导演文档到 Lua 的步骤与自检 |
| `commands.md` | 表演命令和注意事项 |
| `habits.md` | 衔接习惯（含语气词、镜头边界） |
| `examples.md` | 定稿写法对照 |

## 相对初版的补充

- `PlayEmoteSound` 语气词路径与用法
- 大立绘在场时头像参数留空，情绪走 `SetEmotion`
- `move_in` 仅用于真正入场；开场已在场用 `Show(0)`
- 平面审视不要 bg+立绘同参硬滑；推镜优先 `full_picture_*` 单层
- 噩梦/硬切可用黑遮罩，不只靠 `openEyes`
