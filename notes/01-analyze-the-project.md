**Analyze the Project Structure**

        
Let me start by exploring the project structure to understand what we're working with.
        
            
toolName: view_folder
            
status: success
          
/home/lebei/dev/claws/nanobot
          
Let me examine the README.md file to get an overview of the project.
        
            
toolName: view_files
            
status: success
          
            
filePath: /home/lebei/dev/claws/nanobot/README.md
          
Let me examine the core agent files to understand the main functionality.
        
            
toolName: view_files
            
status: success
          
            
filePath: /home/lebei/dev/claws/nanobot/nanobot/agent/loop.py
          
# nanobot: Ultra-Lightweight Personal AI Assistant

## Project Overview

nanobot is an ultra-lightweight personal AI assistant designed to be highly modular, efficient, and easy to extend. It delivers core agent functionality with only ~4,000 lines of core code, making it 99% smaller than similar projects like OpenClaw.

## Architecture

nanobot follows a modular architecture with clear separation of concerns:

1. **Core Agent** (`nanobot/agent/`): The brain of the system that processes messages, builds context, calls LLMs, and executes tools.
2. **Channels** (`nanobot/channels/`): Integrations with various chat platforms (Telegram, Discord, WhatsApp, etc.).
3. **Providers** (`nanobot/providers/`): Interfaces to different LLM providers (OpenAI, Anthropic, Azure OpenAI, etc.).
4. **Tools** (`nanobot/agent/tools/`): Built-in tools for file operations, shell commands, web search, etc.
5. **Memory** (`nanobot/agent/memory.py`): Persistent memory system for storing and retrieving information.
6. **Message Bus** (`nanobot/bus/`): Handles message routing between components.
7. **Cron** (`nanobot/cron/`): Scheduled task management.
8. **Heartbeat** (`nanobot/heartbeat/`): Proactive task execution system.

## Key Features

### 1. Ultra-Lightweight Design
- Only ~4,000 lines of core agent code
- Minimal dependencies
- Fast startup and low resource usage

### 2. Multi-Platform Support
- **Chat Channels**: Telegram, Discord, WhatsApp, Feishu, QQ, DingTalk, Slack, Email, Matrix, Wecom
- **LLM Providers**: OpenAI, Anthropic, Azure OpenAI, DeepSeek, Groq, Gemini, MiniMax, and many more
- **Local LLMs**: Ollama and vLLM support

### 3. Extensible Tool System
- **Built-in Tools**: File operations, shell execution, web search, message sending, subagent spawning, cron management
- **MCP Support**: Model Context Protocol integration for external tools
- **Skill System**: Reusable skills like GitHub, weather, tmux, etc.

### 4. Advanced Features
- **Persistent Memory**: Consolidates and stores important information
- **Scheduled Tasks**: Cron-based task scheduling
- **Heartbeat System**: Proactive task execution every 30 minutes
- **Multi-Instance Support**: Run multiple nanobot instances with separate configs
- **Docker Support**: Containerized deployment
- **Linux Service**: Systemd integration for automatic startup

### 5. Security Features
- **Workspace Restriction**: Sandbox agent tools to specific directories
- **Access Control**: Per-channel allow lists for user access
- **Secure Configuration**: Environment variable support for API keys

## Core Workflow

1. **Message Reception**: Messages are received from chat channels via the message bus
2. **Context Building**: The agent builds context using session history, memory, and skills
3. **LLM Processing**: The LLM is called to generate a response or tool calls
4. **Tool Execution**: If tool calls are generated, they are executed
5. **Response Generation**: The final response is sent back to the user

## Configuration

nanobot uses a JSON configuration file at `~/.nanobot/config.json` with sections for:
- Providers (API keys and settings)
- Agents (model selection and parameters)
- Channels (chat platform configurations)
- Tools (MCP servers and security settings)
- Gateway (port and other settings)

## Deployment Options

1. **Local Installation**: `pip install nanobot-ai`
2. **Docker**: Using the provided Dockerfile and docker-compose.yml
3. **Linux Service**: Systemd user service for automatic startup

## Use Cases

- **24/7 Real-Time Market Analysis**: Discovery, insights, trends
- **Full-Stack Software Engineer**: Develop, deploy, scale
- **Smart Daily Routine Manager**: Schedule, automate, organize
- **Personal Knowledge Assistant**: Learn, memory, reasoning

## Project Structure

The project is organized into clear modules:

- `nanobot/agent/`: Core agent logic and tools
- `nanobot/channels/`: Chat platform integrations
- `nanobot/providers/`: LLM provider interfaces
- `nanobot/skills/`: Reusable skill modules
- `nanobot/bus/`: Message routing system
- `nanobot/cron/`: Scheduled task management
- `nanobot/heartbeat/`: Proactive task execution
- `nanobot/config/`: Configuration management
- `nanobot/session/`: Session management
- `nanobot/utils/`: Utility functions

## Conclusion

nanobot is a powerful yet lightweight AI assistant that combines the core functionality of larger systems with a minimal codebase. Its modular design makes it easy to extend and customize, while its multi-platform support allows it to integrate seamlessly with various chat platforms. Whether you're looking for a personal assistant, a development tool, or a research platform, nanobot provides a solid foundation with minimal overhead.
        