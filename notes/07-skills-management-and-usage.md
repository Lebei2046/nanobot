**How skills are managed and used?​**




          
Let me examine the skills module to understand how skills are managed and used in nanobot.
        
            
toolName: view_files
            
status: success
          
            
filePath: /home/lebei/dev/claws/nanobot/nanobot/agent/skills.py
          
# Skills Management and Usage in nanobot

Skills in nanobot are modular capabilities that extend the agent's functionality, teaching it how to use specific tools or perform certain tasks. Here's a detailed breakdown of how they're managed and used:


## 1. **Skills Architecture**

### **Skill Structure**
- **Format**: Skills are markdown files (`SKILL.md`) stored in dedicated directories
- **Location**: Skills can be in two places (with priority order):
  1. **Workspace Skills**: `~/.nanobot/workspace/skills/` (highest priority)
  2. **Built-in Skills**: `nanobot/skills/` (lower priority)
- **Metadata**: Skills can include YAML frontmatter with:
  - `description`: What the skill does
  - `requires`: Dependencies (CLI tools, environment variables)
  - `always`: Whether to always load the skill

### **SkillsLoader Class**
The `SkillsLoader` class manages all skill operations:
- **Loading**: Reads skill content from files
- **Listing**: Enumerates all available skills
- **Validation**: Checks if skill requirements are met
- **Summarization**: Creates skill summaries for context

**Code Reference**: `nanobot/agent/skills.py`


## 2. **Skill Loading Process**

1. **Initialization**: The `SkillsLoader` is initialized with the workspace path
2. **Skill Discovery**: It scans both workspace and built-in directories for skills
3. **Requirement Checking**: For each skill, it verifies dependencies (CLI tools, env vars)
4. **Skill Content Loading**: Skills can be loaded in two ways:
   - **Full Content**: For skills explicitly requested by the agent
   - **Summary**: For all skills, providing names, descriptions, and locations

**Code Reference**: `SkillsLoader.load_skill()` and `SkillsLoader.build_skills_summary()`


## 3. **Skill Usage in Agent Context**

### **Progressive Loading**
- **Summary Inclusion**: A summary of all skills is included in the agent's initial context
- **Full Loading**: When the agent needs detailed information about a skill, it can use the `read_file` tool to load the full content

### **Skill Execution Flow**
1. **Context Building**: The agent includes skill summaries in its context
2. **Skill Selection**: The LLM decides which skill to use based on the task
3. **Tool Execution**: The agent uses relevant tools according to the skill's instructions
4. **Result Processing**: The agent processes the tool results and generates a response

**Code Reference**: `ContextBuilder.build_messages()` (which includes skills)


## 4. **Built-in Skills**

nanobot comes with several built-in skills:

| Skill | Description | Use Case |
|-------|-------------|----------|
| **clawhub** | Search and install public agent skills | Skill discovery |
| **cron** | Manage scheduled tasks | Task automation |
| **github** | Interact with GitHub repositories | Code management |
| **memory** | Manage and recall memories | Information retrieval |
| **skill-creator** | Create new skills | Skill development |
| **summarize** | Summarize content | Information processing |
| **tmux** | Manage tmux sessions | Terminal management |
| **weather** | Get weather forecasts | Weather information |


## 5. **Creating Custom Skills**

To create a custom skill:
1. **Create a directory** in `~/.nanobot/workspace/skills/` (e.g., `my_skill/`)
2. **Create a `SKILL.md` file** with:
   - YAML frontmatter (description, requirements)
   - Markdown content explaining how to use the skill
3. **Add instructions** for the agent on when and how to use the skill

**Example Skill Structure**:
```markdown
---
description: My custom skill for specific tasks
requires:
  bins: [git]
  env: []
always: false
---

# My Custom Skill

This skill teaches the agent how to perform specific tasks...

## Usage
When the user asks about X, use this approach...
```


## 6. **Skill Requirements**

Skills can specify dependencies:
- **CLI Tools**: Required command-line utilities (e.g., `git`, `curl`)
- **Environment Variables**: Required environment variables (e.g., API keys)

If requirements aren't met, the skill is marked as unavailable.


## 7. **Skill Prioritization**

- **Workspace Skills** override built-in skills with the same name
- **"Always" Skills** are automatically loaded in the context
- **Available Skills** are those with all requirements met


## Conclusion

Skills in nanobot provide a flexible way to extend the agent's capabilities without modifying core code. They're easy to create, share, and manage, allowing users to customize their agent's behavior for specific tasks or domains. The progressive loading approach ensures that the agent only loads detailed skill information when needed, optimizing context size and performance.
