---
name: scenario-lua
description: >-
  Writes Unity scenario Lua from a chapter director document, using the
  剧情表演命令 catalog and the writing habits of chapters 1–7. Use when the
  user mentions 剧情Lua, 导演意图, 章节文档, Scenario snippets, or asks to
  write or revise Assets/Res/Lua/Scenario scripts.
---

# 剧情 Lua

先读导演文档，再按前七章已有脚本的衔接来写。命令以 [commands.md](commands.md) 为准，衔接以 [habits.md](habits.md) 为准。对照例子见 [examples.md](examples.md)。

## 流程

1. 找到导演文档。默认在桌面 `章节剧情/` 下的 `N章.docx`。用户给了别的路径就用那个路径。docx 用解压 `word/document.xml` 取纯文本，不要猜段落。
2. 按「第N节」拆段。一节一个 `Snippet()`。文件夹是 `Assets/Res/Lua/Scenario/200{章号两位}/`。主线文件名是文件夹名再加四位节号，从 `0010` 起，每节加 10。第 16 章第 7 节是 `20016/200160070.lua`，第 2 章第 10 节是 `20002/200020100.lua`。分支小节沿用文档或已有脚本里的编号，例如 `200010011.lua`，不要自己编。
3. 对白 id 用已有表（文档旁的 LC 列表、`_extracted`、或同章已写脚本）。没有 id 就停下来问，不要编 `LC_SCENARIO_*`。
4. 每个资源名先在工程里搜到文件再写进脚本。BGM 在 `Assets/Res/Audios/Music`，音效在 `Assets/Res/Audios/Effect`，背景在 `Assets/Res/Textures/UI/Talk/SpriteAlone/Background`，小插图在 `Assets/Res/Textures/UI/Scenario/SpriteAlone`，立绘 Spine 在 `Assets/Res/Spine/UI/Character`。
5. 同类表演先打开 `20001`–`20007` 里最接近的一节，照它的命令顺序写。特殊订单切大图看 `20002/200020100.lua`。
6. 写完按下面的自检过一遍。不要主动提交游戏仓库。

## 导演文档怎么落成命令

文档自上而下就是时间轴。

| 文档 | Lua |
|---|---|
| `EXT. 地点` 加 `[音乐：…]` | 开场背景和 `PlayAmbient`。音乐名先对上真实文件 |
| 单独一行角色名，下一行是对白 | `ShowSpeakDialogue`。括号里的情绪写成 `emoType`，先在同角色旧脚本里确认这个表情名存在 |
| `黛西` + `[心想]` | `ShowThinkDialogue("daisy_mind", emo, id)`。只有黛西可以心想 |
| 没有角色名的句子 | `ShowDialog`。默认无框；文档或用户写明有框时第二个参数用 `false` |
| `[插图：…]` / `[小插图：…]` | `ShowImgCommand`。先藏立绘。下一张插图之前先 `HideImgCommand` |
| `[全屏表现：…]` | `ExchangeBackground` 到 `full_picture_*`。先藏立绘 |
| `[音效：…]` | `PlayEffect`，和这句一起出现。循环音效收尾要 `StopEffect` |
| `玩法节点：玩家选择` | `ShowDialogChoiceChat(false):AppendChoice(...)` |

角色对白的小头像：场上正中间的大立绘就是说话人时，`user` 留空 `""`。没有大立绘时才带头像。黛西的说话头像和心想头像都放右边。

## 文件

Lua 保存为 UTF-8、无 BOM、CRLF。

```lua
require("ScenarioHelper")

function Snippet()
    SetDefaultUI()
    PlayAmbient("bgm_love_night.mp3", false, 1)

    ScreenDarken(0.5)
    Wait(0.5)
    SetBgVisible("background_woods", true, 0)
    PreparePortrait("theo_sports", "none", 0)
    Portrait("theo_sports"):SetEmotion("pose1", 0)
    Portrait("theo_sports"):SetEmotion("normal", 1)
    Wait(0.3)
    ScreenClear(0.5)
    Wait(0.5)

    -- 对白逐条往下排，每条末尾用注释写下原文

    PlayAmbient("bgm_map_music", true, 2)
    HideDialog()
    Portrait("theo_sports"):Hide(0.3)
    Wait(0.3)
    ScreenDarken(0.5)
    Wait(0.5)
    SetBgVisible("background_woods", false, 0)
    Wait(0.3)
    ScreenClear(0.5)
    Wait(0.5)
end
```

结尾 `SetBgVisible` 关掉的必须是本段最后还开着的那张背景。中途换成了大图，就关那张大图。

## 自检

- 每个资源名都能在上面的目录里找到文件。不要写 `bgm_tense`。
- 旁白有框/无框和用户最后一次要求一致。
- 小插图和大图期间没有立绘。`HideDialog()` 紧挨着后面的表演，中间不 `Wait`。
- `SetBackgroundScalePos` 的时长用同长度的 `Wait` 接住，再开呼吸或下一次缩放。
- `closeEyes` / `openEyes` 播完并 `SetUIEffectVisible(..., false)` 之后，才出现下一条可点击对白。
- 循环音效有 `StopEffect`。小节结束用 `PlayAmbient("bgm_map_music", true, 2)`，只有这一处第二个参数是 `true`。
- 黑屏上如果角色还要说话，用 `ExchangeBackground("background_black", ...)`，不要把 `ScreenDarken` 留在对话框上面。
