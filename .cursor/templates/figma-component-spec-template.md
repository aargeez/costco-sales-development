# Component Specification: {ComponentName}

## Metadata
| Field | Value |
|-------|-------|
| Figma Node | `{node_id}` |
| Atomic Level | `{atom \| molecule \| organism \| template}` |
| File Path | `src/components/{level}/{ComponentName}/index.tsx` |

---

## Figma Properties → React Props

| Figma Property | React Prop | Type | Default | Required |
|----------------|------------|------|---------|----------|
| {figma_prop} | `{react_prop}` | `{type}` | `{default}` | {yes/no} |

---

## TypeScript Interface

```typescript
export interface {ComponentName}Props {
  /** {description} */
  {prop}: {type};
}
```

---

## Variants / States

| State | Trigger | Visual Change | Implementation |
|-------|---------|---------------|----------------|
| Default | — | Base appearance | Default styling |
| Hover | Mouse enter | {hover_change} | `&:hover` in styled() |
| Active | Mouse down | {active_change} | `&:active` in styled() |
| Disabled | `disabled={true}` | {disabled_change} | `&.Mui-disabled` |
| Loading | `isLoading={true}` | {loading_change} | Conditional CircularProgress |

---

## Composition

### Used Components (children)
- {child_component_1}
- {child_component_2}

### Used By (parents)
- {parent_component_1}
- {parent_component_2}

---

## Styling Approach
- [ ] Uses `styled()` API
- [ ] References `theme.palette.*`
- [ ] References `theme.typography.*`
- [ ] No hardcoded hex values
- [ ] No raw CSS classes
- [ ] `shouldForwardProp` for custom props

---

## Accessibility
- [ ] Semantic HTML element
- [ ] `aria-label` on icon-only actions
- [ ] Keyboard navigable
- [ ] Focus indicator visible
- [ ] Screen reader friendly

---

## State Management
- [ ] Uses `useState` for internal state
- [ ] Controlled component pattern (value + onChange)
- [ ] Falls back to uncontrolled when parent doesn't provide value
- [ ] Event handlers properly typed
