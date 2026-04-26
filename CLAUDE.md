# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Project Is

A Claude Code Skill that generates production-ready web assets (favicons, PWA app icons, Open Graph social images) through conversational AI. Users install the skill from `skills/web-asset-generator/` into `~/.claude/skills/` and invoke it via natural language.

## Installing & Using the Skill

```bash
# Install Python dependencies
pip install Pillow
pip install pilmoji 'emoji<2.0.0'   # optional, for emoji source support

# Verify dependencies
python skills/web-asset-generator/scripts/check_dependencies.py

# Run scripts directly
python skills/web-asset-generator/scripts/generate_favicons.py --help
python skills/web-asset-generator/scripts/generate_og_images.py --help
```

No build step required. There is no package.json, test runner, or linter configured.

## Architecture

```
skills/web-asset-generator/
├── SKILL.md              # Claude reads this; YAML frontmatter triggers the skill
├── scripts/
│   ├── generate_favicons.py     # Favicons + PWA icons (16×16 → 512×512, ICO, apple-touch)
│   ├── generate_og_images.py    # OG/social images (1200×630, 1200×675, 1200×1200)
│   ├── emoji_utils.py           # 60+ emoji database with keyword search and Pilmoji rendering
│   ├── check_dependencies.py    # Setup verifier
│   └── lib/validators.py        # WCAG contrast, file size, dimension validation
└── references/specifications.md # Platform specs (FB, Twitter, LinkedIn, WhatsApp)
```

**Skill activation flow**: User makes a natural-language request → Claude reads `SKILL.md` → Claude uses `AskUserQuestion` to clarify asset type/source/platforms → Claude invokes the appropriate Python script → scripts return image files + HTML meta tags → Claude offers framework-specific code integration.

**Framework detection** in the scripts covers: Next.js, Astro, SvelteKit, Vue/Nuxt, Gatsby, plain HTML.

**Validation** (`lib/validators.py`) checks: WCAG contrast ratio (AA/AAA), platform file size limits (FB <8MB, Twitter <5MB), aspect ratios, and output dimensions.

## Plugin Metadata

- `.claude-plugin/plugin.json` — Claude Code marketplace metadata
- Claude Code plugin installation: see `PLUGIN_INSTALLATION.md`
