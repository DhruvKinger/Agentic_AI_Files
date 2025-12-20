# Exercise: Implementing Multi-Agent Architecture - Antarctic Agents

## Overview
In this exercise, you'll work with a multi-agent system where penguin agents interact with a scientist agent. The penguins can take actions (like finding food), and the scientist responds by distributing resources. You'll add a tool to the penguin agents to make them more autonomous.

## Prerequisites
- Basic Python programming skills
- Familiarity with object-oriented programming (classes, methods, instances)
- Understanding of the `smolagents` library and tool-calling agents
- OpenAI API access (provided via Udacity credentials)

## Setup
The exercise uses:
- `smolagents` library for multi-agent architecture
- OpenAI GPT-4o-mini model for agent decision-making
- `@tool` decorator to define tools that agents can use
- `ToolCallingAgent` base class for creating AI-powered agents

## File Structure
- **starter.py** - Your starting point with incomplete code
- **solution.py** - Complete working solution for reference
- **.env** - Contains your API credentials (UDACITY_OPENAI_API_KEY)

## The Architecture

### Agents:
1. **ScientistAgent** - Manages food supply and tools, responds to penguin actions
2. **PenguinAgent** - Takes actions each round, can find food and request resources

### Tools:
- `check_history()` - Check a penguin's resource distribution history
- `record_distribution()` - Record resource distribution events
- `find_food()` - Allows penguins to find food using different methods

## Your Task

### Step 1: Add the find_food tool to PenguinAgent
In `starter.py`, locate the `PenguinAgent` class `__init__` method:

```python
class PenguinAgent(ToolCallingAgent):
    def __init__(self, name: str) -> None:
        # YOUR CODE HERE - add the find_food tool to the tools list
        super().__init__(tools=[], model=model, name=name)
```

**TODO:** Add the `find_food` tool to the tools list. The tool is already defined at the top of the file.

**Hint:** Look at how `ScientistAgent` adds tools: `tools=[check_history, record_distribution]`

### Step 2: Modify the prompt (Optional Enhancement)
In the `take_action()` method, you can enhance the prompt to guide the penguin's decision-making:

```python
prompt = f"""You are Penguin {self.name}.
You have {self.food} food.
What do you want to do? Respond with JSON:  {{'action': '<action_string>', 'method': '<method_string>'}}"""
```

**Optional:** Modify this prompt to encourage penguins to use the `find_food` tool when they have low food.

## Running the Simulation

1. Make sure you have a `.env` file with your `UDACITY_OPENAI_API_KEY`
2. Open a terminal in your workspace
3. Run the script:
   ```bash
   python starter.py
   ```

## Expected Behavior

When running successfully, you should see:
- Penguins taking actions each round
- Some penguins using the `find_food` tool with different methods (fishing vs foraging)
- The scientist responding to penguin actions by distributing food and tools
- Resource tracking showing food supply changes over time
- Periodic resource refreshes for the scientist

## Key Concepts

### Tools in Multi-Agent Systems
Tools are functions that agents can call to perform actions or gather information. The `@tool` decorator makes functions available to agents.

### Agent Decision-Making
Each agent uses the LLM (GPT-4o-mini) to decide what action to take based on:
- Its current state (food level, tools)
- Available tools
- The prompt/instructions it receives

### Multi-Agent Interaction
The simulation shows how multiple agents (penguins and scientist) interact:
- Penguins decide on actions autonomously
- The scientist observes and responds to each penguin's actions
- Resources are tracked and distributed based on history and need

## Troubleshooting

**Error: "YOUR NEW TOOL HERE"**
- This means you haven't replaced the placeholder with the actual tool name
- Replace `"YOUR NEW TOOL HERE"` with `find_food`

**Penguins not finding food:**
- Check that `find_food` is in the tools list
- Verify the action processing logic in `run_simulation()`

**API errors:**
- Verify your `.env` file has the correct `UDACITY_OPENAI_API_KEY`
- Check that the API base URL is correct: `https://openai.vocareum.com/v1`

## Extension Challenges

Once you have the basic exercise working:
1. Create a new tool for penguins (e.g., `share_food`, `build_shelter`)
2. Modify the scientist's decision-making logic
3. Add more sophisticated prompting to guide agent behavior
4. Track and visualize resource distribution over time

## Success Criteria

Your solution is complete when:
- ✅ The script runs without errors
- ✅ Penguins can use the `find_food` tool
- ✅ The simulation completes all 3 rounds
- ✅ You see output showing food being found and distributed
- ✅ The final state shows total food collected by each penguin
