# Contributing to Tez Capital Docs

Hi 👋 Thanks for your interest in improving the Tez Capital documentation!

We welcome contributions of all kinds — typo fixes, clarifications, additional troubleshooting tips, or entirely new pages. Every improvement helps the Tezos baking community.

---

## Quick Start

### 1. Fork the repository

Click **Fork** in the top-right corner of the [GitHub repository](https://github.com/tez-capital/docs.tez.capital_content) to create your own copy.

### 2. Clone your fork

```bash
git clone https://github.com/<your-username>/docs.tez.capital_content.git
cd docs.tez.capital_content
```

### 3. Make your changes

All documentation lives in the `en/` directory as Markdown files. The site is built with [Hugo](https://gohugo.io/).

```
en/
├── _index.md                  # Site home page
├── getting-started/           # Introductory guides
│   ├── _index.md
│   ├── what-is-baking.md
│   ├── choose-your-setup.md
│   └── ...
├── tezbake/                   # TezBake documentation
├── tezpay/                    # TezPay documentation
├── tezsign/                   # TezSign documentation
├── tezgov/                    # TezGov documentation
├── tezwatch/                  # TezWatch documentation
├── faq.md                     # Unified FAQ
└── changelog.md               # What's New
```

#### Editing existing pages

Simply open the relevant `.md` file and make your changes.

#### Adding a new page

1. Create a new `.md` file in the appropriate section directory
2. Add Hugo frontmatter at the top of the file:

```yaml
---
title: "Your Page Title"
weight: 5          # Controls sort order in the sidebar
type: docs
summary: "One-sentence description of this page"
---
```

3. Write your content in Markdown
4. End the page with the standard footer:

```markdown
Any questions/comments/concerns? Please contact the Tez Capital team on [Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital)
```

### 4. Preview locally (optional)

If you have Hugo installed:

```bash
hugo server
```

Then open `http://localhost:1313` in your browser.

### 5. Submit a pull request

Push your changes to your fork and open a pull request against the `main` branch of the original repository. Include a short description of what you changed and why.

---

## Style Guidelines

- **Tone:** Friendly but professional. Clear and concise. Avoid jargon without explanation.
- **Callouts:** Use the standard callout format:
  - `> **💡 TIP:**` for helpful hints
  - `> **⚠️ WARNING:**` for things that can go wrong
  - `> **ℹ️ INFO:**` for neutral informational notes
  - `> **🚨 CRITICAL:**` for safety-critical warnings (e.g. slashing risks)
- **Cross-links:** Link to other pages using Hugo relative paths, e.g. `/tezbake/tutorials/baking-on-mainnet/`
- **Tables:** Prefer tables over long bullet lists for comparisons
- **Commands:** Always wrap CLI commands in fenced code blocks with the appropriate language tag

---

## What We Especially Welcome

- ✅ Typo and grammar fixes
- ✅ Clarifications to confusing passages
- ✅ New troubleshooting entries (things you ran into that weren't documented)
- ✅ Updated hardware recommendations or pricing
- ✅ Translations (contact us on Discord first to coordinate)
- ✅ Screenshots or diagrams that improve understanding

---

## Questions?

Join us on [Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital) — we're happy to help you get your contribution in shape.

Thank you for helping make Tezos baking more accessible! 🥖
