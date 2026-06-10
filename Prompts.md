# 小鸡仔 Codex 桌宠复刻 Prompts

本文档用于配合根目录的 `PLAN.md` 和素材图 `小鸡仔.jpg` 使用。目标是让其他人只拿到这三样内容，也能尽可能复刻出同一个 Codex 桌宠：一个严格基于 `小鸡仔.jpg` 的篮球公仔角色。

## 最高优先级

最终角色必须严格基于 `小鸡仔.jpg`，而不是重新设计一个相似主题的新角色。早期曾讨论过 `3d-toy` 风格，但最终修正为：风格可以服务于生成流程，不能覆盖参考图本身。若“风格化效果”和“参考图还原度”冲突，必须选择参考图还原度。

必须保留的身份核心：

- 眼镜
- 黑发
- 红脸颊
- 背带裤
- 篮球

角色应看起来像从 `小鸡仔.jpg` 中延展出来的同一个桌宠。不能变成普通小鸡、通用卡通篮球角色、毛绒玩具、陌生 3D 公仔、不同发型、不同服装或不同脸型。

## 素材输入

主参考图：

```text
小鸡仔.jpg
```

动作参考 GIF：

```text
铁山靠.gif
```

动作参考 GIF 可选放在同一文件夹或由复刻者自行指定路径。GIF 只用于理解 `running-right` 的身体节奏和摆动方式，不能替代 `小鸡仔.jpg` 的角色外观。

## 角色修正总结

复刻时应遵循以下修正后的角色策略：

1. 不要先创造一个“基础新形象”，再把眼镜、头发、篮球等元素贴上去。
2. 应直接以 `小鸡仔.jpg` 为基础，保留原图中的脸型比例、眼镜位置、头发轮廓、脸颊颜色、衣服结构和篮球关系。
3. 如果需要绿幕或 chroma-key 背景，可以通过生图或后处理替换背景，但不能因此改变角色本体。
4. 如果某一版基础形象已经被确认满意，后续只修动作行，不重做基础图。
5. 所有动作都应像同一个角色在运动，而不是每行重新生成一个近似角色。

## 通用生成原则

每次生成 base 或动画行时，都应把下面这段作为核心约束：

```text
Strictly preserve the character from the provided reference image 小鸡仔.jpg. Keep the same face shape, black-rim glasses, black fluffy hair, red cheeks, overalls, shoes, and basketball identity. Do not redesign the mascot. Do not make a generic chick, generic toy, plush character, or unrelated 3D mascot. The character must look like the same 小鸡仔 from the reference image, only changing pose and motion.
```

推荐背景约束：

```text
Use a clean flat chroma-key background, preferably pure cyan #00FFFF or another extraction-safe solid color. No scenery, no shadows, no text, no logo, no decorative effects, no floor, no white background, no checkerboard pattern.
```

推荐桌宠约束：

```text
The pet must be compact, full-body, centered, readable inside a 192x208 sprite cell, with a clear silhouette and no cropped body parts. Keep each frame aligned on a stable baseline unless the action intentionally jumps.
```

## Base Prompt

用途：生成或确认基础角色图。若已有满意基础图，不要重做。

```text
Create one clean full-body Codex desktop pet reference sprite based strictly on the provided image 小鸡仔.jpg.

The character must be the same 小鸡仔 from the reference: black-rim glasses, black fluffy hair, red cheeks, overalls, shoes, and basketball as identity core. Preserve the original face proportions, hair silhouette, glasses placement, cheek color, clothing structure, and overall personality from the reference image.

Do not redesign the character into a generic chick, generic 3D toy, plush mascot, sticker mascot, or unrelated basketball character. Reference fidelity is more important than any style preset.

Place the single full-body character centered on a perfectly flat chroma-key background. Keep the full body visible, compact, readable at 192x208, and easy to animate. No scenery, text, borders, shadows, glows, detached effects, extra props, floor, or background residue.
```

## 动作状态总表

Codex 桌宠图集状态顺序必须与 `PLAN.md` 保持一致：

| Row | State | Frames | 动作定义 |
| --- | --- | ---: | --- |
| 0 | `idle` | 6 | 单手转球 |
| 1 | `running-right` | 8 | 镜像后的铁山靠式背面摆动 |
| 2 | `running-left` | 8 | 与 `running-right` 对称的镜像动作 |
| 3 | `waving` | 4 | 胯下运球 |
| 4 | `jumping` | 5 | 投篮感起跳 |
| 5 | `failed` | 8 | 篮球从手中掉落，角色低头注视 |
| 6 | `waiting` | 6 | 抱球眨眼 |
| 7 | `running` | 6 | 正面单手运球，有跑动感 |
| 8 | `review` | 6 | 盘腿坐着，认真观察手中的球 |

## Row Prompts

以下提示词用于逐行动画生成。每一行都必须同时参考 `小鸡仔.jpg` 或已确认满意的 canonical base。

### idle

```text
Create a 6-frame sprite strip for idle.

The character is the same 小鸡仔 from the reference image. Front-facing or slightly turned, relaxed and cute, standing in place while spinning a basketball with one hand. The motion should be calm and loopable: subtle body bounce, tiny hair tremble, stable glasses, red cheeks visible, overalls unchanged.

Preserve identity exactly: black-rim glasses, black fluffy hair, red cheeks, overalls, shoes, basketball. Do not redesign the character. Transparent or chroma-key extraction-safe background. No shadows, text, effects, scenery, or extra props.
```

### running-right

```text
Create an 8-frame sprite strip for running-right.

The character is the same 小鸡仔 from the reference image, shown from the back with the back facing the viewer. The body is in a half-squat stance. The right shoulder is lowered. The body moves in a soft curved loop from upper-left toward lower-right, inspired by the provided 铁山靠.gif rhythm, but the character design must remain 小鸡仔.

This action should be the mirrored version requested by the user: preserve the iron-mountain-lean feeling while matching the intended direction. Hair should naturally tremble with the movement. The right-side overall strap naturally slips down. Keep the motion smooth, playful, and loopable.

Preserve identity exactly: black fluffy hair, glasses implied when visible by angle, red cheek hints if visible, overalls, shoes, basketball identity if included. Do not create a realistic human dancer. Do not replace the character with the GIF person. Chroma-key extraction-safe background, no scenery, no shadows, no text.
```

### running-left

```text
Create an 8-frame sprite strip for running-left.

Use the same 小鸡仔 identity and the same action logic as running-right, but mirrored horizontally so the motion reads as the opposite direction. The back faces the viewer, body half-squat, shoulder lowered, soft diagonal rocking loop, hair naturally trembling, and one overall strap naturally slipping.

If a validated running-right strip already exists, prefer deterministic horizontal mirroring for running-left so the identity and frame timing remain consistent.
```

### waving

```text
Create a 4-frame sprite strip for waving.

The character is the same 小鸡仔 from the reference image, performing a between-the-legs basketball dribble. The motion should clearly read as a playful crotch dribble rather than a hand wave. Keep the character compact and readable in a 192x208 cell. Hair keeps a natural small tremble from the dribbling motion.

Preserve identity exactly: black-rim glasses, black fluffy hair, red cheeks, overalls, shoes, basketball. Do not redesign the character. Chroma-key extraction-safe background, no scenery, no shadows, no text.
```

### jumping

```text
Create a 5-frame sprite strip for jumping.

The character is the same 小鸡仔 from the reference image, doing a jump with a strong basketball shooting feeling. The pose should read as preparing to shoot or rising into a shot: knees bend, body lifts, arm and basketball move upward, then the motion loops or settles cleanly.

Preserve the glasses, black hair, red cheeks, overalls, shoes, and basketball. Keep the character cute and compact, not athletic-realistic. Chroma-key extraction-safe background, no floor, no shadows, no text, no effects.
```

### failed

```text
Create an 8-frame sprite strip for failed.

The character is the same 小鸡仔 from the reference image, standing front-facing. The basketball slips or drops from the hand. As the ball falls, the character follows it with their gaze and lowers their head. The emotional tone is small, disappointed, and cute, not dramatic.

The animation should clearly show: ball in hand, ball beginning to fall, character eyes/head tracking downward, ball lower near the body or ground area, final frame loopable or gently held.

Preserve identity exactly: black-rim glasses, black fluffy hair, red cheeks, overalls, shoes, basketball. Do not change the costume or face. Chroma-key extraction-safe background, no scenery, no shadows, no text.
```

### waiting

```text
Create a 6-frame sprite strip for waiting.

The character is the same 小鸡仔 from the reference image, holding the basketball close to the body and blinking. The pose is mostly still, soft, and patient. The blink should be visible but subtle. Hair may have a tiny natural bounce.

Preserve identity exactly: black-rim glasses, black fluffy hair, red cheeks, overalls, shoes, basketball. Keep the body compact and full visible. Chroma-key extraction-safe background, no scenery, no shadows, no text.
```

### running

```text
Create a 6-frame sprite strip for running.

The character is the same 小鸡仔 from the reference image, front-facing, doing a one-hand basketball dribble with a clear running feeling. The pose should show forward energy through body lean, alternating foot movement, and rhythmic ball bounce, while remaining centered enough for a desktop pet sprite.

Preserve identity exactly: black-rim glasses, black fluffy hair, red cheeks, overalls, shoes, basketball. Do not make the character side-facing unless slightly angled for motion readability. Chroma-key extraction-safe background, no scenery, no shadows, no text.
```

### review

```text
Create a 6-frame sprite strip for review.

The character is the same 小鸡仔 from the reference image, sitting cross-legged and seriously observing the basketball in their hands. The action is quiet and focused: small head tilt, tiny blink, subtle hand or ball adjustment. The character should look curious and attentive.

Preserve identity exactly: black-rim glasses, black fluffy hair, red cheeks, overalls, shoes, basketball. Keep the seated pose compact and fully visible. Chroma-key extraction-safe background, no scenery, no shadows, no text.
```

## Negative Prompt

以下内容在所有生成中都应避免：

```text
generic chick, generic mascot, unrelated toy, plush doll, realistic human, realistic dancer, different hair, missing glasses, missing red cheeks, missing overalls, missing basketball, changed outfit, changed face, different character, text, logo, watermark, floor, scenery, white background, checkerboard background, shadows, glow effects, particle effects, extra props, cropped body, cut off feet, inconsistent scale, inconsistent baseline, duplicated character, multiple characters, motion smear that hides identity
```

## 复刻验收标准

验收时不要只看技术校验通过，还要看角色是否真的像 `小鸡仔.jpg`：

- 9 行状态都必须是同一个角色。
- 眼镜、黑发、红脸颊、背带裤、篮球不能丢。
- 动作可以夸张，但不能因为动作导致角色身份漂移。
- `running-right` 和 `running-left` 的方向关系要清楚。
- `waving` 实际应表现为胯下运球，不是挥手。
- `waiting` 与 `idle` 要能区分：`waiting` 是抱球眨眼，`idle` 是单手转球。
- `failed` 要能看出篮球掉落和低头注视。
- `review` 要能看出盘腿坐着认真看球。
- 若某一行动作失败，只重做该行；不要轻易重做已经满意的基础角色。

## 推荐工作流备注

1. 先用 `小鸡仔.jpg` 生成或确认 canonical base。
2. canonical base 被确认满意后，后续所有行都基于它生成。
3. 每生成一行就做视觉检查，优先修当前行。
4. `running-left` 优先从 `running-right` 镜像得到，以保持角色一致性。
5. 最终按照 `PLAN.md` 的尺寸、帧数和状态顺序合成 Codex pet 图集。
