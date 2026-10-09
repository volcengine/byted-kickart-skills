# KickArt Agent Skills

Reusable AI Agent skills for creating and editing short-form marketing videos with
[KickArt](https://console.volcengine.com/kickart/welcome). This repository supports Douyin
e-commerce workflows, including product video generation, viral video replication,
and video element replacement.

可复用的 KickArt AI Agent Skills，用于抖音、抖店商品营销短视频的生成、热门同款
制作和视频元素替换。

## Features / 功能

- Generate product marketing videos from Douyin or Dou店 product information.
  根据抖音或抖店商品信息生成营销视频。
- Upload and use custom image and video materials.
  上传并使用自定义图片、视频素材。
- Replicate the structure, pacing, and style of a popular reference video.
  参考热门视频的结构、节奏和风格制作同款内容。
- Replace characters, scenes, products, props, sounds, and other video elements.
  替换角色、场景、商品、道具、声音等视频元素。
- Submit asynchronous tasks and query their progress and results.
  提交异步任务并查询生成进度和结果。
- Apply OAuth, authorization, payment confirmation, and retry safeguards.
  遵循 OAuth、授权、付费确认和任务重试安全规则。

## Included Skills / 包含的 Skills

| Skill | Use case / 适用场景 |
| --- | --- |
| [`byted-kickart-marketing-material-generator`](./byted-kickart-marketing-material-generator) | Generate product marketing videos, upload materials, create one-click videos, or query tasks. / 生成商品营销视频、上传素材、一键成片或查询任务。 |
| [`byted-kickart-viral-replicator`](./byted-kickart-viral-replicator) | Create a popular-video-style version for a target product. / 参考热门视频为目标商品制作同款内容。 |
| [`byted-kickart-video-elements-replace`](./byted-kickart-video-elements-replace) | Replace characters, scenes, products, props, sounds, or other video elements. / 替换角色、场景、商品、道具、声音等视频元素。 |

## Usage / 使用

After installation, describe your task to the Agent in natural language.
安装后，直接用自然语言向 Agent 描述任务即可：

- "Generate a marketing video for this Dou店 product."
  “为这个抖店商品生成营销视频。”
- "Upload these product images and create a short marketing video."
  “上传这些商品图片并生成营销短视频。”
- "Make a popular-video-style version for this product."
  “参考这个热门视频为商品制作同款。”
- "Replace the person and background in this reference video."
  “替换参考视频中的人物和背景。”
- "Check the progress of the video generation task."
  “查询视频生成任务进度。”

The Agent selects the matching Skill and follows its required information collection,
confirmation, submission, and result-query workflow.
Agent 会自动选择匹配的 Skill，并按照对应流程完成信息收集、确认、提交和结果查询。

Before submitting a paid asynchronous task, review the parameters, confirm media
rights, and complete the required billing and compliance confirmation.
提交付费异步任务前，请确认参数、素材使用权以及必要的付费和合规确认。

## Documentation / 文档

Each Skill contains detailed, task-specific documentation:
每个 Skill 都包含对应的详细操作文档：

- `SKILL.md`: trigger conditions, workflow rules, command mapping, and safety constraints.
  触发条件、工作流规则、命令映射和安全约束。
- `references/`: CLI setup, material upload, reference video, task query, and operation
  guides.
  CLI 配置、素材上传、参考视频、任务查询和具体操作指南。
- `assets/`: skill icons and static assets.
  Skill 图标和静态资源。

Start with the `SKILL.md` in the Skill you want to use.
使用某个 Skill 时，建议先阅读对应目录下的 `SKILL.md`：

```text
byted-kickart-marketing-material-generator/SKILL.md
byted-kickart-viral-replicator/SKILL.md
byted-kickart-video-elements-replace/SKILL.md
```

## Safety and Usage Notes / 安全说明

- Use only media and product content that you are authorized to use, modify, and publish.
  仅使用你拥有合法使用、修改和发布授权的素材与商品内容。
- Do not expose or persist OAuth tokens, access keys, secret keys, or signed download URLs.
  不要暴露或持久化 OAuth Token、Access Key、Secret Key 或带签名的下载链接。
- Paid task submission is asynchronous and may not be idempotent. Do not retry blindly
  when the request status is uncertain; query the returned task ID first.
  付费任务为异步且可能不可幂等。状态不确定时不要盲目重试，应先查询任务 ID。
- Follow the error guidance in the selected Skill for quota, credits, authentication,
  validation, and media-format errors.
  配额、创点、认证、参数校验和媒体格式错误请遵循对应 Skill 中的错误处理指南。

## License / 许可证

The included Skills are distributed under the Apache License 2.0. See the `LICENSE`
file in each Skill directory.
包含的 Skill 使用 Apache License 2.0，详见各 Skill 目录中的 `LICENSE` 文件。
