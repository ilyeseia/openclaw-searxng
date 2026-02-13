# 🦞 OpenClaw with SearXNG Support

<p align="center">
    <img src="https://raw.githubusercontent.com/openclaw/openclaw/main/docs/assets/openclaw-logo-text.png" alt="OpenClaw" width="500">
</p>

<p align="center">
  <strong>Personal AI Assistant with Privacy-Focused Search</strong>
</p>

<p align="center">
  <a href="https://github.com/ilyeseia/openclaw-searxng"><img src="https://img.shields.io/badge/GitHub-ilyeseia%2Fopenclaw--searxng-blue?style=for-the-badge" alt="GitHub"></a>
  <a href="LICENSE"><img src="https://img.shields.io/badge/License-MIT-blue.svg?style=for-the-badge" alt="MIT License"></a>
</p>

---

## 🆕 What's New: SearXNG Support

This fork adds **SearXNG** as a search provider for `web_search`, enabling:

- ✅ **Privacy-focused search** - No tracking, no profiling
- ✅ **Self-hosted control** - Run your own search infrastructure
- ✅ **Metasearch engine** - Aggregate results from multiple sources
- ✅ **No API key required** - Most instances work without authentication
- ✅ **Customizable engines** - Choose Google, Bing, DuckDuckGo, and more

---

## 📋 Table of Contents

- [Prerequisites](#-prerequisites)
- [Installation](#-installation)
- [SearXNG Setup](#-searxng-setup)
- [Configuration](#-configuration)
- [Usage Examples](#-usage-examples)
- [Comparison with Other Providers](#-comparison-with-other-providers)
- [Troubleshooting](#-troubleshooting)
- [Contributing](#-contributing)

---

## 🔧 Prerequisites

Before installing OpenClaw with SearXNG support, ensure you have:

| Requirement | Version | Notes |
|-------------|---------|-------|
| Node.js | ≥ 22 | Required runtime |
| npm/pnpm/bun | Latest | Package manager |
| Git | Latest | For cloning the repository |
| Docker (optional) | Latest | For running SearXNG |

### Check your Node.js version:

```bash
node --version  # Should be v22.x.x or higher
```

---

## 📦 Installation

### Option 1: Clone and Build from Source

```bash
# Clone the repository
git clone https://github.com/ilyeseia/openclaw-searxng.git
cd openclaw-searxng

# Install dependencies (prefer pnpm)
pnpm install

# Build the project
pnpm build

# Run the onboarding wizard
pnpm openclaw onboard --install-daemon
```

### Option 2: Quick Start with npx

```bash
# Run directly without cloning
npx openclaw onboard --install-daemon
```

### Option 3: Global Installation

```bash
# Install globally
npm install -g openclaw@latest

# Or with pnpm
pnpm add -g openclaw@latest

# Run onboarding
openclaw onboard --install-daemon
```

---

## 🐳 SearXNG Setup

### Quick Setup with Docker

The fastest way to get SearXNG running:

```bash
# Pull and run SearXNG
docker run -d \
  --name searxng \
  -p 8888:8080 \
  -e SEARXNG_BASE_URL=http://localhost:8888/ \
  searxng/searxng:latest
```

### Docker Compose Setup (Recommended)

Create a `docker-compose.yml` file:

```yaml
version: '3.8'

services:
  searxng:
    image: searxng/searxng:latest
    container_name: searxng
    ports:
      - "8888:8080"
    environment:
      - SEARXNG_BASE_URL=http://localhost:8888/
    volumes:
      - ./searxng:/etc/searxng
    restart: unless-stopped
```

Then run:

```bash
docker-compose up -d
```

### Verify SearXNG is Running

```bash
# Test the API
curl "http://localhost:8888/search?q=test&format=json"
```

You should receive a JSON response with search results.

---

## ⚙️ Configuration

### Environment Variables

Set these environment variables for SearXNG:

```bash
# Required
export SEARXNG_BASE_URL="http://localhost:8888"

# Optional (if your instance requires authentication)
export SEARXNG_API_KEY="your-api-key"
```

### Configuration File

Edit `~/.openclaw/openclaw.json`:

```json5
{
  tools: {
    web: {
      search: {
        // Set SearXNG as the default search provider
        provider: "searxng",
        
        // Number of results to return (1-10)
        maxResults: 5,
        
        // Timeout in seconds
        timeoutSeconds: 30,
        
        // Cache results for 60 minutes
        cacheTtlMinutes: 60,
        
        // SearXNG-specific configuration
        searxng: {
          // Your SearXNG instance URL
          baseUrl: "http://localhost:8888",
          
          // API key if required (optional)
          // apiKey: "your-api-key",
          
          // Specify search engines (optional)
          engines: ["google", "bing", "duckduckgo"]
        }
      }
    }
  }
}
```

### Available Search Providers

OpenClaw now supports 4 search providers:

| Provider | Type | API Key Required | Description |
|----------|------|------------------|-------------|
| `brave` | Cloud API | Yes | Default, fast, reliable |
| `perplexity` | Cloud AI | Yes | AI-synthesized answers with citations |
| `grok` | Cloud AI | Yes | xAI's Grok with web search |
| `searxng` | Self-hosted | No | Privacy-focused metasearch |

---

## 🚀 Usage Examples

### Start the Gateway

```bash
# Start the gateway
openclaw gateway --port 18789 --verbose

# Or in development mode with auto-reload
pnpm gateway:watch
```

### Search with SearXNG

```bash
# Send a search query
openclaw agent --message "Search for latest AI news"

# The agent will use SearXNG automatically
```

### Switch Between Providers

```json5
// Switch to Brave
{
  tools: {
    web: {
      search: { provider: "brave" }
    }
  }
}

// Switch to SearXNG
{
  tools: {
    web: {
      search: { provider: "searxng" }
    }
  }
}
```

### Using with Chat Commands

In WhatsApp, Telegram, Slack, or Discord:

```
Search for: open source AI projects
```

The assistant will automatically use the configured search provider.

---

## 📊 Comparison with Other Providers

### Feature Matrix

| Feature | SearXNG | Brave | Perplexity | Grok |
|---------|:-------:|:-----:|:----------:|:----:|
| Self-hosted | ✅ | ❌ | ❌ | ❌ |
| Privacy-focused | ✅ | ✅ | ❌ | ❌ |
| No API key needed | ✅ | ❌ | ❌ | ❌ |
| AI-synthesized answers | ❌ | ❌ | ✅ | ✅ |
| Multiple search engines | ✅ | ❌ | ❌ | ❌ |
| Cost | Free | Freemium | Paid | Paid |
| Rate limits | None* | Yes | Yes | Yes |

*Only limited by your own infrastructure

### When to Use SearXNG

**Best for:**
- Privacy-conscious users
- Self-hosted infrastructure
- Organizations with data policies
- Cost-sensitive applications
- Custom search configurations

**Consider alternatives when:**
- You need AI-synthesized answers (use Perplexity/Grok)
- You don't want to manage infrastructure (use Brave)
- You need guaranteed uptime SLAs

---

## 🔍 Troubleshooting

### Connection Issues

```bash
# Check if SearXNG is running
curl http://localhost:8888/health

# Check if the port is open
lsof -i :8888
```

### CORS Issues

If using a web interface, configure CORS in SearXNG:

```yaml
# In SearXNG settings.yml
server:
  cors_allowed_origins:
    - "*"
```

### No Results Returned

1. Check your search engines configuration
2. Verify the SearXNG instance has access to the internet
3. Check SearXNG logs: `docker logs searxng`

### Rate Limiting

Public instances may have rate limits. Solutions:
- Host your own instance
- Reduce `maxResults`
- Increase `cacheTtlMinutes`

---

## 📁 Project Structure

```
openclaw-searxng/
├── src/
│   ├── agents/
│   │   └── tools/
│   │       ├── web-search.ts      # Search providers implementation
│   │       └── web-fetch.ts       # Web fetch functionality
│   ├── config/
│   │   ├── types.tools.ts         # TypeScript type definitions
│   │   └── schema.hints.ts        # UI hints and labels
│   └── ...
├── docs/
│   ├── searxng-search.md          # SearXNG documentation (EN)
│   └── zh-CN/
│       └── searxng-search.md      # SearXNG documentation (CN)
├── README.md                       # This file
└── ...
```

---

## 🔨 Development

### Run in Development Mode

```bash
# Install dependencies
pnpm install

# Build UI (first time only)
pnpm ui:build

# Run with auto-reload
pnpm gateway:watch
```

### Run Tests

```bash
# Run all tests
pnpm test

# Run specific tests
pnpm test web-search
```

### Linting

```bash
# Check code quality
pnpm lint
```

---

## 📖 Additional Documentation

- [SearXNG Documentation](https://docs.searxng.org/)
- [OpenClaw Main Docs](https://docs.openclaw.ai)
- [Web Tools Configuration](https://docs.openclaw.ai/tools/web)
- [Getting Started Guide](https://docs.openclaw.ai/start/getting-started)

---

## 🤝 Contributing

Contributions are welcome! Please see [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

### How to Contribute

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

---

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

## 🙏 Credits

- **OpenClaw** - Original project by [Peter Steinberger](https://github.com/steipete) and contributors
- **SearXNG** - Privacy-respecting metasearch engine
- **All contributors** who helped make this possible

---

## 📮 Support

- **Issues**: [GitHub Issues](https://github.com/ilyeseia/openclaw-searxng/issues)
- **Discord**: [OpenClaw Discord](https://discord.gg/clawd)
- **Documentation**: [docs.openclaw.ai](https://docs.openclaw.ai)

---

<p align="center">
  <strong>Made with ❤️ by the OpenClaw Community</strong>
</p>
