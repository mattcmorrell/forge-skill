---
name: forge
description: Forge pixel-perfect UI from Figma designs - automatically builds new components or refines existing ones
argument-hint: "[optional: component-name]"
allowed-tools: Read, Write, Edit, Glob, Grep, Bash, Task, TodoWrite, mcp__figma-desktop__*, mcp__playwright__*
---

# /forge - Pixel-Perfect UI from Figma

Forge transforms Figma designs into pixel-perfect code through an intelligent iterative workflow. It automatically determines whether to build from scratch or refine existing components.

## Usage

```bash
# Select something in Figma, then just:
/forge

# Or optionally specify a name:
/forge RequestItem
```

The component name is inferred from the Figma selection if not provided.

## How It Works

1. **Assess** - Analyze Figma selection and existing codebase
2. **Plan** - Create detailed specs with Opus
3. **Build** - Implement with Sonnet
4. **Verify** - Compare with Haiku
5. **Iterate** - Fix discrepancies until pixel-perfect

## Model Tiering (Cost Optimized)

| Task | Model | Why |
|------|-------|-----|
| Analysis & Planning | **Opus** | Deep reasoning, comprehensive plans |
| Implementation | **Sonnet** | Capable, cost-effective |
| Verification | **Haiku** | Fast, cheap comparisons |
| Minor Fixes | **Sonnet** | Straightforward changes |
| Major Fix Analysis | **Opus** | Needs re-evaluation |

---

## Step 1: SETUP

1. **Verify dev server**
   - Check if running, start if needed (`npm run dev`)
   - Note the port for Playwright comparison

2. **Fetch Figma selection**
   - `mcp__figma-desktop__get_design_context` → design specs
   - `mcp__figma-desktop__get_screenshot` → visual reference
   - Cache for reuse across agents

3. **Create todo list** to track progress

---

## Step 2: ASSESS & PLAN (Opus)

Spawn a **Task agent with model: opus** to assess and plan:

### Assessment Phase

First, determine the approach by analyzing:

1. **Does a matching component exist?**
   - Search `/src/components/` and `/src/pages/` for similar names
   - Read existing files if found

2. **How much work is needed?**
   - If no component exists → **BUILD mode**
   - If component exists but structure is wrong → **BUILD mode** (rebuild)
   - If component exists and structure is close → **REFINE mode**

3. **Announce the decision:**
   ```
   MODE: BUILD - Creating new component from scratch
   -- or --
   MODE: REFINE - Modifying existing component at /src/components/X/X.tsx
   ```

### Planning Phase

**For BUILD mode**, create:
- Complete component architecture
- File structure to create
- Full implementation specs with code snippets
- Data structures and TypeScript interfaces
- Verification checklist

**For REFINE mode**, create:
- Current vs Target comparison table:
  ```
  | Property     | Current | Target  | Change |
  |--------------|---------|---------|--------|
  | Font size    | 40px    | 48px    | YES    |
  | Color        | #38312f | #2e7918 | YES    |
  ```
- Specific changes needed (minimal, surgical)
- Verification checklist

### Output Requirements

The plan MUST include an explicit **Verification Checklist**:
```markdown
## Verification Checklist
- [ ] Font family: Fields Bold
- [ ] Font size: 48px
- [ ] Line height: 58px
- [ ] Color: #2e7918
- [ ] Padding top: 32px
- [ ] Padding bottom: 24px
- [ ] Padding horizontal: 32px
```

---

## Step 3: IMPLEMENTATION (Sonnet)

Spawn a **Task agent with model: sonnet** to implement:

**BUILD mode:**
- Create all files specified in the plan
- Follow existing codebase patterns
- Use design tokens from `/src/index.css`
- Export components properly

**REFINE mode:**
- Modify only what's specified
- Keep changes minimal and focused
- Preserve existing functionality

---

## Step 4: BUILD VERIFICATION (Haiku)

Spawn a **Task agent with model: haiku** to verify:

1. TypeScript compiles without errors
2. Dev server hot-reloads without errors
3. No console errors on page load

Fix any issues before proceeding.

---

## Step 5: COMPARISON LOOP (Max 3 iterations)

### 5a. COMPARE (Haiku)

Spawn a **Task agent with model: haiku** with Playwright access:

1. Navigate to the page
2. Take screenshot of the implementation
3. Check each item on the Verification Checklist:
   - [ ] Typography correct?
   - [ ] Colors correct?
   - [ ] Spacing correct?
   - [ ] Layout correct?
   - [ ] Icons present?

4. Output verdict:
   - **PIXEL-PERFECT** - All checks pass
   - **NEEDS WORK** - List specific failures

### 5b. FIX IF NEEDED

**PIXEL-PERFECT** → Exit loop, celebrate!

**Minor issues** (spacing, colors, font sizes):
- Sonnet fixes directly, no re-analysis

**Major issues** (structural, layout direction):
- Opus re-analyzes
- Sonnet implements fix

### 5c. EARLY EXIT

Exit immediately when pixel-perfect. Don't waste iterations.

---

## Cost Expectations

| Scenario | Expected Cost |
|----------|---------------|
| Component refinement (1 iteration) | ~$0.50-1.00 |
| Component build (1-2 iterations) | ~$1.50-2.50 |
| Full page build (2-3 iterations) | ~$4-6 |

---

## Built-in Optimizations

1. **Smart mode selection** - AI chooses build vs refine
2. **Cached Figma context** - Fetch once, reuse
3. **Haiku for verification** - 90% cheaper than Sonnet
4. **Explicit checklists** - No ambiguity in comparison
5. **Early exit** - Stop when pixel-perfect
6. **Surgical refines** - Minimal changes when possible

---

## Exit Criteria

**Pixel-Perfect means:**
- All typography matches (font, size, weight, line-height, color)
- All spacing matches (padding, margins, gaps)
- All colors match (backgrounds, borders, text)
- Layout structure matches
- Icons acceptable if different library but visually similar

**Max iterations:** 3

---

## Component Name (Optional)

If provided: `$ARGUMENTS`

If not provided: Infer the component name from the Figma selection metadata (layer name, frame name, etc.)

---

## Final Output

Provide a summary:
- **Mode used:** BUILD or REFINE
- **Files created/modified**
- **Iterations needed**
- **Verification status**
- **Estimated cost** (calculate based on what ran):

  | Phase | Model | Runs | Est. Cost |
  |-------|-------|------|-----------|
  | Analysis | Opus | 1 | ~$0.40 |
  | Implementation | Sonnet | X | ~$0.30 × X |
  | Verification | Haiku | X | ~$0.03 × X |
  | Fixes | Sonnet | X | ~$0.25 × X |
  | **Total** | | | **~$X.XX** |

- **Any manual checks recommended**

**Cost Reference:**
- Opus analysis: ~$0.30-0.50 per run
- Sonnet implementation: ~$0.20-0.40 per run
- Haiku verification: ~$0.02-0.05 per run
- Sonnet fix: ~$0.15-0.30 per run
- Opus re-analysis (if major issues): ~$0.30-0.50 per run
