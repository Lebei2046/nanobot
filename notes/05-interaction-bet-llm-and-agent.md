**Describe the interaction between the agent and LLMs"




          
# Agent-LLM Interaction in nanobot

The interaction between nanobot’s agent and LLMs is a structured, iterative process designed to leverage the LLM’s reasoning capabilities while using tools to perform concrete actions. Here’s a detailed breakdown of how this interaction works:


## 1. **Context Preparation**
Before calling the LLM, the agent builds a comprehensive context using the `ContextBuilder`. This context includes:
- **System Prompt**: Instructions defining the agent’s role, capabilities, and constraints.
- **Session History**: Previous messages from the conversation (stored in `SessionManager`).
- **Memory**: Consolidated information from past interactions (managed by `MemoryConsolidator`).
- **Runtime Context**: Channel-specific details (e.g., platform constraints) and media attachments.
- **Tool Definitions**: Schemas of available tools (in OpenAI function call format) that the LLM can use.

**Code Reference**: The context is built in `ContextBuilder.build_messages()` (called in `AgentLoop._process_message()` at line 387-392).


## 2. **LLM Call**
The agent sends the prepared context to the configured LLM via the `LLMProvider` interface:
- **Provider Abstraction**: The `LLMProvider` (e.g., `OpenAIProvider`, `AnthropicProvider`) handles provider-specific API calls.
- **Retry Logic**: The agent uses `chat_with_retry()` to automatically retry transient errors (e.g., rate limits).
- **Model Selection**: The agent uses the model specified in the config (or the provider’s default).

**Code Reference**: The LLM is called in `AgentLoop._run_agent_loop()` at line 192-196.


## 3. **Response Processing**
The LLM’s response is processed in two possible ways:

### **3.1 Direct Response**
If the LLM generates a final answer (no tool calls):
- The agent strips any `<think>` blocks (used by some models for reasoning).
- Checks for error responses (e.g., `finish_reason == "error"`).
- Adds the response to the session history.
- Returns the response to the user.

### **3.2 Tool Calls**
If the LLM generates tool calls (to perform actions like searching the web or reading files):
- The agent extracts each tool call (name and parameters).
- Validates and executes the tools via the `ToolRegistry`.
- Captures the tool’s output (e.g., search results, file content).
- Adds the tool result back to the context as a new "tool" message.
- Repeats the LLM interaction with the updated context.

**Code Reference**: Tool calls are processed in `AgentLoop._run_agent_loop()` at line 198-222.


## 4. **Iteration**
The process repeats until:
- The LLM generates a final response (no more tool calls).
- The maximum number of iterations is reached (default: 40).
- An error occurs that cannot be resolved.

**Code Reference**: The loop runs in `AgentLoop._run_agent_loop()` at line 187-245.


## 5. **Error Handling**
The agent includes robust error handling:
- **LLM Errors**: If the LLM returns an error, the agent logs it and returns a user-friendly message.
- **Tool Errors**: If a tool fails, the agent captures the error and provides it to the LLM, which can then adjust its approach.
- **Timeout Handling**: Tools have configurable timeouts to prevent hanging.

**Code Reference**: Error handling is implemented in `ToolRegistry.execute()` at line 58-59 and `AgentLoop._run_agent_loop()` at line 227-230.


## 6. **Advanced Features**
- **Thinking Mode**: Some models support explicit reasoning via `<think>` blocks, which the agent processes.
- **Streaming**: The agent can stream LLM responses and tool hints to the user in real-time.
- **MCP Tools**: External tools from MCP servers are seamlessly integrated into the tool registry.

**Code Reference**: Streaming is handled via the `on_progress` callback in `AgentLoop._run_agent_loop()` at line 199-203.


## Example Interaction Flow
1. **User Message**: "Find the latest news about AI and summarize it."
2. **Context Building**: Agent adds user message to context with tool definitions (including `web_search`).
3. **LLM Call**: LLM receives context and decides to use `web_search` to find recent AI news.
4. **Tool Execution**: Agent runs `web_search` with query "latest AI news 2026".
5. **Result Processing**: Search results are added to the context as a tool message.
6. **LLM Call (2)**: LLM receives updated context and summarizes the news.
7. **Final Response**: Agent returns the summary to the user.


## Conclusion
The agent-LLM interaction in nanobot is a sophisticated, iterative process that combines the LLM’s reasoning capabilities with tool execution to solve complex tasks. By abstracting provider differences and handling errors gracefully, nanobot ensures a reliable and flexible interaction that adapts to different LLMs and use cases.
        