**Describe the Message Flow in the Nanobot Project**




          
# Message Flow in nanobot

The message flow in nanobot follows a structured, asynchronous process that involves multiple components working together to handle user inputs, process them through the LLM, execute tools if needed, and return responses. Below is a detailed breakdown of the entire flow:


## 1. **Message Reception**
Messages enter nanobot through **channels** (e.g., Telegram, Discord, CLI, email). Each channel integration (e.g., `TelegramChannel`, `DiscordChannel`) is responsible for:
- Receiving messages from the respective platform (e.g., via Telegram Bot API webhooks or Discord events).
- Converting platform-specific messages into a standardized `InboundMessage` object, which includes:
  - `channel`: The source channel (e.g., "telegram", "cli").
  - `sender_id`: The user/chat ID from the platform.
  - `chat_id`: The conversation ID (e.g., a Telegram chat ID or Discord channel ID).
  - `content`: The message text.
  - `media` (optional): Attachments like images or files.
  - `metadata` (optional): Additional platform-specific data.


## 2. **Message Routing via the Message Bus**
Once converted to `InboundMessage`, the message is **published** to the `MessageBus` (a central message queue). The `MessageBus` acts as a router, decoupling message producers (channels) from consumers (the agent loop).


## 3. **Agent Loop Processing**
The `AgentLoop` (core processing engine) **consumes** inbound messages from the `MessageBus` and processes them in the following steps:

### Step 3.1: Message Validation & Slash Commands
- The agent first checks for slash commands (e.g., `/new` to start a new session, `/stop` to cancel tasks, `/help` for commands).
- If a slash command is detected, it executes the corresponding action (e.g., clearing the session for `/new`).


### Step 3.2: Context Building
The `ContextBuilder` constructs a **context** for the LLM, which includes:
- **Session History**: Previous messages stored in the `SessionManager` (persistent across conversations).
- **Memory**: Consolidated information from past interactions (managed by `MemoryConsolidator`).
- **Skills**: Reusable modules (e.g., GitHub, weather) that extend the agent’s capabilities.
- **Runtime Context**: Channel-specific details (e.g., platform constraints) and media attachments.


### Step 3.3: LLM Interaction
The agent sends the built context to the configured `LLMProvider` (e.g., OpenAI, Anthropic) with:
- The context messages.
- Tool definitions (from the `ToolRegistry`).
- The selected model (e.g., `anthropic/claude-opus-4-5`).

The LLM responds with either:
- A **direct response** (text content) if no tools are needed.
- **Tool calls** if it needs to execute an action (e.g., searching the web, reading a file).


### Step 3.4: Tool Execution
If the LLM returns tool calls, the agent:
- Executes each tool via the `ToolRegistry` (e.g., `WebSearchTool`, `ExecTool`).
- Captures the tool’s output (e.g., search results, file content).
- Adds the tool result back to the context as a new message.
- Repeats the LLM interaction with the updated context until the task is complete.


## 4. **Response Generation**
Once the LLM generates a final response (no more tool calls), the agent:
- Cleans the response (e.g., removing `<think>` blocks).
- Saves the conversation turn to the `SessionManager` (truncating large tool results to avoid context bloat).
- Triggers memory consolidation (via `MemoryConsolidator`) to store important information for future interactions.


## 5. **Response Delivery**
The agent publishes an `OutboundMessage` to the `MessageBus`, which includes:
- `channel`: The target channel (matching the original inbound message).
- `chat_id`: The conversation ID to send the response to.
- `content`: The final response text.
- `metadata` (optional): Platform-specific data (e.g., message formatting hints).

The corresponding channel integration then:
- Receives the `OutboundMessage` from the bus.
- Converts it to the platform’s native format (e.g., Telegram message objects).
- Sends it to the user via the platform’s API.


## Special Cases

### System Messages
- **System messages** (e.g., cron jobs or heartbeat tasks) are processed similarly but use a special `channel: "system"` tag. They bypass some user-specific logic and deliver results to the most recently active chat channel.

### MCP Tools
- **MCP (Model Context Protocol)** tools are connected lazily on the first message. They extend the agent’s capabilities by integrating external tool servers (e.g., filesystem access, custom APIs).


## Key Components Involved
- **MessageBus**: Central message queue for routing `InboundMessage` and `OutboundMessage`.
- **AgentLoop**: Core processing engine that orchestrates the entire flow.
- **ContextBuilder**: Constructs LLM context from session history, memory, and skills.
- **LLMProvider**: Interface to LLMs (e.g., OpenAI, Anthropic).
- **ToolRegistry**: Manages built-in and MCP tools for execution.
- **SessionManager**: Stores conversation history and session state.
- **MemoryConsolidator**: Handles long-term memory storage and retrieval.
- **Channels**: Platform-specific integrations (e.g., Telegram, Discord) for message reception/delivery.


This flow ensures that nanobot efficiently processes user inputs, leverages tools when needed, and delivers contextually relevant responses across multiple platforms.
