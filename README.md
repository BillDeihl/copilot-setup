# VS Code and GitHub Copilot Setup for Prompt Engineering and Python/Markdown Productivity

This repository contains a comprehensive setup for optimizing Visual Studio Code and GitHub Copilot for prompt engineering, Python development, and Markdown-based documentation workflows.

## Features

- **Optimized VS Code Settings**: Includes a `recommended-settings.json` file with settings to enable agent mode, auto-approve tool usage, and other features to enhance Copilot's capabilities.
- **Structured `.github` folder**: A pre-configured `.github` directory to house all your AI-related configurations.
  - `copilot-instructions.md`: Global instructions for Copilot to follow across all interactions.
  - `instructions/`: A directory for more specific, scoped instructions (e.g., for different frameworks or a "memory" file).
  - `prompts/`: Contains reusable prompt files for common tasks.
    - `improve-prompt.prompt.md`: A meta-prompt to help you engineer better prompts.
    - `business-case.prompt.md`: A structured prompt for generating business case documents.
  - `chatmodes/`: Holds custom chat modes.
    - `beastmode.chatmode.md`: A powerful, autonomous chat mode for complex coding tasks.
- **Explanations File**: `EXPLANATIONS.md` provides detailed information about each component of this setup, how it works, and how to use it effectively.

## How to Use

1. **Apply VS Code Settings**: Copy the contents of `recommended-settings.json` into your user or workspace `settings.json` file.
2. **Explore the `.github` folder**:
    - Review and customize `copilot-instructions.md` with your own high-level guidelines.
    - Add your own specialized instructions to the `instructions/` directory.
    - Use the provided prompt files in the `prompts/` directory by typing `/<prompt_name>` in the Copilot Chat.
    - Activate "Beast Mode" from the chat mode selector for complex tasks.
3. **Consult the Explanations**: Refer to `EXPLANATIONS.md` for a deep dive into the concepts and for guidance on how to get the most out of this setup.

This setup is designed to be a starting point. Feel free to iterate and refine the instructions and prompts to better suit your specific needs and workflow.
