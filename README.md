# 剧情 Lua 撰写

给 Cursor 用的 skill。读一章导演文档，按《剧情表演命令》和前七章的写法，写出 `Assets/Res/Lua/Scenario` 下的小节脚本。

## 安装

克隆到本机的 Cursor skills 目录，文件夹名用 `scenario-lua`：

```powershell
git clone https://github.com/5creal/Gamelua-skill.git "$env:USERPROFILE\.cursor\skills\scenario-lua"
```

项目级安装则克隆到该工程的 `.cursor/skills/scenario-lua`。

装好后，在对话里给出章节导演文档（或说明桌面 `章节剧情` 下的第几章），并说明要写的工程路径即可。

## 文件

| 文件 | 内容 |
|---|---|
| `SKILL.md` | 从导演文档到 Lua 的步骤 |
| `commands.md` | 表演命令和注意事项 |
| `habits.md` | 前七章的衔接习惯 |
| `examples.md` | 定稿写法对照 |
