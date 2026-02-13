---
summary: "SearXNG Search API setup for web_search"
read_when:
  - You want to use SearXNG for web_search
  - You need a self-hosted privacy-focused search engine
title: "SearXNG Search"
---

# SearXNG Search API

OpenClaw supports SearXNG as a search provider for `web_search`. SearXNG is a free internet metasearch engine which aggregates results from various search services while respecting privacy.

## Why SearXNG?

- **Privacy-focused**: No tracking, no profiling
- **Self-hosted**: Full control over your search infrastructure
- **Metasearch**: Aggregates results from multiple search engines
- **Customizable**: Choose which engines to use
- **No API key required**: Most instances don't require authentication

## Setup

### 1. Deploy SearXNG

You can deploy SearXNG using Docker:

```bash
docker run -d \
  --name searxng \
  -p 8888:8080 \
  -e SEARXNG_BASE_URL=http://localhost:8888/ \
  searxng/searxng:latest
```

Or use Docker Compose:

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

### 2. Configure OpenClaw

Set the environment variable:

```bash
export SEARXNG_BASE_URL="http://localhost:8888"
```

Or configure in your OpenClaw config file:

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
          // Optional: specify search engines
          engines: ["google", "bing", "duckduckgo"],
          // Optional: API key if your instance requires it
          // apiKey: "your-api-key",
        },
      },
    },
  },
}
```

## Configuration Options

### Environment Variables

| Variable | Description | Default |
|----------|-------------|---------|
| `SEARXNG_BASE_URL` | Base URL of your SearXNG instance | `http://localhost:8888` |
| `SEARXNG_API_KEY` | API key (if required by your instance) | - |

### Config Options

| Option | Type | Description | Default |
|--------|------|-------------|---------|
| `baseUrl` | string | SearXNG instance URL | `http://localhost:8888` |
| `apiKey` | string | API key if required | - |
| `engines` | string[] | List of search engines to use | All available |

## Popular Search Engines

SearXNG supports many search engines. Here are some popular ones:

- `google` - Google Search
- `bing` - Microsoft Bing
- `duckduckgo` - DuckDuckGo
- `wikipedia` - Wikipedia
- `github` - GitHub
- `youtube` - YouTube
- `reddit` - Reddit
- `stackoverflow` - Stack Overflow

You can specify multiple engines:

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

## Public SearXNG Instances

If you don't want to host your own instance, you can use public SearXNG instances:

- https://searx.be
- https://search.bus-hit.me
- https://search.rowie.at
- https://searx.fmac.xyz

> **Note**: Public instances may have rate limits or uptime issues. For production use, consider hosting your own instance.

## Example Response

When using SearXNG, the `web_search` tool returns results similar to Brave:

```json
{
  "query": "open source ai",
  "provider": "searxng",
  "count": 5,
  "results": [
    {
      "title": "Open Source AI - Examples and Projects",
      "url": "https://example.com/open-source-ai",
      "description": "Explore open source AI projects...",
      "engine": "google",
      "siteName": "example.com"
    }
  ]
}
```

## Differences from Other Providers

| Feature | SearXNG | Brave | Perplexity | Grok |
|---------|---------|-------|------------|------|
| Self-hosted | Yes | No | No | No |
| Privacy-focused | Yes | Yes | No | No |
| API key required | No | Yes | Yes | Yes |
| AI-synthesized answers | No | No | Yes | Yes |
| Multiple engines | Yes | No | No | No |

## Troubleshooting

### Connection Refused

If you get connection errors, ensure SearXNG is running:

```bash
curl http://localhost:8888/search?q=test&format=json
```

### CORS Issues

If using a web interface, you may need to configure CORS in your SearXNG settings:

```yaml
# In settings.yml
server:
  cors_allowed_origins:
    - "*"
```

### Rate Limiting

Some public instances have rate limits. Consider:
- Hosting your own instance
- Reducing `maxResults`
- Increasing `cacheTtlMinutes`

## See Also

- [Web tools](/tools/web) for full web_search configuration
- [Brave Search](/brave-search) for the default search provider
- [SearXNG Documentation](https://docs.searxng.org/)
