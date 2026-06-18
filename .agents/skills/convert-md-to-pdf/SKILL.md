---
name: convert-md-to-pdf
description: Use this skill to convert Markdown files to PDF using the md-to-pdf CLI package. It supports advanced configuration, styles, headers, and page breaks.
---

# Convert Markdown to PDF Skill

**Agent Context & Tooling:**
The user relies on `md-to-pdf` to convert Markdown documents (e.g., CVs, technical specifications) into high-quality PDFs. This tool leverages Marked for Markdown-to-HTML conversion, Puppeteer (headless Chromium) for rendering, and highlight.js for code syntax highlighting.

---

## ⚠️ CRITICAL: Non-TTY / Agent Environment Requirement

**ALWAYS** redirect stdin from `/dev/null` when running `md-to-pdf` from a non-interactive shell (e.g., an AI agent, CI/CD pipeline, or script). Without this, the CLI will **hang indefinitely** waiting for stdin because it detects it is not running in an interactive terminal (TTY).

### The Correct Command (Always Use This)

```bash
/opt/homebrew/bin/md-to-pdf <file.md> < /dev/null
```

**Root Cause:** When `md-to-pdf` is not attached to a TTY, it waits for piped content on stdin. Redirecting from `/dev/null` immediately signals EOF, so the CLI proceeds to process the file argument instead.

---

## Instructions for PDF Conversion

### 1. Basic Usage & Commands
*   **Bin Path:** `/opt/homebrew/bin/md-to-pdf` on macOS (globally installed via npm).
*   **Shorthand command:** `md2pdf` (same binary, same behavior).
*   **Standard command (interactive terminal):**
    ```bash
    md-to-pdf <file.md>
    ```
*   **Agent/CI/CD command (stdin fix required):**
    ```bash
    /opt/homebrew/bin/md-to-pdf <file.md> < /dev/null
    ```
*   **Watch Mode** (interactive only):
    ```bash
    md-to-pdf --watch document.md
    ```

### 2. Configuration Options (Command line vs Front-matter/JSON)
Config options are evaluated in order of priority (low to high): Defaults -> JS/JSON Config File -> YAML Front-matter -> CLI Arguments.

In front-matter and JSON configs, keys correspond to CLI flags but use **snake_case** (e.g., `--pdf-options` becomes `pdf_options` and `--body-class` becomes `body_class`).

#### Front-matter Config Format:
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

*   **`dest`**: Output destination path (can only be specified in config files or front-matter, not via CLI flags).
*   **`stylesheet`**: Array or path to local/remote CSS files to style the PDF.
*   **`css`**: Raw inline CSS string.
*   **`body_class`**: CSS class(es) for the `<body>` tag.
*   **`pdf_options`**: Underlying Puppeteer page PDF generation options (format, orientation, margins, headers, footers).
*   **`launch_options`**: Underlying Puppeteer Chromium launch options.

### 3. Layout Control & Customization
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

### 4. Programmatic API Usage
If integrating inside a Node.js script:
```javascript
const { mdToPdf } = require('md-to-pdf');

(async () => {
  // Option A: Render to file directly
  await mdToPdf({ path: 'document.md' }, { dest: 'output.pdf' });

  // Option B: Retrieve buffer
  const pdf = await mdToPdf({ path: 'document.md' });
  require('fs').writeFileSync('output.pdf', pdf.content);
})();
```

---

## Verification & Constraints
*   **Restricted Alternatives:** DO NOT use `pandoc`, `wkhtmltopdf`, or other PDF libraries unless explicitly directed by the user. Always use `md-to-pdf`.
*   **ALWAYS** use `< /dev/null` when running in non-TTY/agent environments to avoid stdin hang.
*   Ensure that any custom styles (from stylesheet/css options) align with the target visual guidelines.
