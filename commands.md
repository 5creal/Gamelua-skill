# 剧情表演命令

来源是《剧情表演命令》。写脚本时用这里的参数和注意事项。前七章的衔接见 [habits.md](habits.md)。

## 界面

| 命令 | 用法 | 注意 |
|---|---|---|
| `SetDefaultUI()` | 小节第一行 | 关掉跳过、自动、隐藏对话 |
| `ShowSpeakDialogue(user, emoType, nickName, content, "dialog", side, false, true, pendant)` | 角色对白 | 女主头像放右边。场上大立绘就是说话人时 `user` 用 `""`。`bottomStyle` 不传。`pendant` 只在这一句有挂件时传 |
| `ShowDialog(content, noFrame, false, true)` | 旁白 | `noFrame` true 无框，false 有框。黑屏上说话不要用它盖在 `ScreenDarken` 下 |
| `ShowThinkDialogue(user, emoType, content)` | 心想 | 只能是女主。头像用 `daisy_mind` |
| `ShowLineBox(content, duration)` | 黑屏上的一行字 | 字在 `Screen` 层。后面必须 `HideLineBox()` |
| `HideDialog()` | 收起对话框 | 做非对话表演前调用。下一行就是表演，中间不要 `Wait` |
| `ShowDialogChoiceChat(false)` | 选项 | 传 `false` 保留上一句。用 `AppendChoice` 加选项 |

## 背景、镜头、插图

| 命令 | 用法 | 注意 |
|---|---|---|
| `SetBgVisible(name, visible, duration)` | 显隐背景 | 包在黑屏里做 |
| `ExchangeBackground(name, effect, isPrefab, scale, posX, posY)` | 换背景或大图 | 空 effect 会变成 `cutscene_bg_default`。遮罩切图用 `cutscene_bg_mask_line_show`。不要换成同一张图 |
| `SetBackgroundScalePos(scale, posX, posY, duration)` | 推拉背景 | 命令立刻结束。scale 为 1 时坐标必须是 0, 0 |
| `StartBackgroundBreath(scale, duration, interval)` | 呼吸 | 等上一次推拉的 `Wait` 结束再开 |
| `StopBackgroundBreath()` | 停止呼吸 | |
| `ShowImgCommand(name, 1, 0, 0, aniName)` | 小插图 | 同时只留一张。位置参数目前不生效。动画默认 `open`，另有 `shake` |
| `HideImgCommand(name)` | 收起小插图 | 下一张之前先收 |

## 转场和特效

| 命令 | 用法 | 注意 |
|---|---|---|
| `ScreenDarken(duration)` / `ScreenClear(duration)` | 黑屏遮罩 | 遮罩盖住对话框。改场景显隐前先等它变暗完 |
| `SetUIEffectVisible(name, visible, isReverse, duration)` | 闭眼、睁眼、刮风等 | 面板打开后会挡住点击，用完要设回 `false` |
| `UICamShake(range, duration)` | 镜头抖动 | 和这句对白放在一起 |

## 立绘

| 命令 | 用法 | 注意 |
|---|---|---|
| `PreparePortrait(name, "none", 0)` | 载入 Spine | `Show` 之前先准备 |
| `Portrait(name):SetEmotion(emo, layer)` | 设表情 | layer 0 是姿势，1 是表情。整段照抄旧脚本再改字符串 |
| `Portrait(name):Show(duration)` / `:Hide(duration)` | 显隐 | |
| `Portrait(name):Play(ani)` | 入场离场 | `move_in`、`move_back` |

`ChessActor` 一组接口已不维护，新脚本不要用。

## 音频

| 命令 | 用法 | 注意 |
|---|---|---|
| `PlayAmbient(name, needsave, duration)` | BGM | 只有结尾恢复地图音乐时 `needsave` 为 `true` |
| `PauseAmbient(duration)` | 停下 BGM | 必须接着 `Wait(duration)` 才能再播 |
| `PlayEffect(name)` / `StopEffect(name)` | 音效 | 循环音效必须停。音效目录里带空格的文件名按原样写 |
| `PlayDialogueSound(name)` | 角色语音 | 写在对白命令的前一行 |

## 地图

这些只在剧情要操作棋盘时用，对白段不要顺手加。

- 镜头对准下一栋建筑：`CameraUniformMove` 配合 `CamZoom(800)` 左右。
- 建造、升级：`UpgradeBuildingGroup`，升级 id 一般是 1。
- 显隐一组建筑：`SetBuildingGroupVisible`。
- 清雾、锁定，用对应的 `LockXxx` / `UnlockXxx`，和旧地图剧情成对出现。
