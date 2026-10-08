# 视频元素替换 CLI 前置校验指南

## 1. 适用范围

所有视频元素替换操作必须通过固定版本 CLI 执行，不得直接请求 OpenAPI。

固定 CLI 前缀：

```bash
npm exec -y --registry=https://registry.npmjs.org --package @volcengine/kickart-open-mcp@1.1.x -- kickart-open-cli
```

| CLI 命令 | 用途 | 关键输出 |
| --- | --- | --- |
| `auth_token` | 检查 OAuth 登录态并自动刷新临期 token | 当前 access token |
| `auth_login` | 启动 OAuth 设备授权并立即返回 | 用户授权链接 |
| `auth_complete` | 用户完成网页授权后，单次请求 token 并完成登录 | 登录及凭据保存结果 |
| `submit_high_similarity_task_v2` | 提交视频元素替换任务 | `task_id`、`request_id`、`request_body` |
| `query_task_v2` | 查询任务状态和结果 | `status`、`progress`、`result_url`、`response_file_path` |

## 2. 强制前置校验

1. 执行 `command -v npm` 检查 `npm exec` 是否可用。npm 不存在时，反馈当前环境缺少 Node.js/npm 并终止操作。
2. 所有 CLI 命令固定使用公网 npm registry 和 `@volcengine/kickart-open-mcp@1.1.x`，不得改用其他 KickArt CLI 包或 registry。
3. 执行 `auth_token >/dev/null` 检查登录态。必须丢弃 stdout，禁止显示、记录或持久化 token。
4. npm exec 下载包或启动 CLI 失败时，反馈脱敏后的 npm 错误并终止操作，不得继续调用业务命令。
5. 登录态检查失败时执行 `auth_login`。该命令只启动设备授权并立即返回授权链接，不会轮询授权状态。
6. 将授权链接展示给用户，引导用户在浏览器完成授权。必须暂停流程，等待用户明确确认已完成网页授权；禁止自动轮询、禁止提前执行 `auth_complete`。
7. 用户确认后，单次执行 `auth_complete` 请求 token 并保存登录凭据。若返回授权尚未完成，告知用户继续完成网页授权并等待再次确认，不得自动重试。
8. `auth_complete` 成功后再次执行 `auth_token >/dev/null`；仍失败则反馈 stderr 中已脱敏的错误并终止操作。
9. 不要求用户提供长期密钥，也不主动要求用户提供 access token。只有用户明确选择导入已有 token 时，才通过标准输入传递，不得把真实 token 写入命令文本、日志或对话。

### OAuth 设备授权交互

```bash
npm exec -y --registry=https://registry.npmjs.org --package @volcengine/kickart-open-mcp@1.1.x -- kickart-open-cli auth_login
# 等待用户在网页完成授权并明确确认后执行
npm exec -y --registry=https://registry.npmjs.org --package @volcengine/kickart-open-mcp@1.1.x -- kickart-open-cli auth_complete
```

`auth_login` 与 `auth_complete` 必须作为两个独立阶段执行。不得把两个命令连续执行，也不得使用循环反复调用 `auth_complete`。

## 3. CLI 调用规则

1. 首次使用命令或参数不确定时，先执行 `submit_high_similarity_task_v2 --help` 或 `query_task_v2 --help`，以实际帮助为准，不猜测未声明的选项。
2. `submit_high_similarity_task_v2` 的必填参数为 `--ref-video`、`--replace-images` 和 `--prompt`。
3. `--language` 可选，支持 `zh`、`en`、`en-us`、`pt-br`、`ja`、`es-mx`、`id`、`ms`、`tl`，默认 `zh`。
4. `--template-id` 必填；Seedance 2.5 使用 `978755330`，Seedance 2.0 mini 使用 `1028571394`，不得猜测或自行替换模板 ID。
5. `--replace-images` 支持逗号分隔列表或重复传参。具体多值语法以 `--help` 为准，必须保持用户提供顺序。
6. 本地参考视频和图片由提交 CLI 自动上传，不调用独立素材入库流程；公网 URL 可直接传入。
7. 本地图片须校验文件存在、扩展名为 JPEG/PNG 且不超过 8MB；本地视频须校验文件存在、扩展名为 MP4/MOV 且不超过 50MB。
8. 所有路径、URL 和提示词必须作为独立参数安全传递，不手工拼接未经转义的命令字符串。
9. 提交成功后从 stdout JSON 保存 `task_id`、`request_id`、任务类型、提交时间、会话 ID 和参数摘要。
10. 提交后不自动轮询，仅在用户主动查询时执行 `query_task_v2`。

## 4. 基础提交命令

实际执行前必须完成 OAuth 检查、模型选择、参数收集、成片信息确认和扣费合规二次确认。

```bash
npm exec -y --registry=https://registry.npmjs.org --package @volcengine/kickart-open-mcp@1.1.x -- kickart-open-cli submit_high_similarity_task_v2 \
  --template-id 978755330 \
  --ref-video https://example.com/ref.mp4 \
  --replace-images https://example.com/replace-1.png,https://example.com/replace-2.png \
  --prompt "将视频中的人物替换为图1，将商品替换为图2，保持原视频结构和节奏。" \
  --language zh
```

禁止在未完成用户确认前执行提交。禁止在提交失败后自动重试付费任务。

## 5. 输出与错误处理

1. CLI 退出码为 `0` 时解析 stdout JSON；日志文本不得当作业务 JSON。
2. CLI 非零退出时读取 stderr，先移除 token 等敏感信息，再向用户说明原因和下一步操作。
3. 无法确认请求是否已受理时禁止再次提交；若输出中有 `task_id`，按 `任务查询指南.md` 查询。
4. 只有明确证明请求未发送，且用户重新完成成片信息确认和二次确认后，才能再次提交。
