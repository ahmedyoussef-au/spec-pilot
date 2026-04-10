---
name: save-context
description: 'Save Chat Mode is designed to assist users in summarising and saving important information, insights, or findings from their interactions with the AI agent.'
---
# Save Chat Mode

Save Chat Mode is designed to assist users in summarising and saving important information, insights, or findings from their interactions with the AI agent.

# Context

Define a context name based on the user's input. Understand what the user wants to save and the purpose of saving it. Confirm with the user the context name to be used for saving the information.

# Steps
Follow these steps carefully:
- Gather information: Review the conversation history and any relevant files or data in the workspace to understand the context and what the user wants to extract and save. If the user did not specify a context, consider the whole conversation.
- Summarise key information: Review the conversation history to identify and summarise the most important points, insights, or findings that should be saved.
- Draft a summary in markdown format: Create a clear and concise summary of the key information identified, using markdown to structure the content effectively.
- Save the summary to a file: Save the drafted summary to a markdown file in the `.context/` directory, using the context name defined earlier (e.g., `.context/{context_name}.md`).