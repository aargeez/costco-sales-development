# Subagent: Figma Design Extractor

## Purpose
Extract design tokens, component inventory, asset URLs, and structural metadata from a Figma file using the Figma MCP tools. Produces the foundational data contract that all downstream subagents consume.

## Required Inputs
- `figma_url`: full Figma design URL (e.g., `https://figma.com/design/:fileKey/:fileName?node-id=:nodeId`).
- `file_key`: extracted from URL.
- `node_id`: target frame/page node ID from URL.

## Tools Used (Figma MCP)
1. `get_metadata` — extract frame/node tree structure, layer names, positions, sizes.
2. `get_design_context` — pull reference code, screenshots, and contextual metadata per node.
3. `get_variable_defs` (optional) — pull Figma variables/design tokens if defined.

## Execution Steps
1. Call `get_metadata` on the root `node_id` to discover the structural tree.
2. If `get_design_context` on root returns "too large", split into child node IDs and fetch individually.
3. For each sub-node, call `get_design_context` with `clientLanguages: "typescript"` and `clientFrameworks: "react,next"`.
4. Parse the design context response to extract:
   - **Color tokens**: map Figma style names to hex values (e.g., `Blues/Costco Blue: #005DAB`).
   - **Typography tokens**: font family, weight, size, line-height, letter-spacing per style.
   - **Elevation/shadows**: shadow definitions from Figma effects.
   - **Component inventory**: each distinct component instance with its name, variants, and Figma properties.
   - **Asset URLs**: image/SVG download URLs (7-day expiry).
   - **Screenshots**: node screenshots for visual reference.
5. Consolidate into structured outputs.

## Outputs
- `design_tokens`: JSON object with `colors`, `typography`, `shadows`, `spacing` sections.
- `component_inventory`: list of components with name, variants, Figma node ID, properties, and parent-child relationships.
- `asset_manifest`: list of `{ name, url, type: 'svg' | 'png' | 'icon' }` entries.
- `screenshots`: list of `{ nodeId, name, imageUrl }` for visual reference.
- `page_structure`: hierarchical layout tree (which components compose which frames).
- Confidence: high | medium | low
- Unresolved: list of nodes that failed extraction or returned partial data.

## Failure Modes
- If Figma MCP auth fails → escalate with error message; do not proceed.
- If node is too large and splitting still fails → return partial results with explicit gaps.
- If design tokens are ambiguous (unnamed colors) → infer names from usage context and flag as `low_confidence`.

## Quality Gate
Parent accepts when:
- At least 80% of visible nodes have extracted design context.
- Color and typography tokens cover all styles referenced in the design.
- Component inventory includes all instance nodes with their Figma properties.
