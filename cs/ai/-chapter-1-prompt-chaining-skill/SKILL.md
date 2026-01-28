```yaml
---
name: prompt-chaining-implementation
description: Implement multi-step LLM workflows by breaking complex tasks into sequential prompts. Triggers when user needs to process complex information, generate structured outputs, build conversational agents, or create multi-step reasoning systems.
---

# Prompt Chaining Implementation Guide

## Core Instructions

When faced with complex tasks that require multiple processing steps, implement prompt chaining by:

1. **Decompose the task** into sequential, focused sub-tasks
2. **Design specific prompts** for each step with clear objectives
3. **Pass outputs as inputs** to subsequent prompts using structured formats
4. **Use frameworks like LangChain** for reliable chain execution
5. **Validate intermediate outputs** before proceeding to next steps

Always use structured output formats (JSON/XML) between steps to ensure data integrity. Each prompt should have a specific role (e.g., "Market Analyst", "Technical Writer") to focus the LLM's behavior.

## Key Concepts

1. **Sequential Decomposition**: Break complex problems into smaller, manageable steps processed in order
2. **Structured Output**: Use JSON/XML formats between steps for reliable data passing
3. **Context Preservation**: Maintain state by passing relevant information through the chain
4. **Modular Design**: Each prompt focuses on one specific operation
5. **External Tool Integration**: Insert API calls, calculations, or validations between LLM steps

## Best Practices

1. **Start with clear task analysis**: Map out all required processing steps before writing prompts
2. **Use JSON for intermediate data**: Ensure machine-readable format between steps
   ```json
   {
     "trends": [
       {
         "trend_name": "AI-Powered Personalization",
         "supporting_data": "73% of consumers prefer..."
       }
     ]
   }
   ```
3. **Assign specific roles**: Give each prompt a clear persona (e.g., "Data Extractor", "Content Writer")
4. **Validate before proceeding**: Check output quality at each step before continuing
5. **Keep prompts focused**: Each prompt should address only one sub-task

## Examples

### Basic LangChain Implementation
```python
from langchain_openai import ChatOpenAI
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.output_parsers import StrOutputParser

llm = ChatOpenAI(temperature=0)

# Step 1: Extract information
prompt_extract = ChatPromptTemplate.from_template(
    "Extract technical specifications from: {text_input}"
)

# Step 2: Transform to JSON
prompt_transform = ChatPromptTemplate.from_template(
    "Transform specifications into JSON with 'cpu', 'memory', 'storage': {specifications}"
)

# Build the chain
extraction_chain = prompt_extract | llm | StrOutputParser()
full_chain = (
    {"specifications": extraction_chain}
    | prompt_transform
    | llm
    | StrOutputParser()
)

# Execute
input_text = "Laptop: 3.5 GHz octa-core, 16GB RAM, 1TB NVMe SSD"
result = full_chain.invoke({"text_input": input_text})
print(result)  # JSON output
```

### Three-Step Workflow Example
1. **Market Analysis**: "Analyze this report and identify key trends with supporting data"
2. **Data Structuring**: "Format the trends as JSON with trend_name and supporting_data fields"
3. **Email Composition**: "Draft a concise email to marketing team outlining: [JSON output from step 2]"

## Common Pitfalls

1. **Unstructured intermediate outputs**: Natural language between steps causes parsing errors
2. **Overly complex single prompts**: Trying to do too much in one step reduces reliability
3. **Missing validation**: Not checking output quality before proceeding to next step
4. **Context loss**: Failing to pass necessary information through the chain
5. **Ignoring external tools**: Not using calculators/APIs for tasks LLMs handle poorly (like math)