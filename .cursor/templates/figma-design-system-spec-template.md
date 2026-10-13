# Figma Design System Specification

## Metadata
| Field | Value |
|-------|-------|
| Figma File | `{figma_url}` |
| File Key | `{file_key}` |
| Target Node | `{node_id}` |
| Extraction Date | `{timestamp}` |
| Extractor Confidence | `{confidence}` |

---

## Design Tokens

### Color Palette
| Token Name | Hex | MUI Mapping | Usage |
|------------|-----|-------------|-------|
| {color_name} | {hex} | `palette.{slot}` | {usage_description} |

### Typography Scale
| Style Name | Font | Weight | Size | Line Height | Letter Spacing | MUI Variant |
|------------|------|--------|------|-------------|----------------|-------------|
| {style_name} | {font_family} | {weight} | {size}px | {line_height}px | {letter_spacing} | `{mui_variant}` |

### Shadows / Elevation
| Token Name | Definition | MUI Mapping |
|------------|------------|-------------|
| {shadow_name} | `{shadow_css}` | `designTokens.shadows.{key}` |

### Spacing
| Token | Value | Usage |
|-------|-------|-------|
| {name} | {value}px | {where_used} |

---

## Component Inventory

### Atoms
| Component | Figma Node | Variants | Props Identified |
|-----------|------------|----------|------------------|
| {name} | `{node_id}` | {variant_list} | {props_list} |

### Molecules
| Component | Figma Node | Composed Of | Props Identified |
|-----------|------------|-------------|------------------|
| {name} | `{node_id}` | {children} | {props_list} |

### Organisms
| Component | Figma Node | Composed Of | Props Identified |
|-----------|------------|-------------|------------------|
| {name} | `{node_id}` | {children} | {props_list} |

---

## Page Structure
```
Frame: {page_name}
├── {organism_1} (x:{x}, y:{y}, w:{w}, h:{h})
│   ├── {child_1}
│   └── {child_2}
├── {organism_2}
│   └── {child_3}
└── {organism_3}
    ├── {child_4}
    └── {child_5}
```

---

## Asset Manifest
| Asset | Type | URL | Expiry |
|-------|------|-----|--------|
| {name} | svg/png/icon | `{url}` | 7 days from extraction |

---

## Unresolved / Low Confidence
| Item | Issue | Suggested Resolution |
|------|-------|---------------------|
| {item} | {issue_description} | {suggestion} |
