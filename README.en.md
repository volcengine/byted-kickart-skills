# KickArt Agent Skills

[简体中文](README.md) | [English](README.en.md)

Reusable AI Agent skills for creating and editing short-form marketing videos with
[KickArt](https://www.volcengine.com/). This repository supports Douyin and Dou店
e-commerce workflows, including product video generation, viral video replication,
and video element replacement.

## Features

- Generate product marketing videos from Douyin or Dou店 product information.
- Upload and use custom image and video materials.
- Replicate the structure, pacing, and style of a popular reference video.
- Replace characters, scenes, products, props, sounds, and other video elements.
- Submit asynchronous tasks and query their progress and results.
- Apply OAuth, authorization, payment confirmation, and retry safeguards.

## Included Skills

| Skill | Use case |
| --- | --- |
| [`byted-kickart-marketing-material-generator`](./byted-kickart-marketing-material-generator) | Generate product marketing videos, upload materials, create one-click videos, or query tasks. |
| [`byted-kickart-viral-replicator`](./byted-kickart-viral-replicator) | Create a popular-video-style version for a target product. |
| [`byted-kickart-video-elements-replace`](./byted-kickart-video-elements-replace) | Replace characters, scenes, products, props, sounds, or other video elements. |

All included skills are currently version `2.3.7`.

## Installation

Install the skills into an agent's skill directory with the Fornax CLI:

```bash
fornax-cli skill install \
  creative_platform.kickart.marketing_material_generator_for_doubao_partner \
  creative_platform.kickart.viral_replicator_for_doubao_partner \
  creative_platform.kickart.video_elements_replace_for_doubao_partner
```

To install the checked-out repository contents into a local agent directory:

```bash
fornax-cli skill install \
  --skill-query-json @./.fornax-cli/skill-sync.json \
  --dir <agent-skills-directory>
```

The `.fornax-cli/` directory is intentionally ignored because it contains local sync
configuration and generated CLI output.

## Requirements

- An AI Agent runtime that supports the Skill format.
- Node.js and npm.
- Access to `@volcengine/kickart-open-mcp@1.1.x`.
- A valid KickArt OAuth session and sufficient account permissions or credits.
- For Fornax sync, install `fornax-cli` and obtain access to the target workspace.

Each Skill performs its own CLI and authentication checks. Never commit OAuth tokens,
AK/SK credentials, or other secrets.

## Usage

After installation, describe your task to the Agent in natural language:

- "Generate a marketing video for this Dou店 product."
- "Upload these product images and create a short marketing video."
- "Make a popular-video-style version for this product."
- "Replace the person and background in this reference video."
- "Check the progress of the video generation task."

The Agent selects the matching Skill and follows its required information collection,
confirmation, submission, and result-query workflow.

Before submitting a paid asynchronous task, review the parameters, confirm media
rights, and complete the required billing and compliance confirmation.

## Documentation

Each Skill contains detailed, task-specific documentation:

- `SKILL.md`: trigger conditions, workflow rules, command mapping, and safety constraints.
- `references/`: CLI setup, material upload, reference video, task query, and operation guides.
- `assets/`: skill icons and static assets.

Start with the `SKILL.md` in the Skill you want to use:

```text
byted-kickart-marketing-material-generator/SKILL.md
byted-kickart-viral-replicator/SKILL.md
byted-kickart-video-elements-replace/SKILL.md
```

## Safety and Usage Notes

- Use only media and product content that you are authorized to use, modify, and publish.
- Do not expose or persist OAuth tokens, access keys, secret keys, or signed download URLs.
- Paid task submission is asynchronous and may not be idempotent. Do not retry blindly
  when the request status is uncertain; query the returned task ID first.
- Follow the error guidance in the selected Skill for quota, credits, authentication,
  validation, and media-format errors.

## License

The included Skills are distributed under the Apache License 2.0. See the `LICENSE`
file in each Skill directory.
