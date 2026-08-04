# Documentation Style Guide, Unsloth-grade

Reference: https://unsloth.ai/docs. Every page in univo-docs follows these rules.

## Page anatomy (in this order)
1. **Emoji + benefit-driven title**, not "Configuration" but "⚡ Get running in 5 minutes"
2. **One-sentence promise**, what the reader achieves by the end
3. **Quickstart/summary box**, the 20% that covers 80% of needs, immediately copyable
4. **"Why?" section**, sell the concept before explaining it
5. **Step-by-step body**, numbered steps, each with complete code blocks
6. **Callouts**, GitBook hints: `{% hint style="info" %}`, `{% hint style="warning" %}`, `{% hint style="success" %}`
7. **"What's next?" footer**, 2-3 links to the logical next pages

## Writing rules
- **No em-dashes (). Ever.** They read as AI-generated. Use commas, colons, or short sentences.
- **Deployment claim**: the platform deploys IN A FEW HOURS. The 3-week window covers only the full enterprise project with client dependencies (DNS, branding, content). Never write "deployed in 3 weeks" as the headline.
- **Complete, runnable examples**, never `...` or pseudo-code in quickstarts
- **Tables for choices**, any "X vs Y" becomes a comparison table
- **Emojis in headers**, consistent vocabulary: ⚡ quickstart, 🏗️ architecture, 🔧 config, 🔒 security, 🚀 deploy, 💡 tips, ⚠️ warnings, ✅ done, 🐛 troubleshooting
- **Second person, present tense**, "You deploy", not "The user deploys"
- **Short paragraphs**, 3 lines max; dense info goes in tables or bullets
- **Every command is copy-pasteable**, no placeholders without explanation inline

## GitBook-specific features to use
- `{% tabs %}` for multi-OS or multi-option instructions
- `{% hint %}` callouts liberally (Unsloth uses ~3-5 per page)
- `{% code title="filename.sh" %}` on all code blocks
- Cards/links grid on section landing pages
