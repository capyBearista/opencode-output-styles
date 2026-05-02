# Plugin Improvement Guide: Hybrid System Prompt Architecture

This guide outlines the "Option C" (Hybrid Architecture) for the `opencode-output-styles` plugin. This approach balances the need for "fresh" upstream prompts with the requirement for bulletproof reliability.

## The Problem: Fragility vs. Staleness

*   **Option A (Hardcoding):** Reliable but becomes stale. Users miss out on upstream OpenCode agent updates, tool-use fixes, and behavioral improvements.
*   **Option B (Dynamic Stripping):** Fresh but fragile. Natural language prompts are not a stable API. Minor upstream formatting changes break regex/parsers, causing "prompt leakage" (coding instructions interfering with your custom style).

## The Solution: Option C (Hybrid Architecture)

Implement a **Defensive Dynamic Extraction with Static Fallback** strategy.

### Step 1: Resilient Dynamic Extraction

Instead of simple regex, move toward a more structural parsing approach. 

*   **Header-Based Splitting:** OpenCode prompts use Markdown headers (e.g., `# Core Mandates`, `# Primary Workflows`). Use a Markdown AST parser (like `mdast`) or a more robust regex that splits by headers.
*   **Targeting:** 
    *   For **Gemini/GPT**: Targeted removal of the `# Core Mandates` and `# Primary Workflows` sections.
    *   For **Anthropic**: Targeted removal of the `# Doing tasks` section.

### Step 2: The Validation Gate

Before applying the stripped prompt, you must verify that the extraction actually worked as intended. If the validation fails, it means the upstream OpenCode prompt format has changed.

**Heuristics to check:**
1.  **Match Confirmation:** Did your extraction logic find and remove the target sections? (e.g., `didMatch === true`).
2.  **Length Delta:** Did the string length decrease by an expected amount (e.g., at least 20-30%)? 
3.  **Keyword Density Check:** Does the resulting string still contain "forbidden" high-density coding terms like `TypeScript`, `refactor`, `package.json`, or `fix bugs`?

### Step 3: Graceful Degradation (The Fallback)

If Step 2 fails, do not ship a broken prompt. Instead:

1.  **Catch the Error:** Use a `try/catch` or an explicit error state from your validation gate.
2.  **Inject Fallback:** Revert `output.system[0]` to a bundled, hand-edited, known-good version of the base prompt that you ship within the plugin.
3.  **Notify the User:** Use `console.warn` or a system message to inform the user: 
    > "OpenCode base prompt format has changed. Using a safe fallback style. Please update the opencode-output-styles plugin for the latest experience."

### Step 4: Upstream Strategic Alignment

To eliminate this problem long-term, contribute a Pull Request to the OpenCode core repository.

**Proposed Changes:**
Wrap the coding-specific sections of `gemini.txt`, `anthropic.txt`, and `gpt.txt` in semantic XML tags:

```markdown
<opencode_coding_instructions>
# Core Mandates
...
# Primary Workflows
...
</opencode_coding_instructions>
```

Once these tags are upstream, your plugin can simply use a single, bulletproof regex to remove everything between those tags, making your dynamic extraction 100% reliable regardless of internal formatting changes.

## Recommended Implementation Flow

1.  **Define Bundled Fallbacks:** Create a directory in your plugin `assets/fallbacks/` containing cleaned `gemini.txt` and `anthropic.txt`.
2.  **Implement `PromptStripper` Class:** Create a utility that takes a raw string, a model family, and returns a `Result<{ stripped: string, isFallback: boolean }>`.
3.  **Integrate with Hook:** Update your `experimental.chat.system.transform` hook to use the `PromptStripper` and handle the notification logic.
