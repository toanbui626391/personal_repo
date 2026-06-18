# Configuration Options Reference

Configuration options are evaluated in order of priority (lowest to highest):
**Defaults → JS/JSON Config File → YAML Front-matter → CLI Arguments**

In front-matter and JSON configs, keys correspond to CLI flags but use **snake_case** (e.g., `--pdf-options` becomes `pdf_options` and `--body-class` becomes `body_class`).

---

## Front-matter Config Format

YAML front-matter can be embedded at the top of the Markdown file:

```markdown
---
dest: ./output.pdf
stylesheet:
  - https://cdnjs.cloudflare.com/ajax/libs/github-markdown-css/4.0.0/github-markdown.min.css
body_class: markdown-body
pdf_options:
  format: A4
  margin: 15mm 20mm
  printBackground: true
---
# Document Title
...
```

## Available Options

*   **`dest`**: Output destination path (can only be specified in config files or front-matter, not via CLI flags).
*   **`stylesheet`**: Array or path to local/remote CSS files to style the PDF.
*   **`css`**: Raw inline CSS string.
*   **`body_class`**: CSS class(es) for the `<body>` tag.
*   **`pdf_options`**: Underlying Puppeteer page PDF generation options (format, orientation, margins, headers, footers).
*   **`launch_options`**: Underlying Puppeteer Chromium launch options.

---

## Layout Control & Customization

*   **Page Breaks:** Add `<div class="page-break"></div>` to force a page break.
*   **Margins:** Set `pdf_options.margin` using a CSS-like shorthand string (e.g., `"10mm 20mm"`).
*   **Headers & Footers:** Set `pdf_options.displayHeaderFooter` to `true`, and configure HTML templates:
    *   `pdf_options.headerTemplate`
    *   `pdf_options.footerTemplate`
    *   *Note:* Margins must be configured large enough to accommodate headers/footers to prevent overlap.
    *   *Supported CSS classes for auto-injection:* `date`, `title`, `url`, `pageNumber`, `totalPages`.
    *   *Template example:*
        ```yaml
        pdf_options:
          displayHeaderFooter: true
          headerTemplate: '<div style="font-size: 8px; margin: 0 auto;">Document Title</div>'
          footerTemplate: '<div style="font-size: 8px; margin: 0 auto;">Page <span class="pageNumber"></span> of <span class="totalPages"></span></div>'
        ```
