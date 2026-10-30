# 对照

左边是导演文档里的一句，右边是前七章和第 16 章定稿后的写法。

## 无框旁白，场上没有头像

```lua
ShowDialog("LC_SCENARIO_s2001600701010", true, false, true) --西奥的眼神看着十分危险。
```

有框旁白把第二个参数写成 `false`。

## 说话人就是场上的大立绘（头像留空 + 立绘表情 + 语气词）

```lua
Portrait("theo_sports"):SetEmotion("think", 1)
PlayEmoteSound("dialog_theo_shock")
ShowSpeakDialogue("", "", "LC_ROLE_theo", "LC_SCENARIO_s2001600101040", "dialog", "left", false, true, "") --西奥：这是一只猛兽……
```

## 场上没有该角色大立绘时，才用小头像表情

```lua
PlayEmoteSound("sd_daisy_sad")
ShowSpeakDialogue("daisy", "worry", "LC_ROLE_daisy", "LC_SCENARIO_s2001600101030", "dialog", "right", false, true, "") --黛西：万一等警察赶到，它就不行了呢！
```

## 小插图直接换下一张，不做黑切

```lua
ShowImgCommand("photo_save_wolf", 1, 0, 0)
ShowDialog("LC_SCENARIO_s2001600201040", true, false, true) --我专心手上的事，一束强光忽然从身后照过来。
HideImgCommand("photo_save_wolf")
HideDialog()
ShowImgCommand("photo_theo_coat", 1, 0, 0)
```

## 开场立绘已在场：直接 Show，不要 move_in

```lua
PreparePortrait("theo_sports", "none", 0)
Portrait("theo_sports"):SetEmotion("pose1", 0)
Portrait("theo_sports"):SetEmotion("normal", 1)
Portrait("theo_sports"):Show(0)
```

## 全屏大图用遮罩进来

```lua
Portrait("theo_sports"):Hide(0.3)
Wait(0.3)
ExchangeBackground("full_picture_lean_together", "cutscene_bg_mask_line_show", false, 1.6, -400, -520)
```

## 已经拉近的镜头上直接换大图

保持当前 scale 和坐标，effect 留空。

```lua
HideDialog()
ExchangeBackground("full_picture_almost_kiss", "", false, 1.5, 0, -160)
SetBackgroundScalePos(1.8, 0, -280, 1.2)
```

## 推近之后再呼吸

```lua
SetBackgroundScalePos(1.8, 0, -280, 1.2)
ShowDialog("LC_SCENARIO_s2001600701030", true, false, false)
Wait(1.2)
StartBackgroundBreath(1.02, 3, 1.2)
WaitDialogAreaClick()
```

呼吸结束在下一句开始之前：`StopBackgroundBreath()`。

## 立绘入场再说话（真正走进来时）

```lua
HideDialog()
Portrait("theo_sports"):Show(1)
Portrait("theo_sports"):Play("move_in")
Wait(1.5)
PlayEmoteSound("dialog_theo_hum")
ShowSpeakDialogue("", "", "LC_ROLE_theo", "LC_SCENARIO_s2001600301080", "dialog", "left", false, true, "")
```

离场是 `Play("move_back")` 然后 `Hide(1)`。

## 噩梦 / 硬切：黑遮罩进图（穿帮时别只靠 openEyes）

```lua
HideDialog()
ScreenDarken(0.3)
Wait(0.3)
ExchangeBackground("full_picture_theo_nightmare", "", false, 1, 0, 0)
ScreenClear(0.3)
Wait(0.3)
```
