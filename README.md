# KickArt Agent Skills

[简体中文](README.md) | [English](README.en.md)

本项目提供一组可复用的 KickArt AI Agent Skills，用于抖音、抖店商品营销短视频
的生成、热门同款制作和视频元素替换。

## 功能

- 根据抖音或抖店商品信息生成营销视频。
- 上传并使用自定义图片、视频素材。
- 参考热门视频的结构、节奏和风格制作同款内容。
- 替换角色、场景、商品、道具、声音等视频元素。
- 提交异步任务并查询生成进度和结果。
- 遵循 OAuth、授权、付费确认和任务重试安全规则。

## 包含的 Skills

| Skill | 适用场景 |
| --- | --- |
| [`byted-kickart-marketing-material-generator`](./byted-kickart-marketing-material-generator) | 生成商品营销视频、上传素材、一键成片或查询任务。 |
| [`byted-kickart-viral-replicator`](./byted-kickart-viral-replicator) | 参考热门视频为目标商品制作同款内容。 |
| [`byted-kickart-video-elements-replace`](./byted-kickart-video-elements-replace) | 替换角色、场景、商品、道具、声音等视频元素。 |

当前包含的 Skill 版本均为 `2.3.7`。

## 安装

使用 Fornax CLI 将 Skill 安装到 Agent 的 Skill 目录：

```bash
fornax-cli skill install \
  creative_platform.kickart.marketing_material_generator_for_doubao_partner \
  creative_platform.kickart.viral_replicator_for_doubao_partner \
  creative_platform.kickart.video_elements_replace_for_doubao_partner
```

将当前仓库中的 Skill 安装到指定的本地 Agent 目录：

```bash
fornax-cli skill install \
  --skill-query-json @./.fornax-cli/skill-sync.json \
  --dir <agent-skills-directory>
```

`.fornax-cli/` 用于保存本地同步配置和 CLI 生成文件，已加入 Git 忽略规则。

## 环境要求

- 支持 Skill 格式的 AI Agent 运行环境。
- Node.js 和 npm。
- 可访问 `@volcengine/kickart-open-mcp@1.1.x`。
- 有效的 KickArt OAuth 登录态，以及必要的权限或创点。
- 从 Fornax 同步时，需要安装 `fornax-cli` 并拥有目标空间访问权限。

每个 Skill 都会执行 CLI 和登录态检查，请勿提交 OAuth Token、AK/SK 或其他敏感信息。

## 使用

安装后，直接用自然语言向 Agent 描述任务即可：

- “为这个抖店商品生成营销视频。”
- “上传这些商品图片并生成营销短视频。”
- “参考这个热门视频为商品制作同款。”
- “替换参考视频中的人物和背景。”
- “查询视频生成任务进度。”

Agent 会自动选择匹配的 Skill，并按照对应流程完成信息收集、确认、提交和结果查询。

提交付费异步任务前，请确认任务参数、素材使用权以及必要的付费和合规确认。

## 文档

每个 Skill 都包含对应的详细操作文档：

- `SKILL.md`：触发条件、工作流规则、命令映射和安全约束。
- `references/`：CLI 配置、素材上传、参考视频、任务查询和具体操作指南。
- `assets/`：Skill 图标和静态资源。

使用某个 Skill 时，建议先阅读对应目录下的 `SKILL.md`：

```text
byted-kickart-marketing-material-generator/SKILL.md
byted-kickart-viral-replicator/SKILL.md
byted-kickart-video-elements-replace/SKILL.md
```

## 安全说明

- 仅使用你拥有合法使用、修改和发布授权的素材与商品内容。
- 不要暴露或持久化 OAuth Token、Access Key、Secret Key 或带签名的下载链接。
- 付费任务为异步且可能不可幂等。状态不确定时不要盲目重试，应先查询任务 ID。
- 配额、创点、认证、参数校验和媒体格式错误请遵循对应 Skill 中的错误处理指南。

## 许可证

包含的 Skill 使用 Apache License 2.0，详见各 Skill 目录中的 `LICENSE` 文件。
