# /forge - Pixel-Perfect UI from Figma

A Claude Code skill that transforms Figma designs into pixel-perfect code through an intelligent iterative workflow.

## What is /forge?

`/forge` is a custom skill for [Claude Code](https://claude.com/claude-code) that automates the design-to-code workflow:

1. **Assess** - Analyzes Figma selection and existing codebase
2. **Plan** - Creates detailed implementation specs using Opus
3. **Build** - Implements with Sonnet (builds new or refines existing)
4. **Verify** - Compares implementation against Figma using Haiku + Playwright
5. **Iterate** - Fixes discrepancies until pixel-perfect (max 3 iterations)

## Features

- 🎯 **Auto-detects mode** - Intelligently chooses BUILD vs REFINE based on your codebase
- 💰 **Cost-optimized** - Smart model tiering (Opus for planning, Sonnet for implementation, Haiku for verification)
- 🔄 **Iterative refinement** - Automatically compares and fixes until pixel-perfect
- 📊 **Cost transparency** - Reports estimated costs for each phase
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
# Select a component or page in Figma, then:
/forge

# Or optionally specify a name:
/forge ComponentName
```

### Example Workflow

1. Open Figma and select the design you want to implement
2. In your terminal, navigate to your project
3. Run `/forge`
4. Watch as it:
   - Analyzes the Figma design
   - Decides whether to build new or refine existing
   - Implements the component
   - Verifies pixel-perfect accuracy
   - Reports cost breakdown

## Cost Expectations

| Scenario | Model Usage | Estimated Cost |
|----------|-------------|----------------|
| Component refinement | 1 Opus + 1 Sonnet + 1-2 Haiku | ~$0.50-1.00 |
| New component build | 1 Opus + 1-2 Sonnet + 2-3 Haiku | ~$1.50-2.50 |
| Full page build | 1-2 Opus + 2-3 Sonnet + 3-6 Haiku | ~$4-6 |

## How It Works

### Model Tiering

- **Opus** - Analysis and planning (high-quality comprehensive plans)
- **Sonnet** - Implementation and fixes (capable, cost-effective)
- **Haiku** - Verification (fast, cheap comparisons)

### BUILD vs REFINE

The skill automatically determines the approach:

- **BUILD mode** - Creates new components from scratch when:
  - No matching component exists
  - Existing component has wrong structure

- **REFINE mode** - Makes surgical modifications when:
  - Component exists and structure is close
  - Only styling/spacing needs adjustment

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
