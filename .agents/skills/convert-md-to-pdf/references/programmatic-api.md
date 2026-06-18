# Programmatic API Usage

If integrating `md-to-pdf` inside a Node.js script instead of using the CLI:

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

> **Note:** This approach is rarely needed when the agent has CLI access. Prefer the shell command documented in `SKILL.md`.
