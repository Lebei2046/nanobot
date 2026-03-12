**Describe memory system in the project and how it works​**




          
Let me examine the memory system implementation in nanobot.
        
            
toolName: view_files
            
status: success
          
            
filePath: /home/lebei/dev/claws/nanobot/nanobot/agent/memory.py
          
# Memory System in nanobot

nanobot's memory system is a sophisticated, two-layered approach to persistent agent memory that balances long-term knowledge storage with efficient context management. Here's a detailed breakdown of how it works:


## 1. **Memory Architecture**

### **Two-Layer Storage**
nanobot uses two files for memory storage, both located in the `~/.nanobot/workspace/memory/` directory:

| Component | File | Purpose |
|-----------|------|---------|
| **Long-term Memory** | `MEMORY.md` | Stores consolidated facts, knowledge, and key information in markdown format |
| **History Log** | `HISTORY.md` | Maintains a grep-searchable log of events with timestamps |

### **Core Classes**
- **`MemoryStore`**: Manages the physical storage and consolidation process
- **`MemoryConsolidator`**: Handles consolidation policy, locking, and session offset updates


## 2. **How Memory Consolidation Works**

### **Trigger Conditions**
Consolidation is automatically triggered when:
- Session token count exceeds half the context window size
- The user runs the `/new` command to start a new session
- Manual consolidation is requested

### **Consolidation Process**
1. **Token Estimation**: The system estimates the current prompt token count
2. **Boundary Selection**: It identifies a user-turn boundary to determine which messages to consolidate
3. **LLM Processing**: The LLM is called to:
   - Summarize key events/decisions/topics from the selected messages
   - Update the long-term memory with new information
4. **Storage Update**: 
   - A timestamped entry is appended to `HISTORY.md`
   - `MEMORY.md` is updated with the consolidated information
5. **Session Truncation**: The consolidated messages are removed from the session history

**Code Reference**: `MemoryConsolidator.maybe_consolidate_by_tokens()` and `MemoryStore.consolidate()`


## 3. **Memory Usage in Agent Context**

### **Context Inclusion**
- **Long-term Memory**: Included in the agent's context as `## Long-term Memory`
- **Session History**: Recent messages (not yet consolidated) are included in the context
- **Skills Summary**: Available skills are included for reference

### **Progressive Loading**
- Only recent messages are kept in the session history
- Older messages are consolidated into memory files
- This keeps the context size manageable while preserving important information

**Code Reference**: `MemoryStore.get_memory_context()`


## 4. **Advanced Features**

### **Token Management**
- The system automatically estimates token counts to prevent context window overflow
- It consolidates messages when the token count approaches half the context window size
- This ensures optimal LLM performance and prevents token limit errors

### **Session-Level Locking**
- Uses weak reference locks to prevent concurrent consolidation for the same session
- Ensures consistent memory state and prevents race conditions

### **LLM-Powered Summarization**
- Leverages the same LLM used by the agent to perform intelligent consolidation
- The LLM identifies key information and updates the memory files accordingly
- This ensures that important information is preserved while irrelevant details are filtered out

**Code Reference**: `MemoryStore.consolidate()`


## 5. **Memory Files Structure**

### **MEMORY.md** (Long-term Memory)
```markdown
# Agent Memory

## Personal Information
- User's name: John
- User's interests: AI, programming, hiking

## Key Events
- 2026-03-10: User asked about Python programming best practices
- 2026-03-08: User discussed AI model architectures
```

### **HISTORY.md** (Searchable Log)
```
[2026-03-12 14:30] User asked about the memory system in nanobot
[2026-03-10 10:15] Assistant provided Python programming best practices
[2026-03-08 16:45] User asked about AI model architectures
```


## 6. **Benefits of the Memory System**

1. **Context Management**: Prevents context window overflow by consolidating old messages
2. **Knowledge Retention**: Preserves important information across sessions
3. **Searchability**: Maintains a timestamped history for easy reference
4. **Efficiency**: Only includes relevant information in the context
5. **Scalability**: Handles long conversations without performance degradation
6. **Intelligence**: Uses the LLM to intelligently summarize and extract key information


## 7. **Usage Examples**

### **Example 1: Long-Term Knowledge Retention**
1. User asks about Python programming best practices
2. Agent provides detailed information
3. The conversation is consolidated into memory
4. Later, when the user asks about Python again, the agent can reference the stored knowledge

### **Example 2: Context Management**
1. User has a long conversation about various topics
2. As the conversation grows, older messages are consolidated
3. The agent maintains a manageable context size
4. The user can still reference past topics because they're stored in memory


## Conclusion

nanobot's memory system is a sophisticated solution that balances long-term knowledge storage with efficient context management. By using a two-layered approach and leveraging the LLM for intelligent consolidation, it ensures that the agent can maintain a comprehensive knowledge base while keeping its context size optimal for performance. This design allows nanobot to handle long conversations effectively and retain important information across sessions, making it a more capable and reliable AI assistant.