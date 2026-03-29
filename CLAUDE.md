# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Repo Is

A research and brainstorming hub for early-stage iOS app ideas. Each subfolder is a separate app concept containing market research reports, concept notes, and analysis. There is no source code, no build system, and no tests — this is a documentation-only repo.

## Repository Structure

```
app-ideas/
├── README.md              # Index table of all ideas with status
├── <idea-name>/
│   ├── market-research.md # Competitive landscape, pain points, opportunities
│   ├── README.md          # Problem statement and concept summary (optional)
│   └── notes.md           # Detailed concept analysis (optional)
```

Each idea folder should have at minimum a market research report. README and notes files are added as concepts mature.

## Conventions

- **Status values** in the root README table: `Brainstorm`, `Research`, `Prototype`, `Active`
- **Market research reports** follow a consistent structure: Executive Summary, Competitive Landscape, Pain Points & Underserved Segments, Market Pulse, iOS Differentiation Angles, Ranked Opportunities, Validation Playbook, Sherlocking Risk Assessment
- **iOS-native differentiation** is a core lens for every idea — all concepts target iOS and evaluate Apple platform capabilities (Share Sheet, WidgetKit, VisionKit, App Clips, App Intents, etc.) as competitive moats
- When adding a new idea, create its subfolder and update the root README table

## Git Remote

GitHub: `vectory-eric/app-ideas`
