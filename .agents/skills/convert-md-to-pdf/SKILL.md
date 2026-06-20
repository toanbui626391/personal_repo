---
name: convert-md-to-pdf
description: >-
  Converts Markdown files (.md) to PDF using the md-to-pdf CLI tool.
  Use when the user asks to generate a PDF, export a document to PDF,
  or convert any Markdown file (e.g., CVs, specs, reports) to PDF format.
  The agent MUST NOT use pandoc, wkhtmltopdf, or any other PDF tool.
---

# Convert Markdown to PDF Skill

This skill uses `md-to-pdf` to convert Markdown documents (e.g., CVs, technical specifications) into high-quality PDFs. The tool leverages Marked for Markdown-to-HTML conversion, Puppeteer (headless Chromium) for rendering, and highlight.js for code syntax highlighting.

---

## When to Use This Skill

- The user asks to convert a Markdown file to PDF
- The user asks to "export", "generate", or "create" a PDF from a `.md` file
- The user mentions `md-to-pdf` or `md2pdf`

## When NOT to Use This Skill

- The user asks for HTML output only (not PDF)
- The user explicitly requests `pandoc`, `wkhtmltopdf`, or another specific tool

---

## ⚠️ CRITICAL: Non-TTY / Agent Environment Requirement

The agent **MUST ALWAYS** redirect stdin from `/dev/null` when running `md-to-pdf`. Without this, the CLI will **hang indefinitely** waiting for stdin because it detects it is not running in an interactive terminal (TTY).

### The Correct Command (Always Use This)

```bash
/opt/homebrew/bin/md-to-pdf <file.md> < /dev/null
```

**Root Cause:** When `md-to-pdf` is not attached to a TTY, it waits for piped content on stdin. Redirecting from `/dev/null` immediately signals EOF, so the CLI proceeds to process the file argument instead.

---

## Instructions for PDF Conversion

### 1. Basic Commands

*   **Binary path:** `/opt/homebrew/bin/md-to-pdf` on macOS (globally installed via npm).
*   **Shorthand alias:** `md2pdf` (same binary, same behavior).
*   **Agent command (always use this):**
    ```bash
    /opt/homebrew/bin/md-to-pdf <file.md> < /dev/null
    ```

### 2. Decision Guide

The agent should choose the approach based on the user's requirements:

- **Simple conversion (no custom styles):** Run the basic command with `< /dev/null`.
- **Custom styling needed:** Add front-matter config to the Markdown file before running the command. See `references/config-options.md` for the full configuration reference.
- **Custom output path:** Use `dest` in front-matter (e.g., `dest: ./output.pdf`), or rename the output file after generation.
- **Page breaks needed:** Insert `<div class="page-break"></div>` at the desired break points in the Markdown source.

### 3. Common Front-matter Example

For most documents, the following front-matter provides a good starting point:

```markdown
---
pdf_options:
  format: A4
  margin: 15mm 20mm
  printBackground: true
---
```

For the full configuration reference (stylesheets, headers/footers, layout control), the agent should read `references/config-options.md`.

---

## Verification & Constraints

*   **Restricted alternatives:** The agent MUST NOT use `pandoc`, `wkhtmltopdf`, or other PDF libraries unless the user explicitly directs otherwise. The agent should always use `md-to-pdf`.
*   **Stdin redirect:** The agent MUST use `< /dev/null` when running in non-TTY/agent environments to avoid stdin hang.
*   **Style alignment:** The agent should ensure that any custom styles (from stylesheet/css options) align with the target visual guidelines specified by the user.
