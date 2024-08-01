+++
title = 'JavaScript/TypeScript 免费调用 gpt-4o-mini'
slug = 'duckduckgo-ai-chat-api'
date = 2024-08-01T16:12:35+08:00
description = '用 duckduckgo-ai-chat 免费调用 gpt-4o-mini'
keywords = ['gpt-4o-mini', 'AI', 'chatbot', 'TypeScript', 'JavaScript', 'Free', '免费']
tags = ['AI', 'TypeScript', 'JavaScript', 'Free']
categories = ['Tech']
+++

[Duckduckgo AI Chat](https://duckduckgo.com/aichat) 提供了免费的 gpt-4o-mini，我开发了一个小库 [duckduckgo-ai-chat](https://jsr.io/@mumulhl/duckduckgo-ai-chat) 用来调用 Duckduckgo AI Chat 的 API。

## 注意事项

Duckduckgo AI Chat 是有针对 IP 的额度限制的，**不要滥用**。

## 小故事

在 jsr.io 上的 duckduckgo-ai-chat 之所以第一个版本是 2.0.0，是因为 1.0.0 基于 Bun 编写发布在 npm 上，发完才发现 bun 不符合我的 all-in-one 需求，所以就换 Deno 了...

顺便发到了 Deno 团队新搞的 jsr.io 上，不仅支持用不同包管理器安装，而且还支持在任何运行时上跑。

## 兼容性

除了 Bun，其他运行时都没问题。

## 安装

```sh
npx jsr add @mumulhl/duckduckgo-ai-chat
# or
pnpm dlx jsr add @mumulhl/duckduckgo-ai-chat
# or
yarn dlx jsr add @mumulhl/duckduckgo-ai-chat
# or
deno add @mumulhl/duckduckgo-ai-chat
```

## 使用示例

```ts
import { initChat } from "@mumulhl/duckduckgo-ai-chat";

// 初始化，可选模型有 gpt-4o-mini, claude-3-haiku-20240307, meta-llama/Llama-3-70b-chat-hf, mistralai/Mixtral-8x7B-Instruct-v0.1
const chat = await initChat("gpt-4o-mini");

// 一次性获取完整的回复
let message = await chat.fetchFull("Hello");
console.log(message)

// 重新回复
chat.redo()
message = await chat.fetchFull("Hello");
console.log(message)

// 获取流式回复
const stream = chat.fetchStream("Hello");
for await (let data of stream) {
  console.log(data)
}
```
