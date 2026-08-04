# Content Creation with Studio

Course content is authored in **Open edX Studio** and rendered in the Learner app through xBlocks. Authors get the full power of the Open edX course engine with the components below.

## 🧩 xBlock types

| xBlock | Use for |
|---|---|
| `html` | Rich text pages, images, embedded media |
| `problem` | Quizzes and assessments: multiple choice, checkboxes, dropdown, numerical, text input |
| `scorm` | Import and play SCORM packages inside a course unit |
| `lti` | Embed third-party learning tools via LTI |
| `google-document` | Embed Google Workspace documents (Docs, Sheets, Slides) directly in courseware |
| `vimeo` | Video units backed by Vimeo Pro hosting |

## 📦 SCORM packaging notes

- Export packages as **SCORM 1.2** for maximum compatibility
- Keep package sizes lean: compress media before zipping, and avoid bundling videos, host them on Vimeo and reference them instead
- Test completion criteria in the package itself; the player reports completion back to the course
- Name files and manifests with ASCII characters only to avoid encoding issues on upload

## 🎥 Video best practices

- **Hosting:** all videos are hosted on **Vimeo Pro** and embedded via the `vimeo` xBlock, never upload raw video files to the course assets
- **Compression:** export H.264 MP4, 1080p maximum; target ~5 Mbps for talking-head content to keep streaming smooth on modest connections
- **Captions:** provide captions for every video, required for accessibility and for the 6 supported locales
- **Length:** keep individual videos under 10 minutes; split longer topics into a sequence of units

## 📝 Rich text with Froala

The `html` xBlock editor uses **Froala WYSIWYG**:

- What-you-see editing for headings, lists, tables, links and images
- Paste-from-Word cleanup built in
- HTML source mode available for advanced authors

## ➗ Math with MathJax

Mathematical notation renders through **MathJax**:

- Write formulas in LaTeX syntax inside HTML components
- Inline math: `\( E = mc^2 \)`, display math: `\[ \int_0^1 x^2 \, dx \]`
- MathJax renders consistently across all 6 locales, including right-to-left (`ar`)

---

*A Studio Reliability Program (monitoring, save-failure elimination, SCORM pipeline improvements) is committed for Q4 2026, see the [roadmap](../roadmap/README.md).*
