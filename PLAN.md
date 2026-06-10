# Codex 桌面宠物开发计划：参考图 → Image2 动画图集 → 打包安装

## Summary

目标是基于你提供的参考图，使用 `$imagegen` 的 image-to-image 能力生成 Codex 兼容桌面宠物，并通过 `hatch-pet` 的确定性脚本完成图集切帧、校验、预览、修复和最终打包。

最终交付物：

- `~/.codex/pets/<pet-id>/pet.json`
- `~/.codex/pets/<pet-id>/spritesheet.webp`
- QA 产物：contact sheet、逐行动画 GIF、validation JSON、review JSON

默认约束：

- 图集尺寸：`1536x1872`
- 网格：8 列 × 9 行
- 单格：`192x208`
- 背景：透明
- 未使用格子：完全透明
- 状态行顺序固定：`idle`, `running-right`, `running-left`, `waving`, `jumping`, `failed`, `waiting`, `running`, `review`

## Key Workflow

1. **素材准备**
   - 收集你的参考图，确认宠物名称、描述、风格、避免事项。
   - 默认使用 `style-preset=auto`，除非你指定 `pixel`, `plush`, `clay`, `sticker`, `flat-vector`, `3d-toy`, `painterly`, `brand-inspired`。
   - 参考图要求：主体完整、无复杂背景、不要包含必须保留的文字或 logo。

2. **初始化 Hatch Pet Run**
   - 使用 `prepare_pet_run.py` 创建运行目录、prompt 文件、布局参考图和 `imagegen-jobs.json`。
   - 输入参考图会作为 canonical visual source，用于后续所有动画行。
   - 运行目录建议放在：
     `work/hatch-pet-runs/<pet-id>/`

3. **生成基础形象**
   - 先生成 `base` 图，要求是单个完整宠物、居中、全身、纯色 chroma-key 背景。
   - 通过视觉检查后，将其保存为：
     `references/canonical-base.png`
   - 后续每一行动画都必须参考这个 canonical base，避免身份漂移。

4. **生成 9 行动画素材**
   - 每行用 `$imagegen` image2 参考 canonical base 和对应 layout guide 生成 row strip。
   - 每行必须生成对应帧数：
     - `idle`: 6 帧
     - `running-right`: 8 帧
     - `running-left`: 8 帧
     - `waving`: 4 帧
     - `jumping`: 5 帧
     - `failed`: 8 帧
     - `waiting`: 6 帧
     - `running`: 6 帧
     - `review`: 6 帧
   - `running-left` 优先由 `running-right` 镜像生成，但只有在镜像不会改变身份、道具方向或语义时才使用；否则单独生成。

5. **确定性图像处理**
   - 使用 `extract_strip_frames.py` 从每行 row strip 提取帧。
   - 使用 `inspect_frames.py` 检查切帧、组件、透明区域和异常。
   - 使用 `compose_atlas.py` 合成最终 `spritesheet.png` 和 `spritesheet.webp`。
   - 使用 `validate_atlas.py` 验证最终图集尺寸、透明像素、空白格、行列结构。
   - 使用 `make_contact_sheet.py` 生成总览图。
   - 使用 `render_animation_previews.py` 生成每行动画 GIF。

6. **视觉 QA 与修复**
   - 检查 contact sheet 和 GIF：
     - 是否同一个宠物
     - 是否风格统一
     - 是否有裁切、背景残留、阴影、漂浮特效、白底
     - 动画是否符合行语义
     - idle 是否足够安静
     - directional running 是否方向正确
   - 若失败，只修最小范围：
     - 单帧问题优先修单帧或该行
     - 行语义错误则重生成该行
     - 基础形象错误才重做 base 和全套动画

7. **打包安装**
   - 将最终文件复制到：
     `~/.codex/pets/<pet-id>/`
   - 生成 `pet.json`：
     ```json
     {
       "id": "<pet-id>",
       "displayName": "<Pet Name>",
       "description": "<one short sentence>",
       "spritesheetPath": "spritesheet.webp"
     }
     ```
   - 保留 QA 摘要：
     `qa/run-summary.json`

## QA And Acceptance Tests

必须全部通过后才接受：

- `spritesheet.webp` 尺寸为 `1536x1872`。
- 8 列 × 9 行结构正确，每格 `192x208`。
- 所有未使用格子完全透明。
- `qa/review.json` 无 errors。
- `final/validation.json` 显示 atlas 合法。
- contact sheet 中 9 行均为同一宠物身份。
- GIF 预览无明显尺寸跳变、方向错误、动作停滞或基线漂移。
- 不出现文字、logo、网格线、布局参考线、白底、阴影、漂浮装饰或背景残留。
- `idle`, `waiting`, `running`, `review`, `failed` 语义彼此可区分。
- 最终 `pet.json` 与 `spritesheet.webp` 位于同一 pet 目录。

## Implementation Commands

初始化：

```bash
SKILL_DIR="$HOME/.codex/skills/hatch-pet"
RUN_DIR="./work/hatch-pet-runs/<pet-id>"

python "$SKILL_DIR/scripts/prepare_pet_run.py" \
  --pet-name "<Pet Name>" \
  --pet-id "<pet-id>" \
  --description "<one short sentence>" \
  --reference "/absolute/path/to/reference.png" \
  --output-dir "$RUN_DIR" \
  --pet-notes "<stable visual description>" \
  --style-preset auto \
  --chroma-key auto \
  --force
```

生成完成后处理：

```bash
python "$SKILL_DIR/scripts/extract_strip_frames.py" \
  --decoded-dir "$RUN_DIR/decoded" \
  --output-dir "$RUN_DIR/frames" \
  --states all \
  --method auto

python "$SKILL_DIR/scripts/inspect_frames.py" \
  --frames-root "$RUN_DIR/frames" \
  --json-out "$RUN_DIR/qa/review.json" \
  --require-components

python "$SKILL_DIR/scripts/compose_atlas.py" \
  --frames-root "$RUN_DIR/frames" \
  --output "$RUN_DIR/final/spritesheet.png" \
  --webp-output "$RUN_DIR/final/spritesheet.webp"

python "$SKILL_DIR/scripts/validate_atlas.py" \
  "$RUN_DIR/final/spritesheet.webp" \
  --json-out "$RUN_DIR/final/validation.json"

python "$SKILL_DIR/scripts/make_contact_sheet.py" \
  "$RUN_DIR/final/spritesheet.webp" \
  --output "$RUN_DIR/qa/contact-sheet.png"

python "$SKILL_DIR/scripts/render_animation_previews.py" \
  --frames-root "$RUN_DIR/frames" \
  --output-dir "$RUN_DIR/qa/previews"
```

打包：

```bash
PET_DIR="$HOME/.codex/pets/<pet-id>"
mkdir -p "$PET_DIR"
cp "$RUN_DIR/final/spritesheet.webp" "$PET_DIR/spritesheet.webp"

jq -n \
  --arg id "<pet-id>" \
  --arg displayName "<Pet Name>" \
  --arg description "<one short sentence>" \
  '{
    id: $id,
    displayName: $displayName,
    description: $description,
    spritesheetPath: "spritesheet.webp"
  }' > "$PET_DIR/pet.json"
```

## Assumptions

- 你会提供至少 1 张参考图；如果有多张，优先使用最能代表最终宠物身份的一张作为主参考。
- 默认使用 `$imagegen` 的 image2 / reference-image 流程生成 base 和每行动画。
- 默认不复制 logo、文字、UI 截图或品牌标语，只提取视觉气质和角色特征。
- 默认使用 `auto` 风格和 `auto` chroma-key；除非你明确指定风格或背景键色。
- 默认最多并行两个图像生成任务，降低身份漂移和质量失控风险。
- 最终需要重启或刷新 Codex，才能在桌面宠物选择中稳定看到新 pet。
