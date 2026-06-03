# Skill 7: HTML Generator

> **When to use this:** Use this skill when a module content markdown file (`03-*-module-content.md`) has been approved (`#review: APPROVED`) and needs to be published as a standalone styled HTML document with an interactive table of contents, embedded diagrams, and a professional visual layout.

---

## How to Trigger This Skill

Copy the prompt below and paste it into your Research & Learning Hub project. Provide the path to the approved module content markdown file.

---

## The Prompt

```
SKILL: HTML Generator

The approved module content file is located at:
[PATH TO 03-*-MODULE-CONTENT.MD FILE]

---

Before generating the HTML, do the following:

1. Read the full markdown file and parse its structure:
   - Title (H1) and subtitle (H3)
   - Module content heading (H2)
   - Day sections (H3) — Day 1 through Day N
   - Each day's fields: Learning Objective, Core Idea, Why It Matters, How It Works
   - Supplemental Reading links
   - Blockquotes (diagram descriptions)
   - Mermaid code blocks
   - Hands-on Activity sections
   - Footer with version info
   - Outro / closing paragraph

2. Check for existing diagrams:
   - Scan the `04-*-visuals.md` file in the same output directory for any mermaid diagrams
   - If diagrams exist, use them for image generation
   - If no diagrams file exists, extract mermaid code blocks from the module content itself

---

Generate the HTML following these specifications:

### File naming
Save to: `outputs/[topic-slug]/03-[topic-slug]-module-content.html`
Images saved to: `outputs/[topic-slug]/images/`

### HTML structure
```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>[Title from markdown]</title>
  <style>/* embedded CSS */</style>
</head>
<body>
  <div class="container">
    <div class="review-badge">#review: APPROVED</div>
    <header>
      <h1>[Title]</h1>
      <p class="subtitle">[Subtitle]</p>
    </header>
    <p class="module-heading">Module Content</p>
    <!-- TABLE OF CONTENTS -->
    <div class="index-section">
      [Auto-generated TOC with links to each day]
    </div>
    <!-- DAY CARDS -->
    <section class="day-card">
      <h2 id="day-X">Day X: [Topic]</h2>
      [Fields: Learning Objective, Core Idea, Why It Matters, How It Works]
      [Supplemental Reading]
      [Blockquote]
      [Diagram image if applicable]
      [Hands-on Activity]
    </section>
    <!-- FOOTER -->
  </div>
</body>
</html>
```

### CSS design requirements
- System font stack (Segoe UI, system-ui, sans-serif)
- Light gray background (#f5f7fa), white day cards with rounded corners and subtle shadow
- Dark blue gradient header (#1a365d → #2b6cb0) with white text
- Fixed review status badge in top-right corner (green for APPROVED)
- Color-coded field labels: blue (Objective), green (Core Idea), orange (Why), purple (How), amber (Hands-on)
- Supplemental Reading in a light gray bordered box
- Blockquotes with left blue accent border and light blue background
- Mermaid diagrams rendered as PNG images in dark-themed containers
- Table of Contents as a white card with responsive 2-column grid
- Responsive layout for mobile (<640px)

### Mermaid diagram handling
- Extract each mermaid code block from the markdown or visuals file
- Render to PNG using the mermaid.ink API (`https://mermaid.ink/img/{base64}`):
  - Base64 encode the diagram text (remove common leading whitespace)
  - Use URL-safe base64 (replace + with -, / with _, strip = padding)
- Save as `images/diagram-1.png` through `images/diagram-N.png`
- Replace mermaid code blocks in HTML with `<img src="images/diagram-N.png">`
- Do NOT include the Mermaid.js CDN script — images are self-contained

### Table of Contents
- Generate a `#day-X` anchor for each day heading
- Build a bullet list or grid of links: "Day X" (linked) + topic description
- Place between the "Module Content" heading and the first day

### What NOT to do
- Do not use external CSS frameworks (no Bootstrap, Tailwind, etc.)
- Do not include JavaScript beyond the initial page load (no JS runtime for diagrams)
- Do not modify the source markdown file
- Do not alter the content or wording from the original markdown
- Do not paginate — single scrollable page
- Do not add print-specific CSS unless requested

---

Constraints:
- Every day heading must have an `id="day-X"` attribute
- All links open in a new tab (`target="_blank"`)
- HTML entities must be properly encoded (&amp; &lt; &gt; &quot;)
- The review badge must reflect the `#review:` status from the source file
- Version, date, and author from the markdown footer must appear in the HTML footer
- The closing outro paragraph must be preserved

---

End with this exact summary:
"HTML file generated at [filepath]. Images saved to [images path]. Open the HTML file in any browser to preview."
```

---

## What You Will Get Back

| Output | Description |
|---|---|
| **HTML Document** | Standalone styled HTML file with embedded CSS |
| **Diagram Images** | PNG renders of all mermaid diagrams |
| **Table of Contents** | Auto-generated index with clickable day links |
| **Summary** | File paths for the HTML and images directory |

---

## Your Action After Receiving the HTML

Once the HTML is returned, do one of the following:

- ✅ **Confirm** — "Looks good, ready to publish"
- ✏️ **Request CSS changes** — "Change the header color to [color]" or "Make the index 3 columns instead of 2"
- 🔧 **Fix content** — "Fix the link for Day 3" or "Update the review badge to NEEDS FIX"

---

## Important Notes

- **The mermaid.ink API is a live service** — diagrams are fetched from `https://mermaid.ink`. If the service is unavailable, the images will not render. For offline use, pre-render diagrams locally using `@mermaid-js/mermaid-cli`.
- **No server-side processing** — the HTML file is fully client-side and can be opened directly in any browser.
- **One HTML file per module content file** — each approved markdown file produces its own standalone HTML.

---

## Example Trigger

```
SKILL: HTML Generator

The approved module content file is located at:
outputs/glue-athena-airflow/03-glue-athena-airflow-module-content.md
```

---

*Skill 7 of 7 — Research & Learning Hub*
*Input comes from → Skill 3: Module Content Builder (#review: APPROVED)*
*Output feeds into → Published deliverable (HTML format)*
