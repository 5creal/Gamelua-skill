# 衔接习惯

前七章怎么接，新章节就怎么接。下面是从 `20001`–`20007` 和已经定稿的第 16 章里抽出来的顺序，不要另起一套。

## 开场和收场

- 第一行是 `SetDefaultUI()`。
- 背景、立绘的显隐包在 `ScreenDarken` 里。先 `Wait` 完变暗时长，再改显隐，再 `ScreenClear`。中途改显隐会闪。
- 开场立绘已在场时用 `Show(0)`，不要无故 `move_in`。
- 不要 `ExchangeBackground` 成当前已经显示的同一张图，那会把背景清掉。
- 小节最后一条可播放命令是 `PlayAmbient("bgm_map_music", true, 2)`。场景中途换 BGM 时第二个参数必须是 `false`。

## 旁白和对话框

- `ShowDialog(id, noFrame, false, true)`。`noFrame = true` 是无框旁白，`false` 是有框旁白。
- 角色对白用 `ShowSpeakDialogue`。`bottomStyle` 不传，保持默认。
- 心想只用 `ShowThinkDialogue`，头像是 `daisy_mind`。
- 选项用 `ShowDialogChoiceChat(false)`，这样上一句对话框还留着。
- 离开对话框去做别的表演时，先 `HideDialog()`，下一行就是表演命令，中间不要 `Wait`。
- 大立绘就是说话人时：`user = ""`，头像表情参数也留空；情绪改大立绘 `SetEmotion`。
- 只有场上没有该角色大立绘时，才给小头像写 `user` + `emoType`（黛西对话头像等）。

## 立绘

- **入场（从外侧走进来）**：`Show(1)`、`Play("move_in")`、`Wait(1.5)`。
- **已在场 / 开场即在 / 插图后恢复**：`Show(0)` 或 `Show(1)`，不要 `move_in`。
- **离场**：`Play("move_back")`、`Hide(1)`。短暂藏起再回来可以只 `Hide` / `Show`。
- 小插图、全屏大图、`ScreenDarken`、`ExchangeBackground` 之前先把立绘藏起来。
- 换表情先找同角色旧脚本里的 `PreparePortrait` / `SetEmotion` / `Show` / `Play` 整段，只改表情字符串。`emoType` 和 `pose` 不是同一个轨道。
- 西奥跑步装 `theo_sports` 常用 `pose1`、`normal`、`think`、`shock`、`pain`。没有 `angry`，冷哼用 `shock`。不要写工程里已下架的表情（如随意编造的 `halo` / `happy`）。
- 黛西说话头像常用 `normal`、`smile`、`happy`、`think`、`worry`、`angry`、`shy`、`shocked`、`astonished`、`awkward`。

## 插图

- 小插图资源名 `photo_`，全屏大图 `full_picture_`，背景 `background_`。全小写英文加下划线，不带章节号，脚本里不写 `.png`。
- 同一时间只有一张小插图。下一张之前 `HideImgCommand`。位置参数目前不生效，用默认 `1, 0, 0`。
- 小插图之间直接换图。不要为了换一张小图去做黑屏。
- 全屏大图的入场遮罩用 `cutscene_bg_mask_line_show`。导演或用户要求直接切图时，`effect` 传 `""`，并把当前的 scale、x、y 原样传进去，避免镜头被重置。
- `scale` 为 1 时，x 和 y 必须是 0。

## 镜头

`SetBackgroundScalePos(scale, posX, posY, duration)` 缩放的是**当前背景根节点**，适合 `full_picture_*` 或背景推拉。命令本身立刻结束，补间还在走。

- 大于 1 是拉近。正的 x 把画面往右推，负的 y 把画面往下推。
- 下一条 `Wait` 不短于这次 duration，再开呼吸或再拉一次。
- 呼吸：`StartBackgroundBreath(1.02, 3, 1.2)`。要停时 `StopBackgroundBreath()`。
- 后面的 `SetBackgroundScalePos` 会先停掉呼吸再补间，所以换镜头前不必再手动停一次。
- `CameraUniformMove` 是地图镜头，对白 UI / 立绘场景不要用它「运镜」。
- `UICamShake` 只是抖动，不是平移推镜。
- `Portrait:SetPos` 与 `SetBackgroundScalePos` **锚点不同**。平面半身立绘上不要用 bg+立绘同参上下滑来仿 3D「审视」——会发飘。需要审视感时：
  1. 优先专用高图 `full_picture_*` 单层 `SetBackgroundScalePos`；或
  2. 分镜切脸/身；或
  3. 旁白点明视线，不要硬做假运镜。

## 闭眼、睁眼、黑屏

时长跟前七章：闭眼 `6.4`，睁眼 `4.4`。特效面板会挡住点击，播完必须关掉。

闭眼进入可点击黑屏：

```lua
SetUIEffectVisible("closeEyes", true, false, 6.4)
Wait(6.4)
SetUIEffectVisible("closeEyes", false, false, 0)
ScreenDarken(0.5)
Wait(0.5)
ExchangeBackground("background_black", "cutscene_bg_default", false)
ScreenClear(0)
```

睁眼和下一张图同时开始，再等特效播完，然后关掉：

```lua
SetUIEffectVisible("openEyes", true, false, 4.4)
ExchangeBackground("full_picture_theo_nightmare", "", false, 1, 0, 0)
Wait(4.4)
SetUIEffectVisible("openEyes", false, false, 0)
```

噩梦等高反差图若睁眼仍穿帮：用 `ScreenDarken` / 黑遮罩硬切进图，再清屏，不要只靠 `openEyes` 盖住。

黑屏上的对白写在 `background_black` 已经换上、`ScreenClear` 之后。`ScreenDarken` 盖在对话框上面，角色在那一层下面说话点不到。

这一段如果要静音，闭眼开始时 `PauseAmbient(1)`，并 `Wait(1)` 后再播下一段 BGM。

## 音频

- `PlayAmbient`、`PlayEffect`、`PlayDialogueSound`、`PlayEmoteSound` 的文件名必须真实存在。紧张气氛用 `bgm_danger_coming.mp3` / `bgm_bad_thing_coming.mp3`。工程里没有 `bgm_tense.mp3`。
- `PauseAmbient(duration)` 之后必须 `Wait(duration)`，才能再 `PlayAmbient`。
- `PlayDialogueSound` 写在对应对白的前一行。没有正式对白语音时，可注释掉 `PlayDialogueSound`，改用 `PlayEmoteSound` 语气词。
- `PlayEmoteSound("dialog_theo_hum")` 一类：只写资源名、不带路径和扩展名；文件在 `Assets/Res/Audios/Effect/Emote/Cn` 或 `En`。写之前两个目录都搜一下，缺文件不要硬写。
- 多多常用 `dialog_buddy_repo`（不要臆造不存在的 buddy 音效名）。
- 不要只播声音、画面什么都不做。
- 循环音效离开该段时 `StopEffect`。

常用 BGM：`bgm_map_music`、`bgm_bad_thing_coming`、`bgm_love_night`、`bgm_funny`、`bgm_happy_enemies`、`bgm_danger_coming`、`bgm_hopeless_pressure`、`bgm_argue`、`bgm_sadness`、`bgm_night`、`bgm_quite_night`、`bgm_lonely_night`。写之前仍以磁盘上的文件为准。

常用语气词前缀：`dialog_theo_*`、`sd_daisy_*`、`dialog_buddy_*`、以及各章角色的 `dialog_<角色>_*`。以 Emote 目录为准。

## 对话框颜色

颜色不写在 Lua 里。`ShowSpeakDialogue` 的 `nickName` 对 `role_color.tsv` 里的角色名，面板按这个 key 换皮肤。`LC_ROLE_buddy` 是绿色。`nickName` 为空时是棕色。
