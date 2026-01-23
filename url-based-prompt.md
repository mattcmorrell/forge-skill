# Build Page from Figma URLs

Build a page from Figma using this workflow:

1. **Decompose**: For each URL I provide, use `get_metadata` to check if it contains child components. If it does, extract the main children (instances/frames, not primitives).

2. **Fetch specs**: For all discovered components (parent + children), call `get_design_context(nodeId)` and `get_screenshot(nodeId)` in parallel.

3. **Clarify (optional)**: If you detect ambiguity about behavior, data sources, or interactions, ask me questions. Otherwise make reasonable assumptions and proceed.

4. **Build in parallel**: Spawn separate agents to build each component simultaneously.

5. **Assemble**: Create a page component that imports and renders all components together, using the screenshot as layout reference.

---

**Screenshot and URLs:**
[Paste screenshot]

```
https://www.figma.com/design/3Vs1Y.../node-id=656-22960
https://www.figma.com/design/3Vs1Y.../node-id=660-24330
```
