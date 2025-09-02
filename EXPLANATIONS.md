# Explanations

This file contains explanations of the various concepts and files used in this setup.

## Optimizing VS Code Copilot Chat for LLM-Powered Development

### Recommended VS Code & Copilot Settings

To get the most out of GitHub Copilot’s LLM integration in VS Code, start by updating VS Code and the Copilot extension to the latest version. Then adjust a few key settings in your `settings.json`:

- **Enable Agent Mode**: Ensure autonomous agent mode is turned on (`"chat.agent.enabled": true`) so you can use Copilot’s multi-step “Agent” in addition to Ask and Edit modes. In VS Code’s Copilot Chat pane, you should then see modes for Ask, Edit, and Agent in the dropdown. Use Ask for Q&A or guidance, Edit for code modifications on selections, and Agent for letting the AI run tools and make edits autonomously on high-level tasks.
- **Use GPT-4.1 when available**: For the best results, select the GPT-4.1 model in Copilot Chat (often labeled GitHub Copilot GPT-4 in the model picker). Copilot’s GPT-4.1 with tools is very fast and capable for complex tasks. (It’s the model “Beast Mode” is designed for.) Keep in mind GPT-4.1 requests may be limited, so use it for the hardest problems and switch to faster models (or Claude v2) for simpler queries if needed.
- **Allow Tool Usage & Iterations**: Enable automatic tool execution and increase iteration limits for the agent. In `settings.json`, set:

  ```json
  "chat.tools.autoApprove": true,
  "chat.agent.maxRequests": 100
  ```

  This approves all tool/terminal commands without prompting and lets the agent make up to 100 requests in a session. Auto-approving tools means the agent can run terminal commands or web searches on its own, so only enable this in trusted projects. The default `maxRequests` (25) can be too low for complex tasks – bumping it to 100 prevents the agent from pausing to ask for continuation on long jobs.
- **Enable Todo List Updates**: Consider turning on the experimental to-do list tool: `"chat.todoListTool.enabled": true`. This isn’t strictly required (a good prompt can manage its own checklist), but Copilot’s agent can use an integrated to-do list to track progress if enabled. In practice, the Beast Mode prompt will itself create a TODO list in markdown, even if this setting is off.
- **Ensure Prompt/Instruction Files are Enabled**: VS Code supports custom instruction files and prompt files as experimental features. Verify that `"chat.promptFiles": true` and `"github.copilot.chat.codeGeneration.useInstructionFiles": true` (should be true by default). These allow you to use `.prompt.md` files for custom prompts and automatically include `.md` files with guidelines (explained below).

With these settings in place, Copilot Chat will be in its most powerful configuration – using GPT-4.1 with agent tools enabled and your custom instructions loaded. Now you can set up your workspace to supply those custom instructions and prompts.

### Organizing the `.github` Folder for Copilot

In your project, use the `.github` folder to store all AI-related config: custom instructions, prompt files, and chat modes. A typical layout might look like:

```
.github/
├── copilot-instructions.md
├── instructions/
│   └── *additional .md files (custom guidelines, “memory”, etc.)*
├── prompts/
│   └── *your .prompt.md files for custom prompts*
└── chatmodes/
    └── *your .chatmode.md files for custom chat modes*
```

Here’s how each of these works:

- **Global Instructions (`copilot-instructions.md`)**: This Markdown file defines general guidelines that Copilot Chat will always include in every prompt (in all modes). It’s the place to put your coding standards, style preferences, or high-level project context. For example, you might specify: “Always follow PEP8 standards for Python. Use meaningful variable names and include docstrings for new functions.” Once you create a `.github/copilot-instructions.md`, Copilot automatically appends it to each query, so the LLM will adhere to those rules without you repeating them. Keep this file concise and high-level – it should read like a guide rather than a prompt. (If you have different standards for different languages or sections of the repo, you can create multiple instruction files in the `instructions/` subfolder as described next.)
- **Additional Instruction Files (`.github/instructions/`)**: You can organize more specialized instructions here (the Copilot defaults include this folder as a search location). For instance, if you have specific frameworks or a “memory” of known issues, put those in separate files. Beast Mode uses this: it can save a “memory” file for things the AI should remember not to do repeatedly. In fact, Beast Mode 3.1 will automatically create a `memory.instructions.md` in `.github/instructions/` to note anything you tell it to “remember” during a session. You might also add files like `django.instructions.md` with tips for using Django in your project, etc. Copilot will include all markdown files in `.github/instructions/` by default, or you can target specific files via glob patterns in settings if needed. Each file can be scoped to certain languages or contexts by naming or content (for example, include the word “Python” or code snippets to hint it’s Python-specific). This keeps your main global instructions file from getting too large. (💡 Note: Because every file here is injected into the prompt, don’t overload them with huge text – keep instructions concise and relevant.)
- **Reusable Prompt Files (`.github/prompts/`)**: Prompt files are for one-off on-demand prompts that you trigger manually in chat. They have a `.prompt.md` extension and contain a predefined prompt or workflow in Markdown. Use these for common tasks or complex prompt setups that you don’t want to retype. For example, you might have `improve-prompt.prompt.md` to refine a given prompt (more on this below), or `generate-unit-tests.prompt.md` to automate writing tests for selected code. By default, any prompt files you add under `.github/prompts` are recognized by Copilot Chat. In chat, you can run them by typing `/<prompt name>` – for instance, `/improve-prompt` – or selecting from the Prompt Files menu. You can also pass parameters after a colon, e.g. `/create-react-form: formName=MyForm` to feed inputs into the prompt. This triggers the LLM to execute the prompt file’s content, possibly asking you for further info if the prompt is designed that way. We’ll create some sample prompt files in the next section.
- **Custom Chat Modes (`.github/chatmodes/`)**: Chat mode files (extension `.chatmode.md`) define entire modes – essentially custom “system prompts” plus tool configurations – that appear alongside Ask/Edit/Agent in the UI. Use these for specialized multi-turn workflows or “personalities” that you want to reuse. For example, Beast Mode is distributed as a `beastmode.chatmode.md` file. Placing it in `.github/chatmodes` (or adding via Configure Chat > Modes) makes “Beast Mode” show up in your mode picker inside VS Code. A chat mode can specify which tools are available, which model to use, and a detailed system prompt defining how the AI should behave. This is the most powerful way to customize behavior, as it persists across the conversation. We will install Beast Mode as a custom mode in the next section. If your VS Code build doesn’t support the UI to add modes, you can still manually drop the file in `.github/chatmodes` (or your global user data folder) and reload VS Code – it should pick it up.

### How instructions vs prompt files differ

The instruction files (global or in the folder) are always applied automatically to every chat request. They’re best for invariant rules (coding style, project conventions, “always do X, never do Y”). Prompt files, on the other hand, are invoked when you need them (via slash command or running from the palette) – use these for specific workflows like generating boilerplate, running a multi-step refactoring, writing a doc outline, etc. This separation lets you have constant guidance in the background, while still using ad-hoc prompts for specific tasks.

## Beast Mode for Autonomous Python Development 🚀

For complex coding tasks, especially in Python, you’ll benefit from Beast Mode – an opinionated custom chat mode that supercharges Copilot’s agent. Beast Mode (v3.1 as of mid-2025) was created by VS Code’s team to address GPT-4.1’s tendency to act quickly but superficially. It layers a detailed workflow on top of the AI, instructing it to plan thoroughly, perform web research, maintain a TODO list, and keep iterating until the problem is fully solved. In other words, it turns the Copilot agent into a tireless pair programmer that won’t quit until it checks off every task. This has been proven to significantly improve the agent’s reliability and completeness on coding problems.

### How to install Beast Mode

Burke Holland (VS Code team) has provided the Beast Mode prompt as a gist. Here’s the setup (you only need to do this once per environment):

1. **Acquire the Beast Mode chat file**: Open the Beast Mode 3.1 gist (available via Burke’s tweet or GitHub). Copy the raw contents of `beastmode3.1.chatmode.md`. (It’s a long markdown file containing the system prompt and tool config.)
2. **Add it as a custom mode**: In VS Code’s Copilot Chat panel, click the mode selector (likely says “Agent” by default) and choose “Configure Modes” → “Create new custom chat mode file”. When prompted where to save, choose User Data Folder to make it available globally (or Workspace if you prefer it tied to the project). Name the mode “Beast Mode”. Paste the copied content into the new file and save.
   - **Note**: If your VS Code version doesn’t have the UI for this, you can manually create a folder `.github/chatmodes/` in your project, save the file as `beastmode.chatmode.md` inside it, and then enable that path via settings (as mentioned above). On next reload, it should register.
3. **Select Beast Mode in chat**: Now “Beast Mode” will appear in the chat modes dropdown. Activate it whenever you have a non-trivial coding task (e.g. “Implement feature X with these requirements…”). The first time, you might get a disclaimer about experimental modes – accept it to proceed.

With Beast Mode active, describe your high-level goal in natural language. For example: “Add a new API endpoint for uploading files, with size validation and unit tests.” Then sit back and watch. Beast Mode will typically respond by analyzing the request, creating a plan in the form of a TODO checklist, and then executing it step by step – writing code, running tools, Googling documentation (it uses the fetch tool to search the web), and updating the checklist as it completes items. It will only return control to you when it believes everything is done (all todos checked off). If it encounters errors or failing tests, it will iterate fixes automatically. This workflow mimics an expert developer: plan, research, implement, test, and verify.

### Why Beast Mode is effective

It aggressively addresses two weaknesses of the default agent – lack of agency (not following through on tasks) and lack of deep thinking. Beast Mode’s prompt literally tells the AI 8 different times not to give up or stop early. It also forces the model to use tools like web search (`fetch_webpage`) to get accurate, up-to-date information instead of hallucinating code. Additionally, by updating a checklist in real-time, it keeps the AI focused on finishing every item. All of this leads to more reliable outcomes, as many users (and Microsoft’s own team) have reported. In short, it “turns your agent into a beast” that can implement features autonomously.

### Python-specific considerations

Beast Mode works for any coding task (language-agnostic), but you can augment it with Python-centric guidance. For instance, in your `copilot-instructions.md` or a `python.instructions.md`, note things like “Follow PEP 8 style guidelines for Python code” or “Use Python 3.10+ features when applicable.” Beast Mode will take those into account (it automatically includes files from `.github/instructions`). If you use certain Python frameworks, add an instructions file about them (e.g., Django best practices) and it will help the agent use them correctly. Burke even suggests an example where he included a UI framework guide (shadcn UI) as an instruction file so Beast Mode would know how to properly use it. Finally, don’t forget to apply the recommended settings we discussed (Auto-approve and `maxRequests`). Beast Mode explicitly relies on tool use, so auto-approval lets it run without interruptions. And complex tasks can easily exceed the default 25 calls – setting 100 ensures it can keep “cooking” through long sequences. With these in place, Beast Mode + GPT-4.1 will be a formidable coding assistant for your Python work (and beyond).

## Prompt Files for Prompt Engineering & Documentation

In addition to autonomous coding, you mentioned needing help with two things: (a) improving your own prompts (prompt engineering assistance) and (b) a “Beast Mode” approach for Markdown business-case documents. We can tackle both by creating custom prompt files.

### A. Prompt Improvement ("Prompt Boosting")

It’s often useful to have the AI refine your initial prompt into a better one – essentially turning a rough idea into a well-engineered prompt before execution. One proven technique is to use a meta-prompt: ask the LLM to act as a “prompt engineer” and rewrite your prompt to include all the best practices (clear role, context, constraints, etc.). We can automate this in VS Code by creating a prompt file for it.

Create a prompt file (e.g. `.github/prompts/improve-prompt.prompt.md`) with content like:

```markdown
---
description: Improve a user-provided prompt by adding clarity and detail.
mode: ask
---
You are an **expert prompt engineer**. Your task is to take the user's raw prompt and transform it into a **detailed, optimized prompt** that will yield a superior result.

**Steps**: First, analyze the user's request for any ambiguities or missing info. Then **rewrite the prompt** to include:
1. A clear **role or persona** for the AI (e.g. "Act as a data scientist...").
2. Essential **context or background** details so the AI isn't guessing.
3. A specific **output format** or style (if expected, e.g. bullet list, JSON, etc.).
4. **Examples or references** (if helpful to guide the tone or correctness).
5. Any important **constraints** or instructions (e.g. "avoid technical jargon", or length limits).

User's original prompt: "${input:prompt}"

Now, **produce only the optimized prompt** (no other commentary).
```

This prompt file (invoked in Ask mode) encapsulates the meta-prompt template described in prompt engineering guides. It essentially tells the LLM: “Here’s my prompt – please rewrite it to be much better, following these guidelines.” Notice we included an input variable `${input:prompt}` – this lets you pass your actual prompt into the file when running it.

#### How to use it

In Copilot Chat, type something like:

`/improve-prompt: prompt="Generate a summary of the new features in our product."`

(include your prompt text in the quotes). The LLM will respond with a polished, more structured prompt. For example, a rough instruction like “explain X feature” might come back as a detailed prompt: “Act as a technical writer for our product. Explain the new XYZ feature introduced in version 4.2, including its purpose, how it works, and the benefits to the user, in 2-3 paragraphs of clear non-technical language.” At this point, you can copy that improved prompt and run it to get the final answer. (If you’re in the Chat UI, you could paste it as a new question; if using prompt files in a chain, see below.) This approach forces clarity and often yields far better results. It’s like having the AI do prompt engineering for you – and you’ll learn from it over time.

#### Going a step further – chaining prompts

You mentioned ideally the improved prompt would then be executed automatically. VS Code’s prompt files allow prompt chaining, where one prompt file can call another. For example, you could create a second prompt file for the actual task (code generation or document writing) and reference it at the end of the “improve-prompt” file. The Copilot agent can then run the improved prompt through the next file in one go. In practice, this is done by adding a markdown link in the prompt file. E.g., your `improve-prompt.prompt.md` could end with: “Use the above refined prompt to run the Actual Task below: Run Task.” When you execute `improve-prompt`, it would then automatically execute the linked `actual-task.prompt.md` with the refined prompt content. This is an advanced technique, and as of now you can’t switch models mid-chain (the same model is used for all linked prompts), but it’s powerful for automating multi-step workflows. If this interests you, see the Copilot docs and community examples on prompt chaining. Otherwise, running the improved prompt manually as described is perfectly fine and perhaps easier to QA. (Side note: There was a mention of a “prompt boost” tool in Copilot – likely referring to a similar idea of rewriting prompts. The method above achieves that, and by doing it via a prompt file, you can see and edit the improved prompt before using it, which is great for learning and control.)

### B. “Beast Mode” for Markdown Documentation

Now, for Markdown business case development, you can create a custom prompt or mode that guides the AI through a structured writing process, analogous to how Beast Mode guides coding. The goal is to have the LLM ask the right questions, plan an outline, and produce a polished document (e.g., for Confluence or Jira) with minimal back-and-forth. We’ll create a prompt file (say `.github/prompts/business-case.prompt.md`) optimized for documentation tasks:

```markdown
---
description: Draft a comprehensive business-case document with iterative refinement.
mode: ask
---
You are an **expert technical writer** and business analyst. You will assist in crafting a clear, well-structured **business case** in Markdown format.

**Process**:
1. **Clarify Requirements** – Begin by asking me any important questions about the business case (purpose, audience, key data, etc.) that are not yet clear. Gather all info you need.
2. **Outline the Document** – Propose a draft outline with sections (e.g. Background, Problem Statement, Proposed Solution, Benefits, Risks, Conclusion). Present this as a checklist of sections or topics to cover (using markdown checkboxes or emojis to mark completion status).
3. **Iterative Drafting** – After we confirm the outline, proceed to write the document **section by section**. For each section: mark it as in progress, write the content in detail (with Markdown headings, lists, etc. as appropriate), then mark that section as completed (✅). Ensure each section is thorough but concise and uses an appropriate tone for business stakeholders.
4. **Review & Refine** – After drafting all sections, review the entire document for clarity, completeness, and coherence. Make any improvements needed, then present the final polished business case.

Throughout the process, **keep me involved** – especially in steps 1 and 2 (Q&A and outline). Only start drafting once the outline is approved. Use a professional, persuasive tone and include factual details (I can provide data if prompted). Format the final document in valid Markdown, ready for Confluence/Jira.

Let's begin by gathering requirements.
```

Let’s break down what this does. We set the mode to `Ask` (no auto-editing, we just want a conversational workflow). The prompt instructs the AI to take on a role (expert business case writer) and outlines a multi-step approach:

- **Step 1**: It should ask clarifying questions. This ensures the AI doesn’t assume missing context. For example, it might ask “Who is the audience for this business case?” or “Do we have any metrics on the current problem?”
- **Step 2**: It should then produce an outline for the business case. We suggest it uses a checklist format (which is similar to how Beast Mode uses a TODO list to plan). This might look like: 🔲 Background, 🔲 Problem Statement, 🔲 Proposed Solution, etc. Each item can be an empty checkbox (or an emoji) initially. This gives you a chance to review the structure before any long content is written.
- **Step 3**: Once you approve the outline (you can say “Looks good, proceed” or ask for adjustments), the AI will draft each section. The prompt tells it to mark sections as in progress and then done (ticking off the checklist ✅ as it goes). You’ll see the document growing section by section. For instance, it might output a heading “## Background” with some paragraphs, then mark that as ✅ in the checklist, then move on to the next section. This approach keeps the AI focused and you informed of progress – very much “beast mode” style iterative writing.
- **Step 4**: Finally, it reviews the whole document and polishes it. You can of course read the final output and ask for any tweaks or additions if something is missing.

#### Using the documentation prompt

Run it by typing `/business-case` in chat (or via the prompt list). The assistant will start with step 1, asking questions. Answer those questions as needed (the better info you give, the better the document). After Q&A, it will show you an outline. Confirm or modify it. Then let it “cook.” You’ll get a nicely formatted Markdown document at the end, which you can copy into Confluence or Jira. This saves a ton of time on structuring and initial drafting. You can always edit the final text, but this workflow ensures the major pieces are in place. This “documentation mode” prompt is inspired by the same principles as coding Beast Mode: plan first, then execute step by step, keep track of tasks, and verify at the end. By explicitly instructing the AI to use that approach for writing, you avoid it jumping straight into a rambling answer. Instead, it will behave more systematically: confirming requirements, outlining, then filling in content – similar to how a human would approach writing a complex document.

## Conclusion & Next Steps

With the configuration and custom files above, your VS Code environment will be set up for maximum productivity with Copilot:

- Copilot Chat will always follow your coding standards from the `copilot-instructions.md` (no more ad-hoc style fixes).
- Beast Mode is on hand for heavy-lifting tasks in Python (or any development) – it will plan, code, test, and research autonomously until the job is done. This is a proven workflow that many developers use to boost efficiency.
- Prompt files give you one-click superpowers: you can improve your prompts, generate documentation, scaffold code, etc., by simply typing a `/command`. This is especially useful for repetitive tasks or complex workflows that you’ve encoded once in a prompt file. Feel free to create more prompt files as you identify needs – for example, a prompt file to do a code review on a PR, or to generate a design diagram from code comments, etc. The sky’s the limit, and you can share these with your team for consistency.

A few final suggestions:

- **Iterate and refine**: Treat your custom instructions and prompts as living documents. If you notice the AI doing something undesirable repeatedly, add a note in `copilot-instructions.md` or the relevant instructions file (this is exactly what the “memory” feature in Beast Mode does for you). If a prompt file’s output isn’t ideal, tweak the wording or add an example to guide it. For instance, you can put a small example in the `business-case` prompt like “(e.g. If the user says the problem is slow login times, a section could be 'Problem Statement: Users experience slow logins…')”. The more you refine, the better the AI’s responses will align with your expectations.
- **Stay updated on Copilot features**: GitHub and VS Code are rapidly improving Copilot. Watch for new features like multi-model prompt chaining, or improvements to custom mode sharing. For example, there’s community chatter about using different models for different prompt file steps – which might eventually let your “prompt improver” use GPT-4, then the actual task run on a faster model, automatically. Also keep an eye on Burke Holland’s blog/GitHub for updates to Beast Mode (v3.1 was July 2025). You can update your Beast Mode file if a new version comes out with further enhancements.

By combining robust settings, a well-structured `.github` folder, Beast Mode for coding, and tailor-made prompt files for other tasks, you’ll have a truly “supercharged” VS Code setup. This proven workflow will minimize tedious setup and maximize what you can get done with AI assistance – letting you focus on creativity and problem-solving while the LLM handles the heavy lifting. Happy coding and writing! 🚀
