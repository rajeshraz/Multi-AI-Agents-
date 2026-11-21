# Multi-AI Agents using LangGraph

This notebook demonstrates three AI agent architectures using **Groq**, **LangGraph**, and **LangChain**.

## 1. Simple Multi-Agent Architecture

### Purpose

Build a basic agent workflow that can search for information and write a summary.

### Agents

- **Researcher Agent:** Searches the web using Tavily and collects information.
- **Writer Agent:** Converts the search results into a short summary.

### Flow

```text
User Input
    |
    v
Researcher Agent
    |
    v
Search the web
    |
    v
Writer Agent
    |
    v
Summary Output
```

### Main Concepts

1. Import Groq and LangGraph packages.
2. Load API keys from the `.env` file.
3. Initialize the Groq LLM.
4. Define `search_web` and `write_summary` tools.
5. Define a shared agent state.
6. Create the researcher and writer nodes.
7. Add routing logic.
8. Compile the workflow as a LangGraph graph.
9. Invoke the graph with a user question.

---

## 2. Supervisor Multi-Agent Architecture

### Purpose

Use a supervisor to coordinate specialist agents and create a complete report.

### Agents

- **Supervisor:** Decides which specialist should work next.
- **Researcher:** Finds information about the topic.
- **Analyst:** Analyzes the research and identifies insights and risks.
- **Writer:** Creates an executive report.

### Flow

```text
User Input
    |
    v
Supervisor
    |
    +--> Researcher --> Supervisor
    |
    +--> Analyst --> Supervisor
    |
    +--> Writer --> Supervisor
    |
    v
Final Report
```

### Main Concepts

1. Define `SupervisorState` to store research, analysis, report, and task status.
2. Create a supervisor prompt that can select:
   - `researcher`
   - `analyst`
   - `writer`
   - `done`
3. Create separate agent functions for research, analysis, and writing.
4. Store each agent's result in the shared state.
5. Use `router()` to decide the next node and prevent unnecessary loops.
6. Compile all nodes in one LangGraph workflow.
7. Print `response["final_report"]` to show the actual report.

---

## 3. Hierarchical Multi-Agent Architecture

### Purpose

Organize specialist agents under team leaders and a CEO.

### Agent Hierarchy

```text
CEO
 |
 +--> Research Team Leader
 |      |
 |      +--> Data Researcher
 |      |
 |      +--> Market Researcher
 |
 +--> Writing Team Leader
        |
        +--> Technical Writer
        |
        +--> Summary Writer
```

### Agent Roles

- **CEO:** Assigns work to the research or writing team and finishes the task.
- **Research Team Leader:** Divides research work and combines the findings.
- **Data Researcher:** Finds facts, statistics, evidence, and examples.
- **Market Researcher:** Studies market trends, opportunities, and risks.
- **Writing Team Leader:** Coordinates the writing tasks and combines reports.
- **Technical Writer:** Writes the detailed technical report.
- **Summary Writer:** Writes the executive summary.

### Flow

```text
User Input
    |
    v
CEO
    |
    v
Research Team Leader
    |
    +--> Data Researcher
    |
    +--> Market Researcher
    |
    v
Combined Research
    |
    v
Writing Team Leader
    |
    +--> Technical Writer
    |
    +--> Summary Writer
    |
    v
Combined Final Report
    |
    v
CEO
    |
    v
END
```

### Main Concepts

1. Define `HierarchicalState` to store team results and the next node.
2. Define separate decision agents for the CEO and team leaders.
3. Specialists return their output to the leader that manages them.
4. Team leaders combine specialist output and return it to the CEO.
5. Use separate routers for the CEO, research team, and writing team.
6. Use `next_node` and completion variables to prevent routing loops.
7. Compile all hierarchy nodes using `StateGraph`.
8. Invoke the graph with the user's task.
9. Print `hierarchical_response["final_output"]`.

## Routing and State

Each architecture uses a shared state to pass information between agents.

The router reads values such as `next_node`, `next_agent`, or `task_complete`, then selects the next graph node.

```text
Agent completes work
    |
    v
Agent updates shared state
    |
    v
Router reads the state
    |
    v
Next node is selected
```

## Setup

1. Create a virtual environment.
2. Install required packages.
3. Create a `.env` file.
4. Add the Groq API key:

```text
GROQ_API_KEY=your_api_key
TAVILY_API_KEY=your_api_key
```

5. Open `multiaiagent.ipynb`.
6. Run the cells from top to bottom.
