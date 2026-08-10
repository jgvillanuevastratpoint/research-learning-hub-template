# Project Instructions

## Project: research-learning-hub-template

This repository contains structured training materials for Spring Boot and MongoDB.

---

## File Structure

```
input/springboot-mongo-training/
├── fundamentals.md      # 2-week beginner track
├── advanced.md          # 6-week advanced track
├── SPRINGBOOT-TRAINING.MD   # Original training doc (reference)
├── springboot-research.txt  # Original research doc (reference)
└── hands-on-training.txt    # Empty
```

---

## Training Tracks

| Track | Duration | Level | Prerequisites |
|-------|----------|-------|---------------|
| Fundamentals | 2 weeks | Beginner | Basic Java knowledge |
| Advanced | 6 weeks | Advanced | Fundamentals track completed |

---

## Markdown Conventions

### Mermaid Diagrams

- **Gantt charts**: Use weekdays only (Mon-Fri). Do not schedule tasks across weekends.
- **Colors**: Use muted, high-contrast colors for readability. Always define explicit `style` directives for each task.
  - Use `color:#212121` (dark text) on all fills for maximum contrast
  - Use light, desaturated fills: `#f5f5f5` (gray), `#e3f2fd` (blue), `#e8f5e9` (green), `#fff3e0` (orange), `#fce4ec` (pink), `#f3e5f5` (purple), `#e0f2f1` (teal)
  - Use matching stroke colors with moderate saturation: `#616161`, `#1565c0`, `#2e7d32`, `#e65100`, `#c62828`, `#6a1b9a`, `#00695c`
  - Avoid bright neon fills (`#f9f`, `#bbf`, `#bfb`) — they obscure text
- **Axis format**: Use `%a` for day-of-week display.
- **Flowcharts**: Style every node and subgraph explicitly. Use `fill` for background, `stroke` for border, `color` for text.

### Code Blocks

- Include all code snippets as requested by user
- Use proper language tags (`java`, `yaml`, `xml`)
- Keep package naming consistent: `com.example.catalogservice` for fundamentals, `com.wallet.*` for advanced

### Content Guidelines

- Prerequisites are required for advanced track (links back to fundamentals)
- Include Mermaid architecture diagrams for visual continuity
- Include milestone checklists at the end of each section
- Link between tracks at the end of fundamentals.md

---

## Style Notes

- Be concise and direct
- Use tables for structured comparisons
- Use task lists `[ ]` for milestones
- Keep headings consistent (H1 for title, H2 for sections, H3 for subsections)
