**List of Technologies Used in Nanobot Project**

To determine the tech stack of the nanobot project, I'll analyze the codebase structure, dependencies, and configuration files:
### Core Language
- **Python (≥3.11)**: Main programming language for the core agent, tools, and integrations.

### Dependencies & Libraries
- **Loguru**: For logging.
- **Asyncio**: For asynchronous operations (e.g., message processing, tool execution).
- **Pydantic**: For configuration schema validation (used in `nanobot/config/schema.py`).
- **Node.js (≥18)**: Required for the WhatsApp bridge (in the `bridge/` directory).

### LLM Providers (Integrations)
- **OpenAI**
- **Anthropic**
- **Azure OpenAI**
- **DeepSeek**
- **Groq** (for LLM + voice transcription via Whisper)
- **Gemini**
- **MiniMax**
- **AiHubMix**
- **SiliconFlow**
- **VolcEngine**
- **Dashscope** (Qwen)
- **Moonshot** (Kimi)
- **Zhipu** (GLM)
- **Ollama** (local LLMs)
- **vLLM** (local OpenAI-compatible servers)
- **OpenAI Codex** (via OAuth)
- **GitHub Copilot** (via OAuth)

### Chat Channels (Integrations)
- **Telegram** (Bot API)
- **Discord** (Discord API)
- **WhatsApp** (Node.js bridge)
- **Feishu** (WebSocket long connection)
- **Mochat** (Socket.IO WebSocket)
- **DingTalk** (Stream Mode)
- **Slack** (Socket Mode)
- **Email** (IMAP/SMTP)
- **QQ** (botpy SDK with WebSocket)
- **WeCom** (WebSocket long connection)
- **Matrix** (Element, with optional E2EE support)

### Protocols & Standards
- **MCP (Model Context Protocol)**: For integrating external tool servers.
- **WebSocket**: Used for real-time communication with several channels (Feishu, Mochat, QQ, WeCom).
- **Socket.IO**: Used for Mochat integration.

### Deployment & DevOps
- **Docker**: Containerized deployment (Dockerfile and docker-compose.yml provided).
- **Linux Systemd**: For running the gateway as a user service.
- **uv**: Recommended for fast installation (alternative to pip).

### Tools & Utilities
- **Built-in Tools**: File operations (read/write/edit/list), shell execution, web search (Brave API), web fetch, message sending, subagent spawning, cron management.
- **MCP Tools**: External tool servers integrated via MCP.

### Memory & Storage
- **Local File System**: For session storage, memory consolidation, and workspace files.

This tech stack enables nanobot to be lightweight yet powerful, supporting multiple chat platforms and LLM providers while maintaining a minimal codebase.
        