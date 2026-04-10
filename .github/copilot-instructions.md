# Gantt Chart Project - AI Coding Instructions

## Project Overview
A standalone, single-file HTML/JavaScript Gantt chart application using D3.js for visualization. No build system—all CSS, HTML, and JS are embedded in one file.

## Architecture

### Single-File Structure
- **[gantt10.html](../gantt10.html)** - Current production version (all-in-one HTML + CSS + JS)
- **[archive/](../archive/)** - Previous numbered versions for reference/rollback

### Key Components (all in `gantt10.html`)
| Section | Lines | Purpose |
|---------|-------|---------|
| CSS Styles | 7-200 | All styling including `.task-rect`, `.milestone`, modals |
| HTML Structure | 204-340 | Layout with `#container`, `#chart`, modals |
| D3.js Rendering | 350-800 | `renderChart()`, scales, axes, bar gradients |
| Modal Logic | 800-1100 | Task add/edit modal, person import/export |

### Data Model (JSON in localStorage)
```json
{
  "tasks": [{
    "person": "bob",
    "task": "sales report",
    "start": "2025-03-01",
    "end": "",           // Optional; defaults to view window end
    "capacityImpact": "high|medium|low",
    "milestones": { "phase 1": "2025-05-01" }
  }]
}
```
- Storage key: `gantt_tasks_v1`
- Tasks auto-sort by: person → start date → task name

## Code Conventions

### D3.js Patterns
- Use `d3.utcParse("%Y-%m-%d")` and `d3.utcFormat("%Y-%m-%d")` for dates
- SVG built via chained D3 selections; always clear with `svg.selectAll('*').remove()` before re-render
- Gradient IDs follow pattern: `g${taskId}_${index}`

### State Management
- `currentData` - Parsed JSON object with tasks array
- `hiddenKeys` - Set of task keys for toggle visibility (key = `person||task||start||end`)
- `capacityImpactState` - Object tracking High/Medium/Low filter checkboxes
- `editIndex` - `null` for add, task index for edit mode

### Key Functions
- `renderFromTextarea(rebuildToggles)` - Parse JSON → render chart
- `renderChart(tasksRaw, rebuildTogglePanel)` - Core D3 rendering logic
- `sortTasksArray(tasks)` - In-place sort by person/start/task
- `keyFor(t)` - Generate unique key for toggle state
- `openQuickModalForEdit(idx)` / `openQuickModalForAdd()` - Modal controllers

## UI Conventions

### Visual Styling
- Gradient background: `#007a9b → #004d8b → #002b55`
- Panels use `backdrop-filter: blur(8px)` with semi-transparent backgrounds
- Task bars use linear gradients with fade effect at truncation points
- Milestones rendered as "pin" icons (circle head + line stem)

### Capacity Impact Classes
```css
.task-rect.impact-high   /* Bold stroke + glow filter */
.task-rect.impact-low    /* Reduced opacity (0.45) */
```

## Development Workflow

### Versioning Pattern
When making significant changes:
1. Copy current `ganttN.html` → `archive/ganttN.html`
2. Continue editing the main file (increment version in filename if desired)

### Testing Changes
1. Open `gantt10.html` directly in browser (no server required)
2. Modify JSON in textarea → "Save & Render"
3. Use "Reset Example" to restore default data
4. Check localStorage via DevTools → Application → Local Storage

### Common Modifications
- **Add new filter**: Add checkbox in `#right` panel, update state object, add condition in `renderChart()`
- **Change color palette**: Modify `customPalette` array (9 colors used by `d3.scaleOrdinal`)
- **Adjust date range**: Modify `getViewWindow()` switch cases

## External Dependencies
- **D3.js v7** loaded from CDN: `https://d3js.org/d3.v7.min.js`
- No other runtime dependencies

## Gotchas
- All dates must be UTC (`d3.utcDay`, `d3.utcParse`) to avoid timezone shifts
- Empty `end` date defaults to view window end, not infinity
- Modal forms use native HTML5 validation (`required` attribute)
- Task index changes after sort—always re-parse after modifications
