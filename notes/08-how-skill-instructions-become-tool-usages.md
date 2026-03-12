**How skill instructions become tool usages?​**




          
# How Skill Instructions Become Tool Usages

## The Process Flow

### 1. Skill Discovery and Loading
1. **Initial System Prompt**: The `ContextBuilder` includes a skills summary in the system prompt, listing all available skills with their descriptions and availability.
2. **Skill Identification**: The agent identifies relevant skills based on the user's request.
3. **Skill Loading**: The agent uses the `read_file` tool to load the full `SKILL.md` content for the relevant skill(s).

### 2. Instruction Processing
1. **Skill Content Analysis**: The LLM processes the skill instructions, which typically include:
   - Step-by-step procedures
   - Tool usage examples
   - Best practices for task completion
   - Expected input/output formats

2. **Reasoning and Planning**: The LLM uses the skill instructions to formulate a plan, determining:
   - Which tools to use
   - What parameters to provide
   - The sequence of tool calls

### 3. Tool Call Generation
1. **Tool Call Construction**: The LLM generates tool calls based on the skill instructions. For example, a `git` skill might instruct:
   ```markdown
   To clone a repository:
   1. Use the `exec` tool
   2. Set `command` to `git clone <repository-url>`
   3. Set `blocking` to `true`
   ```
   The LLM would then generate:
   ```python
   {
       "tool_calls": [
           {
               "id": "1",
               "type": "function",
               "function": {
                   "name": "exec",
                   "arguments": "{\"command\": \"git clone https://github.com/example/repo.git\", \"blocking\": true}"
               }
           }
       ]
   }
   ```

2. **Tool Execution**: The agent executes the tool calls via the `ToolRegistry.execute()` method.

3. **Result Processing**: The agent processes the tool results and determines the next steps, referring back to the skill instructions as needed.

## Key Mechanisms

### 1. Context Integration
- **Skills as Context**: Skill instructions become part of the agent's context, allowing the LLM to reference them during reasoning.
- **Progressive Loading**: The agent loads only the relevant skills, keeping the context focused and efficient.

### 2. LLM Reasoning
- **Instruction Interpretation**: The LLM interprets skill instructions and maps them to specific tool calls.
- **Parameter Extraction**: The LLM extracts necessary parameters from the instructions and user input.
- **Sequence Planning**: The LLM determines the optimal sequence of tool calls based on the skill instructions.

### 3. Tool Execution Pipeline
- **Tool Registry**: The `ToolRegistry` manages all available tools and handles execution.
- **Error Handling**: The agent handles tool execution errors and adapts based on skill instructions.
- **Result Integration**: Tool results are integrated back into the context for further processing.

## Example: Git Skill Usage

#### Skill Instructions (`SKILL.md`)
```markdown
# Git Skill

This skill helps you work with Git repositories.

## Cloning a Repository
1. Use the `exec` tool with the command `git clone <repository-url>`
2. Set `blocking` to `true` to wait for completion
3. Check the output for success or error messages

## Checking Status
1. Use the `exec` tool with the command `git status`
2. Set `blocking` to `true`
3. Review the output to see modified files
```

#### Agent Processing
1. **User Request**: "Clone the nanobot repository from GitHub"
2. **Skill Identification**: Agent identifies the `git` skill from the skills summary
3. **Skill Loading**: Agent uses `read_file` to load `skills/git/SKILL.md`
4. **Instruction Processing**: LLM reads the cloning instructions
5. **Tool Call Generation**: LLM generates an `exec` tool call with `command: "git clone https://github.com/user/nanobot.git"`
6. **Tool Execution**: Agent executes the git clone command
7. **Result Handling**: Agent processes the output and reports success/failure

## Skill-to-Tool Mapping Examples

| Skill Instruction | Tool Call Generated |
|-------------------|---------------------|
| "Use `read_file` to examine the config file" | `read_file(path: "config.json")` |
| "Run `ls -la` to list directory contents" | `exec(command: "ls -la", blocking: true)` |
| "Search for 'error' in the logs" | `exec(command: "grep error logs.txt", blocking: true)` |
| "Write a new README file" | `write_file(path: "README.md", content: "# Project\n...")` |
| "Fetch weather data for New York" | `web_search(query: "current weather New York")` |

## Conclusion

Skill instructions become tool usages through a process of:
1. **Discovery**: Agent identifies relevant skills
2. **Loading**: Agent reads the full skill instructions
3. **Interpretation**: LLM processes the instructions and maps them to tool calls
4. **Execution**: Agent executes the generated tool calls
5. **Adaptation**: Agent adjusts based on tool results and further skill instructions

This system allows Nanobot to leverage structured knowledge (skills) to effectively use its tools, creating a powerful and flexible task completion mechanism.
