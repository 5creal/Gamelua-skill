---
name: scenario-lua
description: >-
  Writes Unity scenario Lua from a chapter director document, using the
  剧情表演命令 catalog and writing habits from chapters 1–7 plus chapter 16
  polish (emote SFX, portrait/camera rules). Use when the user mentions
  剧情Lua, 导演意图, 章节文档, Scenario snippets, PlayEmoteSound, or asks to
  write or revise Assets/Res/Lua/Scenario scripts.
---

# 剧情 Lua

先读导演文档，再按前七章与第 16 章定稿脚本的衔接来写。命令以 [commands.md](commands.md) 为准，衔接以 [habits.md](habits.md) 为准。对照例子见 [examples.md](examples.md)。

## 流程

1. 找到导演文档。默认在桌面 `章节剧情/` 下的 `N章.docx`。用户给了别的路径就用那个路径。docx 用解压 `word/document.xml` 取纯文本，不要猜段落。
2. 按「第N节」拆段。一节一个 `Snippet()`。文件夹是 `Assets/Res/Lua/Scenario/200{章号两位}/`。主线文件名是文件夹名再加四位节号，从 `0010` 起，每节加 10。第 16 章第 7 节是 `20016/200160070.lua`，第 2 章第 10 节是 `20002/200020100.lua`。分支小节沿用文档或已有脚本里的编号，例如 `200010011.lua`，不要自己编。
3. 对白 id 用已有表（文档旁的 LC 列表、`_extracted`、或同章已写脚本）。没有 id 就停下来问，不要编 `LC_SCENARIO_*`。
4. 每个资源名先在工程里搜到文件再写进脚本：
   - BGM：`Assets/Res/Audios/Music`
   - 音效：`Assets/Res/Audios/Effect`（非 Emote）
   - 语气词：`Assets/Res/Audios/Effect/Emote/Cn` 与 `En`（`PlayEmoteSound` 只写不含扩展名的文件名）
   - 背景：`Assets/Res/Textures/UI/Talk/SpriteAlone/Background`
   - 小插图：`Assets/Res/Textures/UI/Scenario/SpriteAlone`
   - 立绘 Spine：`Assets/Res/Spine/UI/Character`
5. 同类表演先打开 `20001`–`20007` 或同章已定稿节里最接近的一节，照它的命令顺序写。特殊订单切大图看 `20002/200020100.lua`；语气词与表情联调看 `20016`。
6. 写完按下面的自检过一遍。不要主动提交游戏仓库。

## 导演文档怎么落成命令

文档自上而下就是时间轴。

| 文档 | Lua |
|---|---|
| `EXT. 地点` 加 `[音乐：…]` | 开场背景和 `PlayAmbient`。音乐名先对上真实文件 |
| 单独一行角色名，下一行是对白 | `ShowSpeakDialogue`。括号里的情绪：大立绘用 `Portrait:SetEmotion`；小头像才写 `emoType`。语气词用 `PlayEmoteSound`，写在对白前一行 |
| `黛西` + `[心想]` | `ShowThinkDialogue("daisy_mind", emo, id)`。只有黛西可以心想 |
| 没有角色名的句子 | `ShowDialog`。默认无框；文档或用户写明有框时第二个参数用 `false` |
| `[插图：…]` / `[小插图：…]` | `ShowImgCommand`。先藏立绘。下一张插图之前先 `HideImgCommand` |
| `[全屏表现：…]` | `ExchangeBackground` 到 `full_picture_*`。先藏立绘。需要推镜时只动这一层 `SetBackgroundScalePos` |
| `[音效：…]` | 环境/动作音用 `PlayEffect`；角色语气词用 `PlayEmoteSound`。循环音效收尾要 `StopEffect` |
| `玩法节点：玩家选择` | `ShowDialogChoiceChat(false):AppendChoice(...)` |

角色对白的小头像：场上正中间的大立绘就是说话人时，`user` 留空 `""`，且不要给空头像塞 `emoType`。没有大立绘时才带头像。黛西的说话头像和心想头像都放右边。

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
    Portrait("theo_sports"):Show(0)
    Wait(0.3)
    ScreenClear(0.5)
    Wait(0.5)

    -- 对白逐条往下排；需要语气时在对白前加 PlayEmoteSound
    -- 每条末尾用注释写下原文

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

- 每个资源名都能在上面的目录里找到文件。不要写 `bgm_tense`。语气词在 `Emote/Cn` 或 `En` 里确实存在。
- 旁白有框/无框和用户最后一次要求一致。
- 小插图和大图期间没有立绘。`HideDialog()` 紧挨着后面的表演，中间不 `Wait`。
- 大立绘在场时 `ShowSpeakDialogue` 的 `user` 为 `""`，不要给空头像写表情参数。
- `SetBackgroundScalePos` 的时长用同长度的 `Wait` 接住，再开呼吸或下一次缩放。
- 不要用 `CameraUniformMove` / `UICamShake` 代替背景推镜；平面「上下审视」不要对 bg+立绘同参硬滑，优先单层 `full_picture_*` 推镜或分镜/旁白。
- `closeEyes` / `openEyes` 播完并 `SetUIEffectVisible(..., false)` 之后，才出现下一条可点击对白。噩梦/硬切可用黑遮罩，不要只靠睁眼盖穿帮。
- 循环音效有 `StopEffect`。小节结束用 `PlayAmbient("bgm_map_music", true, 2)`，只有这一处第二个参数是 `true`。
- 黑屏上如果角色还要说话，用 `ExchangeBackground("background_black", ...)`，不要把 `ScreenDarken` 留在对话框上面。
