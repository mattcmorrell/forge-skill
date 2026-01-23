# /forge - Simple Figma URL Builder

A Claude Code skill that builds pixel-perfect pages from Figma URLs using a straightforward approach that achieves better results than complex multi-phase workflows.

## What is /forge?

`/forge` is a custom skill for [Claude Code](https://claude.com/claude-code) that builds pages from Figma URLs:

1. **Provide URLs** - Paste Figma component URLs (or parent frame URLs)
2. **Decompose** - Auto-discovers child components from parent frames
3. **Fetch Specs** - Gets precise specs for all components in parallel
4. **Clarify** - Optionally asks questions if behavior is ambiguous
5. **Build in Parallel** - Spawns agents to build all components simultaneously
6. **Assemble** - Creates page component using screenshot for layout context

**Result: Pixel-perfect implementation with minimal complexity.**

## Features

- 🔗 **URL-based** - Works from Figma URLs instead of selections (more accurate, reproducible)
- 🔍 **Auto-decomposition** - Discovers child components from parent frame URLs
- ⚡ **Parallel building** - Builds all components simultaneously with separate agents
- 📸 **Screenshot layout** - Uses full-page screenshot for component composition
- 💬 **Smart clarification** - Only asks questions when behavior is genuinely ambiguous
- 🎯 **Pixel-perfect** - Achieves better accuracy than complex multi-phase approaches
- 🚀 **Simple workflow** - No analysis phases, verification loops, or iterations
- 💰 **Cost-efficient** - Straightforward parallel building, no expensive verification cycles
- 🔧 **Zero configuration** - Works with any React/TypeScript codebase

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
   - Take a screenshot of your full page (save for step 3)
   - Right-click main components → "Copy link"
   - Can copy parent frame URL (forge auto-discovers children)
   - Or copy individual component URLs

2. **Run forge:**
   ```
   /forge
   ```

3. **Provide screenshot and URLs:**
   - Paste your screenshot
   - Paste Figma URLs (one per line)

   Example:
   ```
   https://www.figma.com/design/3Vs1Y.../node-id=656-22960
   https://www.figma.com/design/3Vs1Y.../node-id=660-24330
   ```

4. **Watch it build:**
   - Decomposes URLs to find child components
   - Fetches all specs in parallel
   - Asks clarifying questions if needed (optional)
   - Builds all components in parallel with separate agents
   - Assembles page using screenshot for layout

**Result:** Pixel-perfect page in one pass!

## How It Works

### URL-Based Decomposition

**Step 1: Provide Figma URLs**
- Right-click components in Figma Desktop → "Copy link"
- Can provide parent frame URLs or individual component URLs
- Example: `https://www.figma.com/design/3Vs1Y.../node-id=656-22960`

**Step 2: Auto-Discover Components**
- Forge extracts node ID from URL (e.g., `656-22960` → `656:22960`)
- Calls `get_metadata(nodeId)` to check for children
- If parent has child components, extracts them automatically
- Builds list of all components to create

**Step 3: Fetch Precise Specs**
- Calls `get_design_context(nodeId)` for each component in parallel
- Gets exact dimensions, colors, typography, spacing from Figma
- Calls `get_screenshot(nodeId)` for visual reference
- All data retrieved simultaneously (fast)

**Step 4: Optional Clarification**
- Only asks questions if behavior is genuinely ambiguous
- Example: "Should this button open a modal or navigate?"
- If no ambiguity, makes reasonable assumptions and proceeds

**Step 5: Parallel Building**
- Spawns separate agents for each component
- Each agent builds one component with focused specs
- All agents run simultaneously
- No complex orchestration or intermediate steps

**Step 6: Page Assembly**
- Creates page component that imports all built components
- Uses screenshot to determine layout (positioning, nesting, structure)
- Ensures components compose correctly

### Why This Works

**URL-based is more accurate:**
- Direct node ID fetching gives cleaner, more complete specs
- No timing issues with selections staying active
- Reproducible (same URLs = same results every time)
- Parallel fetching is fast

**Simple workflow achieves better results:**
- No analysis phase to lose precision
- No verification loop with iterative fixes
- Each agent gets focused data directly from Figma
- Straightforward decompose → build → assemble

**Result: Pixel-perfect in one pass**

### Cost Expectations

**Example: Page with 4-6 components**
- Decomposition: ~$0.20 (metadata + specs fetching)
- Parallel building: 6 × ~$0.40 = ~$2.40 (Sonnet agents)
- Page assembly: ~$0.20 (composition)
- **Total: ~$2.80-3.00**

Much more cost-efficient than complex multi-phase approaches with verification loops.

## Requirements

- Node.js project with React + TypeScript
- Figma Desktop app installed and running
- Figma Desktop MCP server configured (see Installation)

## Permissions

The skill uses these tools (pre-approved when invoked):
- File operations: `Read`, `Write`, `Edit`, `Glob`, `Grep`
- Development: `Bash` (for npm, git, etc.)
- Orchestration: `Task`, `TodoWrite`
- Figma MCP: `mcp__figma-desktop__*`

## Legacy Approach

The original forge used a complex multi-phase workflow (Opus analysis → Sonnet implementation → Haiku verification → iteration loop). While sophisticated, it achieved only ~75% accuracy.

The current simple URL-based approach achieves pixel-perfect results by:
- Eliminating intermediate analysis/verification phases
- Using URL-based specs (cleaner data than selection-based)
- Direct parallel building without orchestration overhead

**The legacy implementation is preserved in `SKILL-legacy.md` for reference.**

Key lesson: **Simple workflows with clean data > complex workflows with transformations.**

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
