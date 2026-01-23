---
name: forge
description: Forge pixel-perfect pages from Figma - creates new components or refines existing ones using screenshot context and component specs
argument-hint: ""
allowed-tools: Read, Write, Edit, Glob, Grep, Bash, Task, TodoWrite, mcp__figma-desktop__*, mcp__playwright__*
---

# /forge - Full Page Builder from Figma

Forge builds complete pages from Figma by combining screenshot context (composition/layout) with precise component specs from Figma MCP. Supports both creating new components and refining existing ones.

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
3. **Auto-Detect Existing** - Find which components already exist in codebase
4. **Analyze Composition** - Use screenshot to understand layout/spatial relationships
5. **Plan Page** - Create page structure with per-component actions (Opus)
6. **Build/Refine Components** - Create new or update existing with precise specs (Sonnet, can be parallel)
7. **Verify** - Compare against screenshot using Playwright (Haiku)
8. **Iterate** - Fix discrepancies until pixel-perfect

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

### Phase 0: Setup (First Thing)

**Before doing anything else:**

1. **Clean up previous run:**
   ```bash
   rm -rf .forge/
   mkdir .forge/
   ```
   This ensures `.forge/` only contains data for the CURRENT run, preventing confusion between multiple forge sessions.

### Phase 1: Capture Figma Selection (Happens First)

1. **Wait for user confirmation** that Figma selection is ready (e.g., "ready", "done")

2. **Immediately fetch from Figma MCP:**
   ```
   mcp__figma-desktop__get_design_context()
   ```
   This returns ALL selected components with their specs (dimensions, colors, typography, spacing).

3. **Save specs to file:**
   - Write specs to `.forge/figma-specs.json`
   - This keeps specs out of context, reduces token bloat

4. **Confirm to user:**
   "✓ Got specs for [N] components. You can now work on other things in Figma."

### Phase 2: Get Screenshot (Happens Second)

**IMMEDIATELY after confirming Figma capture, prompt for screenshot:**

```
Step 2: Upload screenshot
Please attach a full-page screenshot showing the complete layout and composition of your page.
```

**STOP and wait for screenshot upload. Do NOT do anything else until screenshot is received.**

**After screenshot is uploaded:**
- Save screenshot to `.forge/screenshot.png`
- This allows verification agents to reference it directly without context bloat

### Phase 3: Post-Screenshot Setup

**Only after screenshot is uploaded:**

1. **Verify dev server** is running, start if needed (`npm run dev`)

2. **Auto-detect existing components:**
   - Look in `/src/components/` and `/src/pages/` using Glob
   - Match by name similarity to Figma component names
   - Report findings: "Found existing: Header.tsx, Sidebar.tsx" and "Need to create: Card.tsx, Footer.tsx"
   - This detection happens automatically - no user choice needed

### Summary

At this point you have:
- **`.forge/figma-specs.json`** - Exact component specs
- **`.forge/screenshot.png`** - Original design screenshot
- **Dev server** running
- **Component detection complete** - know which exist vs. need creation

All data is saved to files - lean context, precise specs. User's Figma selection can change - you already have the data!

---

## Step 2: ANALYZE & PLAN (Opus)

Spawn a **Task agent with model: opus** to analyze and create implementation plan.

**Provide to the agent:**
- Component detection results (which components exist vs. need creation)
- Path to screenshot: `.forge/screenshot.png`
- Path to Figma specs: `.forge/figma-specs.json`

**Agent reads from files** - keeps context lean, ensures fresh data.

### Analysis Phase

Analyze both inputs together:

1. **Screenshot Analysis** (Composition/Layout):
   - Read `.forge/screenshot.png`
   - Identify the page structure (grid, flex, columns)
   - Note spatial relationships (how components are positioned relative to each other)
   - Understand layout flow (header → content → footer, sidebar + main, etc.)
   - Identify breakpoints and responsive behavior hints

2. **Figma Specs Analysis** (Component Details):
   - Read `.forge/figma-specs.json`
   - Parse specs for each selected component
   - Extract precise dimensions, colors (hex values), typography (font, size, weight, line-height)
   - Note spacing/padding values
   - Identify interactive states if present

3. **Component Mapping**:
   - Match screenshot regions to Figma component specs
   - Name each component appropriately (Header, Sidebar, MainContent, etc.)
   - Create hierarchy (Page → Layout → Components)

4. **Container Extraction** (Critical):
   - **Check if parent/group has container styling** (background, border-radius, shadow, padding)
   - If yes, extract container as separate component: e.g., `PerformancePageCard.tsx`
   - Container styling includes: background colors, border-radius, box-shadow, padding, borders
   - Children become separate components nested inside: `<PageCard><ProfileHeader /><VitalsSidebar />...</PageCard>`
   - **This prevents missing card/container styling when selecting grouped components**

### Planning Phase

Create a comprehensive build plan and save to `.forge/plan.md`:

**Page Structure:**
```typescript
// Example structure with container extraction
<PageLayout>
  <PerformancePageCard> {/* Container from parent group - node-id: 1:100 */}
    <ProfileHeader /> {/* From Figma node-id: 1:234 */}
    <BodyLayout>
      <VitalsSidebar /> {/* From Figma node-id: 2:345 */}
      <PerformanceContent /> {/* From Figma node-id: 3:456 */}
    </BodyLayout>
  </PerformancePageCard>
</PageLayout>
```

**Container Extraction Example:**
If the parent group has:
- Background: white (#FFFFFF)
- Border-radius: 16px
- Box-shadow: 0px 4px 12px rgba(0,0,0,0.1)
- Padding: 24px

Extract this as `PerformancePageCard.tsx` component, then nest the children inside it.

**For Each Component:**
- File path (create new or refine existing)
- **Action:** CREATE (if doesn't exist) or REFINE (if exists)
- Component interface/props
- Exact styling specs from Figma:
  - Typography: font, size, weight, line-height, color (hex)
  - Spacing: padding, margins, gaps (exact px values)
  - Colors: backgrounds, borders, text (hex values)
  - Dimensions: width, height
- Layout approach (flex, grid, positioning)
- **If REFINE:** List what needs to change (only styling/structure, preserve logic)

**Note:** A single page can have mixed actions - some components created, others refined. Example: "Create Card.tsx (new), Refine Header.tsx (exists), Refine Sidebar.tsx (exists)"

**Build Order:**
- **If container extracted:** Build container first, then children in parallel
  - Container must exist before children (they're nested inside it)
  - Example: Build `PerformancePageCard.tsx` → then `ProfileHeader`, `VitalsSidebar`, `PerformanceContent` in parallel
- **If no container:** Can all components be built in parallel? Or do some depend on others?
- Recommend parallel if independent, sequential only if dependencies exist

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
Task 1 (Sonnet): Build/Refine Header component
Task 2 (Sonnet): Build/Refine Sidebar component
Task 3 (Sonnet): Build/Refine MainContent component
Task 4 (Sonnet): Build/Refine Footer component
```

**Each agent receives:**
- Component name (e.g., "Header")
- Action: CREATE or REFINE (determined from plan)
- Instruction to read `.forge/plan.md` for their specific component section
- Instruction to read `.forge/figma-specs.json` for their component's exact specs

**Agents do NOT receive full context** - they read only what they need from files. This keeps context lean and ensures precise specs.

**Note:** In a single run, some agents may create new components while others refine existing ones. Each agent follows the appropriate workflow based on its assigned action.

### CREATE Mode (New Components)

Each agent:
- Reads `.forge/plan.md` for its component section
- Reads `.forge/figma-specs.json` for exact specs (dimensions, colors, spacing, typography)
- Creates its component file
- Implements based on precise Figma specs
- Uses existing design tokens from `/src/index.css`
- Exports properly

### REFINE Mode (Existing Components)

Each agent:
- Reads `.forge/plan.md` for its component section and what needs to change
- Reads `.forge/figma-specs.json` for exact target specs
- **Reads the existing component file**
- **Preserves all logic:** event handlers, state, effects, business logic, data fetching
- **Preserves props interface** (unless structure changed in Figma)
- **Only updates styling to match Figma specs:**
  - Typography (font-family, font-size, font-weight, line-height, color)
  - Spacing (padding, margin, gap)
  - Colors (background, border)
  - Dimensions (width, height)
  - Layout (flex, grid properties)
- **Uses Edit tool for surgical changes** (not Write - preserve existing code)
- **If JSX structure must change** (e.g., add/remove elements to match Figma):
  - Make minimal changes
  - Preserve existing event handlers and refs
  - Keep all logic intact

### Sequential Implementation (If Dependencies or Container Extraction)

**If container was extracted:**
1. First: Build container component (e.g., `PerformancePageCard.tsx`) - one Sonnet agent
2. Then: Build children in parallel (they depend on container existing)

**If other dependencies exist:**
Spawn **one Sonnet agent** that builds in order:
1. Build/refine page layout structure first
2. Build/refine components one by one
3. Integrate components into layout

**Note:** Container + parallel children is a hybrid approach - sequential container, then parallel children.

### All Implementations Must:
- Follow existing codebase patterns (check `/src/components/` for examples)
- Use TypeScript with proper types
- Use design tokens (colors, spacing from CSS variables)
- Export components via `index.ts` files
- Follow the exact specs from the Figma plan
- **In REFINE mode:** Preserve all existing functionality

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

Spawn a **Task agent with model: haiku** with Playwright access.

**Provide to agent:**
- Path to original screenshot: `.forge/screenshot.png`
- Path to Figma specs: `.forge/figma-specs.json`

1. **Take implementation screenshot:**
   - Navigate to the page via Playwright
   - Take full-page screenshot
   - Save to `.forge/implementation.png`

2. **Compare against original screenshot:**
   - Read `.forge/screenshot.png` (original Figma design)
   - Compare to `.forge/implementation.png` (current implementation)
   - Check layout structure (does it match?)
   - Check component positioning (are things in the right places?)
   - Check spacing between components

3. **Verify against Figma specs checklist:**
   - Read `.forge/figma-specs.json` for exact values
   - [ ] Typography matches (font, size, weight, line-height, color - exact values)
   - [ ] Colors match (backgrounds, text, borders - exact hex values)
   - [ ] Dimensions match (widths, heights - exact px values)
   - [ ] Spacing matches (padding, margins, gaps - exact px values)
   - [ ] All components present

4. **Output verdict:**
   - **PIXEL-PERFECT** - Matches screenshot and all specs pass
   - **NEEDS WORK** - List specific failures with exact values (e.g., "Padding is 16px, should be 24px")

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

**Note:**
- Parallel building is more expensive upfront (4 Sonnet agents running simultaneously) but much faster. Sequential building is cheaper but slower.
- Refinement mode is typically cheaper than create mode since changes are surgical (Edit tool vs Write tool, preserving existing code)

---

## Built-in Optimizations

1. **File-based workflow** - Saves specs/screenshots to `.forge/` directory, keeps context lean
2. **Focused agent context** - Each agent reads only its component's specs, not all data
3. **Auto-detection** - Automatically determines which components to create vs. refine
4. **Container extraction** - Extracts card/wrapper styling from parent groups automatically
5. **Mixed operations** - Can create new and refine existing in single run
6. **Screenshot + Figma combo** - Visual context with precise specs
7. **Single Figma MCP call** - Get all components at once
8. **Parallel building** - Build multiple components simultaneously (optional)
9. **Haiku for verification** - 90% cheaper than Sonnet for comparisons
10. **Explicit checklists** - No ambiguity in verification, exact values required
11. **Early exit** - Stop when pixel-perfect
12. **Surgical refinements** - Preserves logic, only updates styling (Edit tool)

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
- **Components created:** List new components with file paths
- **Components refined:** List updated components with file paths
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

---

## Cleanup

**After successful completion:**

1. **Add `.forge/` to `.gitignore`** if not already present:
   ```bash
   echo ".forge/" >> .gitignore
   ```

2. **Optional: Remove `.forge/` directory** if you want to clean up:
   ```bash
   rm -rf .forge/
   ```

**Note:**
- The `.forge/` directory contains temporary files for the CURRENT forge run only
- Each forge run automatically cleans and recreates `.forge/` at startup
- Safe to delete after completion, or leave it for debugging
- Add to `.gitignore` to avoid committing temporary files
