# VS Code and GitHub Copilot Setup Guide

This repository helps you set up Visual Studio Code and GitHub Copilot for better coding and documentation. Everything here is designed to make your development work easier and more productive.

## What This Repository Contains

This setup includes configuration files, helpful prompts, and detailed instructions to get the most out of GitHub Copilot in VS Code. Whether you're new to coding or an experienced developer, these files will help you work more efficiently.

## Folder Structure and Locations

### `.github/` Folder
**Location**: `.github/` (in the main directory)  
**Purpose**: Contains all AI and Copilot-related configuration files

This folder has three main subfolders:

- **`chatmodes/`** - Custom chat modes for different types of work
- **`prompts/`** - Ready-to-use prompts for common tasks  
- **`copilot-instructions.md`** - Global rules that Copilot follows in every conversation

### `.vscode/` Folder
**Location**: `.vscode/` (in the main directory)  
**Purpose**: VS Code workspace settings for this specific project

### Root Files
**Location**: Main directory  
**Purpose**: Core documentation and settings files

## Complete File Reference

| File Name | Purpose | When You Need It | Required/Optional |
|-----------|---------|------------------|-------------------|
| **README.md** | Main documentation and setup guide | First time setup and reference | **Required** |
| **EXPLANATIONS.md** | Detailed technical explanations of how everything works | When you want to understand the technical details | Optional |
| **recommended-settings.json** | VS Code settings to copy into your own settings | Setting up VS Code for optimal Copilot use | **Required** |
| **review-1.md** | Review document with recommendations and fixes | For maintainers and advanced users | Optional |
| **.github/copilot-instructions.md** | Global rules and guidelines for Copilot | Always active - sets coding standards | **Required** |
| **.github/chatmodes/beastmode.chatmode.md** | Powerful autonomous mode for complex tasks | When working on complicated coding problems | Optional |
| **.github/prompts/improve-prompt.prompt.md** | Helps you write better prompts | When you want to improve your prompts | Optional |
| **.github/prompts/business-case.prompt.md** | Template for writing business case documents | When creating business proposals or cases | Optional |
| **.github/prompts/commit-message-instructions.md** | Rules for writing good commit messages | When you want consistent, clear commit messages | Optional |
| **.vscode/settings.json** | VS Code settings for this workspace only | Automatically used when you open this project | **Required** |

## File Types Explained

### Required Files
These files are essential for the basic setup to work:
- **README.md** - You're reading it now! Contains all the important setup info
- **recommended-settings.json** - Copy these settings to make Copilot work better
- **.github/copilot-instructions.md** - Basic rules that improve Copilot's responses
- **.vscode/settings.json** - Makes VS Code work properly with this project

### Optional Files
These files add extra features but aren't needed for basic use:

**For Advanced Users:**
- **EXPLANATIONS.md** - Deep technical details about how everything works
- **review-1.md** - Technical review and improvement suggestions

**For Productivity:**
- **Beast Mode** (`.github/chatmodes/beastmode.chatmode.md`) - An AI mode that works independently on complex problems
- **Prompt Files** (`.github/prompts/*.prompt.md`) - Ready-made prompts for common tasks like improving writing or creating business cases

## How to Get Started

### Step 1: Copy VS Code Settings
1. Open the `recommended-settings.json` file
2. Copy all the text inside it
3. In VS Code, open your settings (File → Preferences → Settings)
4. Click the "Open Settings (JSON)" button (top right corner)
5. Paste the copied settings into your settings file

### Step 2: Customize the Instructions
1. Open `.github/copilot-instructions.md`
2. Read through the current rules
3. Change them to match your coding style and preferences

### Step 3: Try the Prompts (Optional)
1. In VS Code, open Copilot Chat
2. Type `/improve-prompt` to use the prompt improvement tool
3. Type `/business-case` to create business case documents

### Step 4: Learn More (Optional)
- Read `EXPLANATIONS.md` for detailed technical information
- Try "Beast Mode" for complex coding tasks

## Project Types and Relevant Files

### For Any Project
- README.md (this file)
- recommended-settings.json
- .github/copilot-instructions.md
- .vscode/settings.json

### For Python Projects
- All basic files above
- Modify .github/copilot-instructions.md to include Python-specific rules

### For Documentation Projects
- All basic files above
- .github/prompts/business-case.prompt.md
- .github/prompts/improve-prompt.prompt.md

### For Complex Coding Projects
- All basic files above
- .github/chatmodes/beastmode.chatmode.md

## Getting Help

1. **Start Here**: Read this README completely
2. **Need More Details**: Check EXPLANATIONS.md
3. **Want Examples**: Look at the files in .github/prompts/
4. **Having Problems**: Check that you copied the settings correctly from recommended-settings.json

## Contributing

This setup works best when customized for your needs. Feel free to:
- Modify the instruction files to match your coding style
- Add new prompt files for tasks you do often
- Share improvements with others

The goal is to make your development work easier and more productive!
