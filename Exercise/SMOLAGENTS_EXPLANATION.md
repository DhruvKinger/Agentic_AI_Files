# What is Smolagents framework?

Smolagents is a Python framework for building multi-agent systems powered by large language models (LLMs). It is designed to make it easy to create, orchestrate, and manage agents that can use tools (Python functions), maintain state, and collaborate to solve complex tasks.

Smolagents lets you build intelligent, tool-using, multi-agent systems with LLMs and Python, making it easy to combine language understanding with real-world actions and stateful workflows.

## How does it work?

1. **Define Tools**: Decorate Python functions with `@tool` to make them available to agents.
2. **Create Agents**: Subclass `ToolCallingAgent` and specify which tools the agent can use.
3. **Orchestrate**: Use agents in your application, passing prompts and letting them call tools as needed.
4. **LLM-Powered Reasoning**: The agent framework parses the LLM's output, detects tool calls, and executes the corresponding Python functions.

## What is a ToolCallingAgent?

`ToolCallingAgent` is a core class in the Smolagents framework. It represents an agent powered by a large language model (LLM) that can:

- Interpret prompts and user input
- Decide when to call Python functions ("tools")
- Execute those tools and use their results in their reasoning

### How does it work?

1. **Define tools**: Decorate Python functions with `@tool`.
2. **Create an agent**: Instantiate or subclass `ToolCallingAgent`, passing the tools and an LLM model.
3. **Interact**: Call `.run(prompt)` on the agent.

The agent will:
- Use the LLM to interpret the prompt.
- Decide if a tool should be called.
- Call the tool if needed, and use its result in the response.

## Example: Weather Agent

```python
from smolagents import ToolCallingAgent, OpenAIServerModel, tool
import json

# Initialize a language model
model = OpenAIServerModel(
    model_id="gpt-4o-mini",  # Small, efficient model
    api_key="your-api-key"
)

# Define tools that agents can use
@tool
def get_weather(city: str) -> str:
    """
    Get weather information for a city.
    
    Args:
        city (str): The name of the city.
    
    Returns:
        str: Weather description.
    """
    return f"Weather in {city}: Sunny, 75°F"

@tool
def save_data(key: str, value: str) -> str:
    """
    Save data to memory.
    
    Args:
        key (str): The key to store data under.
        value (str): The value to store.
    
    Returns:
        str: Confirmation message.
    """
    memory[key] = value
    return f"Saved: {key} = {value}"

# Global shared state
memory = {}

# Create a SmoLAgent
class WeatherAgent(ToolCallingAgent):
    def __init__(self):
        super().__init__(
            tools=[get_weather, save_data],  # Tools this agent can use
            model=model,                     # Language model
            name="weather_assistant"         # Agent identifier (must be valid Python identifier)
        )

    def ask_weather(self, city: str):
        """Agent processes a weather request."""
        response = self.run(
            f"Get weather for {city} and save it. "
            f"Respond with JSON: {{'weather': 'description', 'saved': true/false}}"
        )
        return response

# Usage
agent = WeatherAgent()
result = agent.ask_weather("New York")
print(result)  # Agent uses tools automatically to get and save weather
```

## Important Notes

### Tool Docstrings
All `@tool` decorated functions **must** have proper docstrings with `Args` and `Returns` sections. This is required for Smolagents to generate JSON schemas:

```python
@tool
def my_tool(param1: str, param2: int) -> str:
    """
    Brief description of what the tool does.
    
    Args:
        param1 (str): Description of param1.
        param2 (int): Description of param2.
    
    Returns:
        str: Description of return value.
    """
    # Implementation
    pass
```

**Without proper docstrings, you'll get `DocstringParsingException` errors.**

### Agent Names
Agent names must be **valid Python identifiers** (no spaces, no special characters except underscore):

✅ Good: `"weather_assistant"`, `"penguin_0"`, `"scientist"`  
❌ Bad: `"Weather Assistant"`, `"Penguin 0"`, `"scientist-1"`

### Multi-Agent Systems
You can create multiple agents that interact with each other:

```python
# Agent 1: Makes decisions
class DecisionAgent(ToolCallingAgent):
    def __init__(self):
        super().__init__(tools=[tool1, tool2], model=model, name="decision_maker")

# Agent 2: Responds to actions
class ResponseAgent(ToolCallingAgent):
    def __init__(self):
        super().__init__(tools=[tool3, tool4], model=model, name="responder")

# Orchestrate interaction
decision_agent = DecisionAgent()
response_agent = ResponseAgent()

# Agent 1 takes action
action = decision_agent.run("Decide what to do")

# Agent 2 responds
response = response_agent.run(f"Respond to this action: {action}")
```

This is exactly the pattern used in the Antarctic Agents exercise with `ScientistAgent` and `PenguinAgent`.

## Connection to Antarctic Agents Exercise

In the exercise, you'll see this pattern in action:
- `find_food`, `check_history`, `record_distribution` are **tools** decorated with `@tool`
- `ScientistAgent` and `PenguinAgent` are **ToolCallingAgents** with different tools
- The simulation orchestrates **multi-agent interaction** where penguins take actions and the scientist responds
- All tools have **proper docstrings** to prevent parsing errors
- Agent names use **valid identifiers** like `"scientist"` and `"penguin_0"`
