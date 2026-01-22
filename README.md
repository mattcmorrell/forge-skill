# /forge - Full Page Builder from Figma

A Claude Code skill that creates new pages or refines existing components from Figma by combining screenshot context with precise component specs.

## What is /forge?

`/forge` is a custom skill for [Claude Code](https://claude.com/claude-code) that builds full pages using a hybrid approach:

1. **Mode Selection** - Choose to create new or refine existing components
2. **Screenshot** - Provides layout and composition context
3. **Figma MCP** - Provides precise component specifications
4. **Analyze** - Opus creates comprehensive build plan
5. **Build/Refine** - Sonnet creates new or updates existing components (parallel or sequential)
6. **Verify** - Haiku + Playwright compares against screenshot
7. **Iterate** - Fixes discrepancies until pixel-perfect (max 3 iterations)

## Features

- 🎭 **Dual mode** - Create new components or refine existing ones
- 📸 **Hybrid approach** - Screenshot for layout + Figma MCP for precision
- 📁 **File-based workflow** - Saves specs to `.forge/` directory, keeps context lean for precision
- ⚡ **Parallel building** - Build multiple components simultaneously (optional)
- 🎯 **Smart grouping** - Select 4-6 main components in Figma, not 500 individual elements
- 🔧 **Surgical refinements** - Preserves logic, only updates styling in refine mode
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
   - Take a screenshot of the full page (save it for step 5)

2. **Select components in Figma:**
   - Multi-select all grouped components (or select parent Frame)

3. **Run forge:**
   ```
   /forge
   ```
   - Forge asks: "Creating new or refining existing?"
   - You reply: `1` (new) or `2` (refine)

4. **Capture components:**
   - Forge prompts: "Reply 'ready' when your selection is active"
   - You reply: `ready`
   - **Forge immediately captures component specs** (~5 seconds)
   - Forge confirms: "✓ Got specs for 4 components. You can now work on other things in Figma."

5. **Upload screenshot:**
   - Forge prompts: "Please attach a full-page screenshot"
   - Upload your screenshot (from step 1)
   - **Your Figma selection doesn't need to stay active anymore!**

6. **Watch it build:**
   - Analyzes screenshot for layout/composition
   - Creates build plan using cached Figma specs
   - Creates/refines components (in parallel if independent)
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

### File-Based Workflow

Forge saves data to a `.forge/` directory to keep context lean and ensure precision:

- **`.forge/figma-specs.json`** - Exact component specs from Figma MCP
- **`.forge/screenshot.png`** - Original design screenshot
- **`.forge/plan.md`** - Comprehensive build plan from Opus
- **`.forge/implementation.png`** - Current implementation screenshot (for comparison)

**Why this matters:**
- Each agent reads **only** its component's specs (not all 6 components)
- Reduces token bloat in context by ~70%
- Small details like spacing (24px vs 16px) don't get lost in noise
- Fresh data on every read (no stale context)

**Cleanup:**
Add `.forge/` to your `.gitignore`. The directory is recreated on each forge run and can be safely deleted after completion.

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
