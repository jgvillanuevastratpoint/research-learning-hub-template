# Skill 7: HTML Generator

> **When to use this:** Use this skill when a module content markdown file (`03-*-module-content.md`) has been approved (`#review: APPROVED`) and needs to be published as a standalone styled HTML document with a metadata card, training timeline Gantt chart, table-of-contents table, embedded diagrams, and a professional visual layout.

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
   - Tags front matter (YAML between `---` delimiters)
   - Title (H1) and subtitle (H3)
   - Metadata table (if present — extract fields like Skill Domains, Duration, Pace)
   - Gantt chart (mermaid code block with phase sections)
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

### HTML structure (top-to-bottom order)
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
    <!-- REVIEW BADGE -->
    <div class="review-badge">#review: STATUS</div>

    <!-- HEADER BANNER -->
    <header>
      <h1>[Title]</h1>
      <p class="subtitle">[Subtitle]</p>
    </header>

    <!-- METADATA CARD -->
    <div class="metadata-card">
      <table class="metadata-table">
        [Title, Version, Date, Author, Skill Domains, Duration, Pace]
      </table>
      <!-- tags: topic-slug, skill-domains, audience, difficulty, ... -->
    </div>

    <!-- TRAINING TIMELINE — GANTT CHART -->
    <div class="gantt-block">
      <div class="gantt-label">Training Timeline</div>
      <img src="images/gantt.png" alt="Training timeline Gantt chart">
    </div>

    <!-- TABLE OF CONTENTS -->
    <p class="section-heading">Table of Contents</p>
    <table class="toc-table">
      <thead>
        <tr>
          <th>#</th>
          <th>Topic</th>
          <th>Skill Domain</th>
          <th>Delivery Method</th>
          <th>Suggested Pace</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td><a href="#day-1">Day 1</a></td>
          <td>[Topic]</td>
          <td>[Domain]</td>
          <td>[Method]</td>
          <td>[Pace]</td>
        </tr>
        [...repeat for each day]
      </tbody>
    </table>

    <!-- DAY CARDS -->
    <section class="day-card" id="day-1">
      <h2>Day 1: [Topic]</h2>
      [Fields: Learning Objective, Core Idea, Why It Matters, How It Works]
      [Supplemental Reading]
      [Blockquote]
      [Diagram image if applicable]
      [Hands-on Activity]
    </section>
    [...repeat for each day]

    <!-- FOOTER -->
    <footer>
      [Version, Date, Author]
    </footer>

    <!-- OUTRO -->
    <div class="outro">
      [Closing paragraph]
    </div>
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
- Responsive layout for mobile (<640px)

### Metadata card CSS
- White card with a subtle border and rounded corners
- Metadata table: two columns (Field / Value), minimal styling, muted gray labels
- Hidden HTML comment with tags for scraping

### Gantt image CSS
- Dark-themed container similar to mermaid blocks
- Image at full width with `max-width: 100%; height: auto;`
- "Training Timeline" label above the image

### TOC table CSS
- Full-width table with bordered cells
- Sticky `<thead>` with dark blue background and white text
- Alternating row shading (white / light gray)
- Day numbers are anchor links (`#day-X`)
- Hover highlight on rows

### Mermaid diagram handling
- Extract each mermaid code block from the markdown or visuals file
- For the Gantt chart: extract the `gantt` mermaid block specifically and render separately
- Render to PNG using the mermaid.ink API (`https://mermaid.ink/img/{base64}`):
  - Base64 encode the diagram text (remove common leading whitespace)
  - Use URL-safe base64 (replace + with -, / with _, strip = padding)
- Save as `images/diagram-1.png` through `images/diagram-N.png` for content diagrams
- Save Gantt chart as `images/gantt.png`
- Replace mermaid code blocks in HTML with `<img src="images/diagram-N.png">`
- Do NOT include the Mermaid.js CDN script — images are self-contained

### Table of Contents
- Extract from the markdown: Day number, Topic, Skill Domain, Delivery Method, Suggested Pace
- If the markdown has no domain/method/pace columns, generate the TOC with just "#" and "Topic" columns
- Build an HTML `<table>` with `<thead>` and `<tbody>`
- Each day number links to `#day-X`

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
- The metadata card must include both the visible table and a hidden HTML comment with tags
- The Gantt chart image must be placed between the metadata card and the TOC table
- The TOC must be a proper HTML table with thead/tbody, not a bullet list or div grid

---

End with this exact summary:
"HTML file generated at [filepath]. Images saved to [images path]. Open the HTML file in any browser to preview."
```

---

## What You Will Get Back

| Output | Description |
|---|---|
| **HTML Document** | Standalone styled HTML file with embedded CSS |
| **Metadata Card** | Visible table with title, domains, duration, pace + hidden HTML comment with tags |
| **Gantt Chart Image** | PNG render of the phase-block training timeline |
| **Table of Contents** | HTML table with linked day numbers, topic, domain, delivery method, and pace |
| **Diagram Images** | PNG renders of all content mermaid diagrams |
| **Summary** | File paths for the HTML and images directory |

---

## Your Action After Receiving the HTML

Once the HTML is returned, do one of the following:

- ✅ **Confirm** — "Looks good, ready to publish"
- ✏️ **Request CSS changes** — "Change the header color to [color]" or "Make the TOC table sortable"
- 🔧 **Fix content** — "Fix the link for Day 3" or "Update the review badge to NEEDS FIX"

---

## Important Notes

- **The mermaid.ink API is a live service** — diagrams are fetched from `https://mermaid.ink`. If the service is unavailable, the images will not render. For offline use, pre-render diagrams locally using `@mermaid-js/mermaid-cli`.
- **No server-side processing** — the HTML file is fully client-side and can be opened directly in any browser.
- **One HTML file per module content file** — each approved markdown file produces its own standalone HTML.
- **Tags in HTML comment** — placed in the metadata card as `<!-- tags: ... -->` for automated scraping and backlink generation.

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
