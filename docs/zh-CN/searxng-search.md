---
summary: "SearXNG 搜索 API 设置用于 web_search"
read_when:
  - 您想使用 SearXNG 进行网页搜索
  - 您需要一个自托管的隐私友好型搜索引擎
title: "SearXNG 搜索"
---

# SearXNG 搜索 API

OpenClaw 支持 SearXNG 作为 `web_search` 的搜索提供商。SearXNG 是一个免费的互联网元搜索引擎，在尊重隐私的同时聚合来自各种搜索服务的结果。

## 为什么选择 SearXNG？

- **隐私优先**：无跟踪，无画像
- **自托管**：完全控制您的搜索基础设施
- **元搜索**：聚合多个搜索引擎的结果
- **可定制**：选择使用哪些引擎
- **无需 API 密钥**：大多数实例不需要身份验证

## 设置

### 1. 部署 SearXNG

您可以使用 Docker 部署 SearXNG：

```bash
docker run -d \
  --name searxng \
  -p 8888:8080 \
  -e SEARXNG_BASE_URL=http://localhost:8888/ \
  searxng/searxng:latest
```

或使用 Docker Compose：

```yaml
version: '3'
services:
  searxng:
    image: searxng/searxng:latest
    ports:
      - "8888:8080"
    environment:
      - SEARXNG_BASE_URL=http://localhost:8888/
    volumes:
      - ./searxng:/etc/searxng
```

### 2. 配置 OpenClaw

设置环境变量：

```bash
export SEARXNG_BASE_URL="http://localhost:8888"
```

或在 OpenClaw 配置文件中配置：

```json5
{
  tools: {
    web: {
      search: {
        provider: "searxng",
        maxResults: 5,
        timeoutSeconds: 30,
        searxng: {
          baseUrl: "http://localhost:8888",
          // 可选：指定搜索引擎
          engines: ["google", "bing", "duckduckgo"],
          // 可选：如果您的实例需要 API 密钥
          // apiKey: "your-api-key",
        },
      },
    },
  },
}
```

## 配置选项

### 环境变量

| 变量 | 描述 | 默认值 |
|------|------|--------|
| `SEARXNG_BASE_URL` | SearXNG 实例的基础 URL | `http://localhost:8888` |
| `SEARXNG_API_KEY` | API 密钥（如果您的实例需要） | - |

### 配置选项

| 选项 | 类型 | 描述 | 默认值 |
|------|------|------|--------|
| `baseUrl` | string | SearXNG 实例 URL | `http://localhost:8888` |
| `apiKey` | string | 如果需要的 API 密钥 | - |
| `engines` | string[] | 要使用的搜索引擎列表 | 所有可用的引擎 |

## 常用搜索引擎

SearXNG 支持许多搜索引擎。以下是一些常用的：

- `google` - 谷歌搜索
- `bing` - 微软必应
- `duckduckgo` - DuckDuckGo
- `wikipedia` - 维基百科
- `github` - GitHub
- `youtube` - YouTube
- `reddit` - Reddit
- `stackoverflow` - Stack Overflow

您可以指定多个引擎：

```json5
{
  tools: {
    web: {
      search: {
        provider: "searxng",
        searxng: {
          baseUrl: "http://localhost:8888",
          engines: ["google", "bing", "duckduckgo"],
        },
      },
    },
  },
}
```

## 公共 SearXNG 实例

如果您不想托管自己的实例，可以使用公共 SearXNG 实例：

- https://searx.be
- https://search.bus-hit.me
- https://search.rowie.at
- https://searx.fmac.xyz

> **注意**：公共实例可能有速率限制或正常运行时间问题。对于生产使用，请考虑托管您自己的实例。

## 示例响应

使用 SearXNG 时，`web_search` 工具返回与 Brave 类似的结果：

```json
{
  "query": "开源人工智能",
  "provider": "searxng",
  "count": 5,
  "results": [
    {
      "title": "开源 AI - 示例和项目",
      "url": "https://example.com/open-source-ai",
      "description": "探索开源 AI 项目...",
      "engine": "google",
      "siteName": "example.com"
    }
  ]
}
```

## 与其他提供商的区别

| 特性 | SearXNG | Brave | Perplexity | Grok |
|------|---------|-------|------------|------|
| 自托管 | 是 | 否 | 否 | 否 |
| 隐私优先 | 是 | 是 | 否 | 否 |
| 需要 API 密钥 | 否 | 是 | 是 | 是 |
| AI 综合答案 | 否 | 否 | 是 | 是 |
| 多引擎支持 | 是 | 否 | 否 | 否 |

## 故障排除

### 连接被拒绝

如果出现连接错误，请确保 SearXNG 正在运行：

```bash
curl http://localhost:8888/search?q=test&format=json
```

### CORS 问题

如果使用 Web 界面，您可能需要在 SearXNG 设置中配置 CORS：

```yaml
# 在 settings.yml 中
server:
  cors_allowed_origins:
    - "*"
```

### 速率限制

一些公共实例有速率限制。请考虑：
- 托管您自己的实例
- 减少 `maxResults`
- 增加 `cacheTtlMinutes`

## 另请参阅

- [Web 工具](/tools/web) 获取完整的 web_search 配置
- [Brave 搜索](/brave-search) 了解默认搜索提供商
- [SearXNG 文档](https://docs.searxng.org/)
