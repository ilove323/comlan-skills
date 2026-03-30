# Slack 插件

此插件将 Slack 与 OpenClaw 集成，提供在 Slack 中搜索、阅读和发送消息的工具。

## 命令

- `/slack:summarize-channel <频道名称>` — 总结 Slack 频道中的最近活动
- `/slack:find-discussions <话题>` — 跨 Slack 频道查找关于特定话题的讨论
- `/slack:draft-announcement <话题>` — 起草格式规范的 Slack 公告并保存为草稿
- `/slack:standup` — 根据您最近的 Slack 活动生成站会更新
- `/slack:channel-digest <频道1, 频道2, ...>` — 获取多个 Slack 频道的最近活动摘要

## 技能

- **slack-messaging** — 使用 mrkdwn 语法撰写格式规范、有效的 Slack 消息的指导
- **slack-search** — 在 Slack 中有效搜索消息、文件、频道和人员的指导
