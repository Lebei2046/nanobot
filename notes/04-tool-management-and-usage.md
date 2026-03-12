**How the tools are managed and used?**




          
Let me examine the tool registry and related files to understand how tools are managed and used in nanobot.
        
            
toolName: view_files
            
status: success
          
            
filePath: /home/lebei/dev/claws/nanobot/nanobot/agent/tools/registry.py
          
Let me examine the base tool class to understand how tools are implemented.
        
            
toolName: view_files
            
status: success
          
            
filePath: /home/lebei/dev/claws/nanobot/nanobot/agent/tools/base.py
          
# Tool Management and Usage in nanobot

nanobot has a sophisticated tool management system that allows the agent to interact with its environment, perform tasks, and extend its capabilities. Here's a detailed breakdown of how tools are structured, managed, and used:


## Tool Architecture

### 1. **Tool Base Class** (`Tool`)
All tools in nanobot inherit from the abstract `Tool` class, which defines the core structure:
- **Name**: Unique identifier for the tool (e.g., "read_file")
- **Description**: Human-readable explanation of what the tool does
- **Parameters**: JSON Schema defining the tool's input parameters
- **Execute Method**: Core functionality that runs when the tool is called

The base class also provides:
- **Parameter Casting**: Automatically converts input types to match schema requirements
- **Parameter Validation**: Ensures inputs meet schema constraints
- **Schema Conversion**: Transforms tool definitions to OpenAI's function call format


### 2. **Tool Registry** (`ToolRegistry`)
The `ToolRegistry` acts as a central manager for all tools:
- **Registration**: Adds tools to the registry by name
- **Retrieval**: Gets tools by name for execution
- **Schema Generation**: Creates tool definitions for the LLM
- **Execution**: Safely runs tools with validated parameters
- **Error Handling**: Provides user-friendly error messages for tool failures


## Default Tools

nanobot comes with a set of built-in tools registered by default:

| Tool | Description | Use Case |
|------|-------------|----------|
| **ReadFileTool** | Reads content from files | Accessing local files |
| **WriteFileTool** | Writes content to files | Creating or overwriting files |
| **EditFileTool** | Edits specific parts of files | Modifying existing files |
| **ListDirTool** | Lists directory contents | Exploring the filesystem |
| **ExecTool** | Executes shell commands | Running system commands |
| **WebSearchTool** | Searches the web using Brave API | Finding information online |
| **WebFetchTool** | Fetches content from URLs | Reading web pages |
| **MessageTool** | Sends messages to users | Communicating with users |
| **SpawnTool** | Creates subagents for background tasks | Parallel processing |
| **CronTool** | Manages scheduled tasks | Setting up recurring actions |


## MCP (Model Context Protocol) Tools

nanobot supports external tool servers via MCP, which:
- **Extends Capabilities**: Adds specialized tools without modifying core code
- **Two Transport Modes**: Local (stdio) or remote (HTTP)
- **Automatic Discovery**: Tools are automatically registered on startup
- **Dynamic Connection**: MCP servers connect lazily on first use


## Tool Usage Flow

1. **Context Building**: The agent builds a context that includes tool definitions
2. **LLM Interaction**: The LLM receives tool definitions and may generate tool calls
3. **Tool Execution**: 
   - AgentLoop extracts tool calls from LLM response
   - ToolRegistry validates and executes each tool
   - Tool results are added back to the context
4. **Iteration**: The process repeats until the LLM generates a final response


## Tool Safety and Security

- **Parameter Validation**: All tool inputs are validated against JSON schemas
- **Workspace Restriction**: Tools can be sandboxed to specific directories
- **Error Handling**: User-friendly error messages prevent task failures
- **Timeout Control**: Tools have configurable timeouts to prevent hanging
- **Access Control**: Channel-specific allow lists restrict who can use tools


## Extending Tools

To add a new tool to nanobot:

1. **Create a Tool Class**: Inherit from `Tool` and implement required methods
2. **Define Schema**: Create a JSON Schema for parameters
3. **Register the Tool**: Add it to the ToolRegistry during initialization
4. **Update Documentation**: Ensure the tool's purpose is clear to the LLM


## Example Tool Definition

```python
class MyCustomTool(Tool):
    @property
    def name(self) -> str:
        return "my_custom_tool"
    
    @property
    def description(self) -> str:
        return "Does something useful with input"
    
    @property
    def parameters(self) -> dict:
        return {
            "type": "object",
            "properties": {
                "input": {
                    "type": "string",
                    "description": "Input for the tool"
                }
            },
            "required": ["input"]
        }
    
    async def execute(self, input: str) -> str:
        # Tool implementation
        return f"Processed: {input}"
```


## Conclusion

nanobot's tool system is designed to be:
- **Extensible**: Easy to add new tools
- **Secure**: Validated inputs and sandboxing
- **Reliable**: Comprehensive error handling
- **Flexible**: Supports both built-in and external tools
- **User-Friendly**: Clear error messages and intuitive usage

This architecture enables the agent to perform a wide range of tasks, from simple file operations to complex web searches and background processes, all while maintaining a high level of reliability and security.
        