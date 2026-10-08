---
name: byted-kickart-video-elements-replace
label: 视频元素替换
icon: assets/icon.png
description: 基于参考视频和替换素材替换视频元素，支持替换角色、场景、商品、道具、声音等内容。当用户要求替换视频元素、生成高相似度视频时使用本技能。
version: 2.3.7
---

# 视频元素替换SKILL

## 🚨 强制前置校验流程

所有 KickArt 操作必须先完成 CLI 可用性与 OAuth 登录态检查：

1. **KickArt CLI 校验**
   - 读取 `references/CLI前置校验指南.md`。
   - 使用 `command -v npm` 检查 `npm exec` 是否可用。
   - 所有命令固定使用 `npm exec -y --registry=https://registry.npmjs.org --package @volcengine/kickart-open-mcp@1.1.x -- kickart-open-cli`。
   - 执行 `auth_token >/dev/null` 检查登录态，禁止展示、记录或持久化 token。
   - 尚未登录或凭据失效时，执行 `auth_login` 获取授权链接并展示给用户；该命令立即返回，不会轮询授权状态。
   - 等待用户明确确认已完成网页授权后，单次执行 `auth_complete` 获取 token 并完成登录，再次检查登录态；禁止自动轮询或在用户确认前执行。
   - npm exec 下载包失败或 CLI 仍不可用时，反馈脱敏后的 npm 错误并终止操作。
   - 不要求用户提供长期密钥，不读取或回显 OAuth token，禁止直接请求 OpenAPI。

## 🎯 意图识别与任务执行指南

结合当前用户输入和历史记录判断意图后，必须严格读取并遵循对应执行依据指南，不可凭空猜测执行步骤。

| 任务类型 | 触发意图与关键词 | 执行依据 | 输出要求 | 前置要求 |
|---------|----------------|---------|---------|---------|
| **进度查询** | 用户提及「查询进度」「查看结果」「生成怎么样了」「好了吗」或查询具体任务 ID | 读取 `references/任务查询指南.md`，执行 `query_task_v2` | 解析 stdout JSON 并输出当前状态或结果 | 需从会话上下文获取任务 ID |
| **视频元素替换** | 用户提及「替换角色」「替换场景」「替换商品」「替换道具」「替换声音」「视频元素替换」等 | 读取 `references/视频元素替换指南.md`，执行 `submit_high_similarity_task_v2` | 解析 stdout JSON 并输出任务 ID；后续通过 `query_task_v2` 获取结果 | 必须逐步收集参考视频、语言、模型、替换素材、创作提示词，并完成两次提交确认 |
| **视频参考** | 用户主动提供参考视频、视频链接，或表示要上传视频 | 读取 `references/视频参考指南.md` | 获取可直接传给 `--ref-video` 的素材值 | 视频元素替换的首要前置步骤 |
| **替换素材** | 用户主动提供替换角色、场景、商品或道具图片，或提出声音等非图片元素替换需求 | 读取 `references/替换图片指南.md` | 获取按用户顺序排列、可直接传给 `--replace-images` 的图片列表；其他元素通过 `--prompt` 描述 | 必须逐项确认接收顺序，替换关系在创作提示词中说明 |

## ⚠️ 错误处理规范

所有错误必须明确告知原因和可执行解决方案，禁止模糊提示。

| 错误码 | 错误类型 | 错误描述 | 用户处理建议 |
| --- | --- | --- | --- |
| 0 | Success | 任务成功，结果已生成 | - |
| 1000 | AsyncTaskRunning | 任务正在处理 | 稍后通过任务查询获取结果 |
| 1300 | AuthFailed | OAuth 认证失败 | 依次执行 `auth_login`、等待网页授权、执行 `auth_complete`；禁止自动重试付费提交 |
| 1400 | ParamErr | 参数错误 | 按 CLI `--help` 检查字段和必填项 |
| 1401 | ConcurrentErr | 并发超限 | 降低请求频率或联系管理员 |
| 1402 | InsufficientPoints | 创点不足 | 充值创点或升级套餐；不得继续提交 |
| 1403 | QuotaErr | 套餐或权限不足 | 联系管理员开通权限 |
| 1410 | TemplateIdNotExist | 模板不存在 | 确认 `--template-id` 是否为服务端支持的模板；Seedance 2.5 使用 `978755330`，Seedance 2.0 mini 使用 `1028571394` |
| 1412 | ImageFormatErr | 图片格式错误 | 使用 JPEG 或 PNG 图片 |
| 1413 | InvalidMediaUrlErr | 媒体 URL 无效 | 确保 URL 公网可达且未过期 |
| 1418 | InvalidDurationBillingErr | 输入视频时长不符合要求 | 按服务端错误提示更换参考视频 |
| 1423 | VideoResolutionError | 视频分辨率错误 | 提供符合服务端要求的参考视频 |
| 1424 | VideoDurationError | 视频时长错误 | 更换符合服务端要求的参考视频 |
| 1425 | VideoFormatError | 视频格式不支持 | 使用 MP4 或 MOV 视频 |
| 1426 | VideoSizeError | 视频大小超限 | 压缩视频后重新提供 |
| 1427 | RefVideoError | 参考视频链接错误 | 检查 URL 是否有效 |
| 1428 | LanguageEnumError | 语言参数错误 | 使用 `zh`、`en`、`en-us`、`pt-br`、`ja`、`es-mx`、`id`、`ms` 或 `tl` |
| 1450 | InputValidationErr | 模板入参校验失败 | 按 message 修正参数，重新确认后再提交 |
| 1500 | InternalErr | 服务端内部错误 | 记录脱敏后的 `request_id` 并联系技术支持 |
| 1600 | AsyncTaskNotExist | 任务不存在 | 检查任务 ID |
| 1601 | AsyncTaskTimeoutErr | 任务超时 | 联系技术支持；新建任务必须重新确认 |
| 2000 | AsyncTaskFailed | 任务失败 | 根据 message 修正问题，禁止自动重试付费任务 |

## 安全与提交规则

- 参考视频、替换素材及创作内容必须获得合法使用、改编和商业发布授权。
- 只有用户完成成片信息确认和扣费、合规二次确认后才能提交。
- `submit_high_similarity_task_v2` 会创建付费异步任务，不保证幂等，任何失败场景均禁止自动重试。
- 无法确认请求是否已受理时，不得再次提交；若有 `task_id`，按任务查询指南查询。
- 提交成功后返回真实 `task_id`，不把“已提交”表述为“已生成”。
