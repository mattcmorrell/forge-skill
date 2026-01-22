---
name: forge
description: Forge pixel-perfect pages from Figma designs using screenshot context and component specs - builds multiple components intelligently
argument-hint: ""
allowed-tools: Read, Write, Edit, Glob, Grep, Bash, Task, TodoWrite, mcp__figma-desktop__*, mcp__playwright__*
---

# /forge - Full Page Builder from Figma

Forge builds complete pages from Figma by combining screenshot context (composition/layout) with precise component specs from Figma MCP.

## Initial Prompt (Always Start Here)

When invoked, **immediately respond with this:**

```
📸 Ready to forge a page from Figma!

Step 1: Prepare your Figma selection
- In Figma Desktop, select all main components on the page
- Either multi-select the major components (Header, Sidebar, MainContent, etc.)
- Or select the parent Frame containing all components
- Aim for 4-6 main sections

Reply "ready" when your selection is active, and I'll capture the component specs immediately.
```

**STOP here and wait for user confirmation (e.g., "ready", "done", "ok").**

### After User Confirms Selection

**Immediately:**
1. Call `mcp__figma-desktop__get_design_context()` to capture selected components
2. Save/cache the Figma specs
3. Confirm: "✓ Got specs for [N] components from Figma. You can now change your selection or work on other things in Figma."

**Then prompt for screenshot:**
```
Step 2: Upload screenshot
Please attach a full-page screenshot showing the complete layout and composition of your page.
```

**STOP and wait for screenshot upload.**

## How It Works

1. **Gather Context** - User uploads screenshot + has Figma components selected
2. **Fetch Specs** - Get all selected components from Figma MCP in one call
3. **Analyze Composition** - Use screenshot to understand layout/spatial relationships
4. **Plan Page** - Create page structure with component breakdown (Opus)
5. **Build Components** - Implement each component with precise specs (Sonnet, can be parallel)
6. **Verify** - Compare against screenshot using Playwright (Haiku)
7. **Iterate** - Fix discrepancies until pixel-perfect

## Model Tiering (Cost Optimized)

| Task | Model | Why |
|------|-------|-----|
| Analysis & Planning | **Opus** | Deep reasoning, comprehensive plans |
| Implementation | **Sonnet** | Capable, cost-effective |
| Verification | **Haiku** | Fast, cheap comparisons |
| Minor Fixes | **Sonnet** | Straightforward changes |
| Major Fix Analysis | **Opus** | Needs re-evaluation |

---

## Step 1: GATHER CONTEXT

### Phase 1: Capture Figma Selection (Happens First)

1. **Wait for user confirmation** that Figma selection is ready (e.g., "ready", "done")

2. **Immediately fetch from Figma MCP:**
   ```
   mcp__figma-desktop__get_design_context()
   ```
   This returns ALL selected components with their specs (dimensions, colors, typography, spacing).

3. **Confirm to user:**
   "✓ Got specs for [N] components. You can now work on other things in Figma."

4. **Cache the Figma data** for use in later steps

### Phase 2: Get Screenshot (Happens Second)

1. **Prompt user:** "Please attach a full-page screenshot showing the layout"

2. **Wait for screenshot upload**

3. **Verify dev server** is running, start if needed (`npm run dev`)

### Summary

At this point you have:
- Figma specs (captured and cached)
- Screenshot (for composition/layout understanding)
- Dev server running

User's Figma selection can change - you already have the data!

---

## Step 2: ANALYZE & PLAN (Opus)

Spawn a **Task agent with model: opus** to analyze and create implementation plan:

### Analysis Phase

Analyze both inputs together:

1. **Screenshot Analysis** (Composition/Layout):
   - Identify the page structure (grid, flex, columns)
   - Note spatial relationships (how components are positioned relative to each other)
   - Understand layout flow (header → content → footer, sidebar + main, etc.)
   - Identify breakpoints and responsive behavior hints

2. **Figma Specs Analysis** (Component Details):
   - Parse the Figma MCP response for each selected component
   - Extract precise dimensions, colors (hex values), typography (font, size, weight, line-height)
   - Note spacing/padding values
   - Identify interactive states if present

3. **Component Mapping**:
   - Match screenshot regions to Figma component specs
   - Name each component appropriately (Header, Sidebar, MainContent, etc.)
   - Create hierarchy (Page → Layout → Components)

### Planning Phase

Create a comprehensive build plan:

**Page Structure:**
```typescript
// Example structure
<PageLayout>
  <Header /> {/* From Figma node-id: 1:234 */}
  <BodyLayout>
    <Sidebar /> {/* From Figma node-id: 2:345 */}
    <MainContent /> {/* From Figma node-id: 3:456 */}
  </BodyLayout>
  <Footer /> {/* From Figma node-id: 4:567 */}
</PageLayout>
```

**For Each Component:**
- File path to create (e.g., `/src/components/Header/Header.tsx`)
- Component interface/props
- Exact styling specs from Figma:
  - Typography: font, size, weight, line-height, color (hex)
  - Spacing: padding, margins, gaps (exact px values)
  - Colors: backgrounds, borders, text (hex values)
  - Dimensions: width, height
- Layout approach (flex, grid, positioning)

**Build Order:**
- Can components be built in parallel? Or do some depend on others?
- Recommend parallel if independent, sequential if dependencies exist

**Verification Checklist:**
For the overall page:
- [ ] Layout structure matches screenshot
- [ ] Component positioning matches
- [ ] Spacing between components matches
- [ ] Responsive behavior works

Per component:
- [ ] Typography matches Figma specs exactly
- [ ] Colors match (use color picker if needed)
- [ ] Dimensions match
- [ ] Spacing/padding matches

---

## Step 3: IMPLEMENTATION (Sonnet)

### Approach

Based on the plan from Step 2, decide:
- **Parallel** if components are independent (faster, more tokens)
- **Sequential** if components depend on each other (safer, slower)

### Parallel Implementation (Recommended)

If components are independent, spawn **multiple Sonnet agents in parallel** (one per component):

```
Task 1 (Sonnet): Build Header component
Task 2 (Sonnet): Build Sidebar component
Task 3 (Sonnet): Build MainContent component
Task 4 (Sonnet): Build Footer component
```

Each agent:
- Creates its component file
- Implements based on Figma specs from the plan
- Uses existing design tokens from `/src/index.css`
- Exports properly

### Sequential Implementation (If Dependencies)

If components depend on each other, spawn **one Sonnet agent** that builds in order:

1. Build page layout structure first
2. Build components one by one
3. Integrate components into layout

### All Implementations Must:
- Follow existing codebase patterns (check `/src/components/` for examples)
- Use TypeScript with proper types
- Use design tokens (colors, spacing from CSS variables)
- Export components via `index.ts` files
- Follow the exact specs from the Figma plan

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

1. **Take implementation screenshot:**
   - Navigate to the page via Playwright
   - Take full-page screenshot

2. **Compare against original screenshot:**
   - Reference the original Figma screenshot provided by user
   - Check layout structure (does it match?)
   - Check component positioning (are things in the right places?)
   - Check spacing between components

3. **Verify against Figma specs checklist:**
   - [ ] Typography matches (font, size, weight)
   - [ ] Colors match (backgrounds, text, borders)
   - [ ] Dimensions match (widths, heights)
   - [ ] Spacing matches (padding, margins, gaps)
   - [ ] All components present

4. **Output verdict:**
   - **PIXEL-PERFECT** - Matches screenshot and all specs pass
   - **NEEDS WORK** - List specific failures with references to which component/area

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

| Phase | Model | Components | Est. Cost Per |
|-------|-------|------------|---------------|
| Analysis | Opus | 1 page | ~$0.50-0.80 |
| Implementation (parallel) | Sonnet | Per component | ~$0.30-0.50 each |
| Verification | Haiku | 1 page | ~$0.05-0.10 |
| Fixes (if needed) | Sonnet | Per component | ~$0.20-0.40 each |

**Example: Page with 4 components (1-2 iterations)**
- Analysis: ~$0.60
- Implementation: 4 × $0.40 = ~$1.60 (parallel)
- Verification: 2 × $0.08 = ~$0.16
- Fixes: 2 × $0.30 = ~$0.60
- **Total: ~$3-4**

**Note:** Parallel building is more expensive upfront (4 Sonnet agents running simultaneously) but much faster. Sequential building is cheaper but slower.

---

## Built-in Optimizations

1. **Screenshot + Figma combo** - Visual context with precise specs
2. **Single Figma MCP call** - Get all components at once
3. **Parallel building** - Build multiple components simultaneously (optional)
4. **Haiku for verification** - 90% cheaper than Sonnet for comparisons
5. **Explicit checklists** - No ambiguity in verification
6. **Early exit** - Stop when pixel-perfect
7. **Component reuse** - Checks for existing similar components first

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

## Final Output

Provide a comprehensive summary:

- **Page built:** [Page Name]
- **Components created:** List all components with file paths
- **Build approach:** Parallel or Sequential
- **Iterations needed:** X of 3
- **Verification status:** PIXEL-PERFECT or remaining issues
- **Estimated cost breakdown:**

  | Phase | Model | Count | Est. Cost |
  |-------|-------|-------|-----------|
  | Analysis | Opus | 1 | ~$0.60 |
  | Implementation | Sonnet | X components | ~$0.40 × X |
  | Verification | Haiku | X iterations | ~$0.08 × X |
  | Fixes | Sonnet | X components | ~$0.30 × X |
  | **Total** | | | **~$X.XX** |

- **Manual checks:** Any recommended follow-up actions

**Cost Reference:**
- Opus page analysis: ~$0.50-0.80 per run
- Sonnet component build: ~$0.30-0.50 per component
- Haiku page verification: ~$0.05-0.10 per run
- Sonnet component fix: ~$0.20-0.40 per component
- Opus re-analysis (if major issues): ~$0.50-0.80 per run
