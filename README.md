# /forge - Full Page Builder from Figma

A Claude Code skill that builds complete pages from Figma by combining screenshot context with precise component specs.

## What is /forge?

`/forge` is a custom skill for [Claude Code](https://claude.com/claude-code) that builds full pages using a hybrid approach:

1. **Screenshot** - Provides layout and composition context
2. **Figma MCP** - Provides precise component specifications
3. **Analyze** - Opus creates comprehensive build plan
4. **Build** - Sonnet implements components (parallel or sequential)
5. **Verify** - Haiku + Playwright compares against screenshot
6. **Iterate** - Fixes discrepancies until pixel-perfect (max 3 iterations)

## Features

- 📸 **Hybrid approach** - Screenshot for layout + Figma MCP for precision
- ⚡ **Parallel building** - Build multiple components simultaneously (optional)
- 🎯 **Smart grouping** - Select 4-6 main components in Figma, not 500 individual elements
- 💰 **Cost-optimized** - Model tiering (Opus/Sonnet/Haiku) with transparent cost estimates
- 🔄 **Iterative refinement** - Automatically compares and fixes until pixel-perfect
- 🚀 **Zero configuration** - Works with any React/TypeScript codebase

## Installation

1. **Prerequisites:**
   - [Claude Code CLI](https://code.claude.com)
   - [Figma Desktop MCP server](https://github.com/anthropics/mcp-server-figma) configured
   - [Playwright MCP server](https://github.com/executeautomation/mcp-playwright) configured

2. **Install the skill:**
   ```bash
   # Clone this repo
   git clone https://github.com/[your-username]/forge-skill.git

   # Symlink to Claude's global skills directory
   ln -s ~/Projects/forge-skill ~/.claude/skills/forge
   ```

3. **Verify installation:**
   ```bash
   # In any project, type:
   /forge
   ```
   It should appear in autocomplete.

## Usage

```bash
/forge
```

### Example Workflow

1. **Prepare in Figma:**
   - Group the main sections of your page (Header, Sidebar, MainContent, Footer, etc.)
   - Aim for 4-6 main components
   - Take a screenshot of the full page (save it for step 4)

2. **Select components in Figma:**
   - Multi-select all grouped components (or select parent Frame)

3. **Run forge:**
   ```
   /forge
   ```
   - Forge prompts: "Reply 'ready' when your selection is active"
   - You reply: `ready`
   - **Forge immediately captures component specs** (~5 seconds)
   - Forge confirms: "✓ Got specs for 4 components. You can now work on other things in Figma."

4. **Upload screenshot:**
   - Forge prompts: "Please attach a full-page screenshot"
   - Upload your screenshot (from step 1)
   - **Your Figma selection doesn't need to stay active anymore!**

5. **Watch it build:**
   - Analyzes screenshot for layout/composition
   - Creates build plan using cached Figma specs
   - Builds components (in parallel if independent)
   - Verifies against screenshot
   - Reports cost breakdown

**Result:** Complete page with pixel-perfect components!

## Cost Expectations

**Example: Page with 4 components (1-2 iterations)**

| Phase | Model | Count | Est. Cost |
|-------|-------|-------|-----------|
| Analysis | Opus | 1 | ~$0.60 |
| Implementation | Sonnet | 4 (parallel) | ~$1.60 |
| Verification | Haiku | 2 | ~$0.16 |
| Fixes | Sonnet | 2 | ~$0.60 |
| **Total** | | | **~$3-4** |

**Note:** Parallel building uses more tokens upfront (multiple Sonnet agents) but completes much faster. Sequential building is cheaper but slower.

## How It Works

### The Hybrid Approach

**Screenshot provides:**
- Overall page composition
- Spatial relationships between components
- Layout structure (grid, flex, positioning)
- Visual context for "how things fit together"

**Figma MCP provides:**
- Exact dimensions (px values)
- Precise colors (hex values)
- Typography specs (font, size, weight, line-height)
- Spacing values (padding, margins, gaps)
- Component hierarchy and names

**Together:** Visual context + precise specs = pixel-perfect implementation

### Model Tiering

- **Opus** - Page analysis and comprehensive build planning
- **Sonnet** - Component implementation (parallel or sequential)
- **Haiku** - Fast verification against screenshot + specs

### Exit Criteria

Achieves "pixel-perfect" when:
- Typography matches (font, size, weight, line-height, color)
- Spacing matches (padding, margins, gaps)
- Colors match (backgrounds, borders, text)
- Layout structure matches

Maximum 3 iterations with early exit when perfect.

## Requirements

- Node.js project with React + TypeScript
- Design tokens defined in CSS
- Dev server running (for Playwright verification)
- Figma Desktop app with selection ready

## Permissions

The skill uses these tools (pre-approved when invoked):
- File operations: `Read`, `Write`, `Edit`, `Glob`, `Grep`
- Development: `Bash` (for npm, git, etc.)
- Orchestration: `Task`, `TodoWrite`
- Figma MCP: `mcp__figma-desktop__*`
- Playwright MCP: `mcp__playwright__*`

## Contributing

This is a living skill that improves with use. Contributions welcome!

1. Fork the repo
2. Create a feature branch
3. Make your improvements
4. Test with real Figma designs
5. Submit a PR

## License

MIT

## Credits

Built with [Claude Code](https://claude.com/claude-code) by Anthropic.

Developed through iterative prompt engineering and real-world testing.
