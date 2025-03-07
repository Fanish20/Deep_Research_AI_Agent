# Deep Research AI Agentic System

## Overview
This project implements a research and answer-drafting system using Tavily API for web searches and LangGraph for workflow management. The system consists of two agents:
1. **Research Agent**: Queries Tavily API to fetch relevant search results.
2. **Answer Drafter Agent**: Processes the search results and generates a structured response.

## Features
- Uses Tavily API to perform web searches.
- Extracts and formats relevant search results.
- Drafts a well-structured response based on search findings.
- Utilizes LangGraph to define a structured workflow.

## Prerequisites
Ensure you have Python installed along with the necessary dependencies:

```bash
pip install tavily langgraph langchain requests
```

## Setup
1. **Obtain Tavily API Key**
   - Sign up at [Tavily](https://www.tavily.com) and get an API key.
   - Replace `API_KEY` in the script with your actual key.

2. **Run the script**
   ```bash
   Design_a_Deep_Research_AI_Agentic_System.ipynb
   ```

## Code Explanation
### Import Required Libraries
```python
from tavily import TavilyClient
from langgraph.graph import Graph
from langchain.prompts import PromptTemplate
from requests.exceptions import HTTPError
```

### Initialize Tavily Client
```python
API_KEY = "your-api-key-here"
tavily = TavilyClient(api_key=API_KEY)
```

### Research Agent
- Takes user query and fetches top 5 search results from Tavily API.
- Handles API errors gracefully.

```python
def research_agent(inputs):
    try:
        query = inputs["query"]
        response = tavily.search(query=query, max_results=5, search_depth="basic")
        return {"data": response.get('results', [])}
    except HTTPError as e:
        return {"data": []}
```

### Answer Drafter Agent
- Formats search results into a readable response.

```python
def answer_drafter_agent(inputs):
    data = inputs["data"]
    if not data:
        return {"answer": "No data available to draft an answer."}
    answer = "Here is the information I found:\n\n"
    for i, result in enumerate(data, start=1):
        answer += f"{i}. **{result['title']}**\n   - URL: {result['url']}\n   - Summary: {result['content']}\n\n"
    return {"answer": answer}
```

### Workflow Setup
- Uses LangGraph to define workflow steps.
```python
workflow = Graph()
workflow.add_node("research", research_agent)
workflow.add_node("draft", answer_drafter_agent)
workflow.add_edge("research", "draft")
workflow.set_entry_point("research")
workflow.set_finish_point("draft")
compiled_workflow = workflow.compile()
```

### Running the Workflow
```python
def run_workflow(query):
    return compiled_workflow.invoke({"query": query})

if __name__ == "__main__":
    query = "full form of USA"
    final_answer = run_workflow(query)
    print(final_answer["answer"])
```

## Usage Example
Run the script and provide a search query:
```bash
Design_a_Deep_Research_AI_Agentic_System.ipynb
```
Output Example:
```
Here is the information I found:
1. **United States of America**
   - URL: https://example.com/usa
   - Summary: The United States of America (USA) is a country in North America...
```



## Contact
For any questions or contributions, feel free to reach out or create a pull request!