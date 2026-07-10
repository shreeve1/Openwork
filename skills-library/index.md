# Skills Library

All **92 skills** organized by category. Nothing auto-loads — agents invoke these on demand via their SKILL.md files.

## Layout

```
skills-library/
├── audit-verify-explain-grade-5/ — Audit work, verify claims, explain in plain language
├── copywriting/ — Write or improve marketing copy for any page
├── customer-email-draft-threads/ — Draft-only Gmail customer support triage
├── customer-support-verification/ — Verify support work against runbooks
├── daily-ui-inspiration-capture/ — Recurring daily UI inspiration capture pipeline
├── elevenlabs-tts/ — Text-to-speech audio from ElevenLabs voice profiles
├── html-to-interaction-prompts/ — Extract animation/interaction prompts from HTML
├── netlify-deploy/ — Deploy web projects to Netlify via CLI
├── optimize-web-animations/ — Profile and optimize frontend animation performance
├── pdf/ — Read, create, and review PDF files with Python tools
├── performance-profiling/ — Apple platform profiling with Instruments
├── playwright/ — Browser automation via playwright-cli
├── playwright-interactive/ — Persistent browser interaction via js_repl
├── screenshot/ — OS-level desktop and system screenshots
├── stitched-full-page-capture/ — Reliable full-page screenshots for lazy-loaded pages
├── swiftui-debugging/ — Diagnose SwiftUI rendering performance issues
├── video-to-superprompt/ — Turn reference videos into detailed recreation prompts
├── x-bookmark-quote-posts/ — Turn X/Twitter bookmarks into quote-post drafts
├── media/
│   ├── aura-asset-images/ — High-quality stock images from Aura Assets
│   └── unsplash-asset-images/ — High-quality Unsplash images for design assets
├── ui/
│   ├── design-first-ui-prompting/ — Spec-driven UI generation prompts
│   ├── design-taste-frontend/ — Senior UI/UX engineer with metric-based rules
│   ├── frontend-design/ — Production-grade frontend interfaces
│   ├── full-output-enforcement/ — Enforce complete code generation, ban placeholders
│   ├── gpt-taste/ — Elite UX/UI with GSAP motion, AIDA structure
│   ├── high-end-visual-design/ — Agency-level design: fonts, spacing, shadows
│   ├── image-to-code/ — Design-first image-to-code pipeline
│   ├── industrial-brutalist-ui/ — Raw mechanical interfaces, Swiss typography
│   ├── minimalist-ui/ — Clean editorial with warm monochrome
│   ├── redesign-existing-projects/ — Upgrade existing sites to premium quality
│   ├── seo-audit/ — Technical SEO audit and diagnosis
│   ├── stitch-design-taste/ — Semantic design system for Google Stitch
│   └── swiftui-pro/ — SwiftUI best practices and performance review
└── web-design/ (60+ skills) — Motion, 3D, and UI styling effects
    ├── gsap/ — Timelines, ScrollTrigger, stagger, SPA cleanup
    ├── threejs/ — Scene setup, loaders, performance, disposal
    ├── tailwindcss/ — Utility-first patterns, responsive variants
    ├── landing-page/ — Landing page design patterns
    ├── pricing-page/ — Pricing page design patterns
    └── ... plus 55+ visual effect skills (gradients, shadows, WebGL, etc.)
```

**Total: 92 skills across 4 categories (general, media, UI, web-design).**

## How skills are invoked

Skills are standard AgentSkill format — YAML frontmatter with `name`/`description` plus Markdown body. Agents load the SKILL.md and follow the workflow inside.
