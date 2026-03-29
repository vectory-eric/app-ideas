# English Learning App for Immigrants

An English learning app for immigrants at the B1-B2 plateau stage — where textbook English diverges from lived English.

## The Problem

Most English learning apps (Duolingo, Babbitt, Busuu) optimize for beginners on a gamified treadmill. Immigrants at B1-B2 level hit a brutal plateau where textbook English diverges from *lived* English — fast-talking coworkers, regional accents, idioms in Slack messages, reading a lease agreement, understanding a doctor's explanation. The gap isn't vocabulary. It's **contextual fluency under real-world pressure**.

## Concept Ideas

### Top 3 Bets

| # | Concept | Description | Why |
|---|---------|-------------|-----|
| 1 | **Between the Lines** | Paste/screenshot text, get tone & subtext analysis | Fastest to MVP — productizes existing behavior (screenshotting confusing emails). Share Sheet + LLM. Ship TestFlight in a week. |
| 2 | **WorkSpeak** | Industry-specific English training by job vertical | Biggest business upside — B2B to employers. App Clips QR in the break room = Trojan horse distribution. |
| 3 | **The Daily Catch-Up** | Real news deconstructed in difficulty layers | Motivation problem solved — users already want to read news. Natural subscription product. |

### Other Ideas

| # | Concept | Description | Notes |
|---|---------|-------------|-------|
| 4 | **Overheard** | Tap a widget when you hear something you didn't understand, build a personal vocab deck from real life | Low build risk, weekend MVP. Retention risk without review loop. |
| 5 | **Couch Tutor** | Learn from what you're watching — micro-lessons from TV episode transcripts | Content licensing risk. Works best as YouTube companion. |
| 6 | **MeetingPrep** | Pre-teaches vocabulary for upcoming meetings via calendar integration | Narrow use case — could be a WorkSpeak feature. |
| 7 | **Civic English** | Bureaucratic survival English — citizenship tests, DMV, healthcare forms, tax docs | Small TAM but very high-intent users. |

### Deprioritized

| # | Concept | Why Deprioritized |
|---|---------|-------------------|
| 8 | **Rewind** — passive audio capture + daily debrief | Privacy minefield. Months of App Store review battles. |
| 9 | **SoundLike** — accent coaching with phoneme feedback | ELSA Speak has $27M+ funding and this positioning. |
| 10 | **Neighbor** — location-based language exchange | Marketplace cold-start problem. Need funding, not a build. |

## iOS Mechanics to Explore

- **Share Sheet extension** — zero behavior change, already in user's flow (Between the Lines)
- **App Clips** — QR code onboarding without IT involvement (WorkSpeak)
- **Lock Screen widgets** — daily phrase engagement (Daily Catch-Up)
- **VisionKit OCR** — screenshot & document scanning (Between the Lines, Civic English)
- **On-device Speech Recognition** — privacy-first transcription (Overheard)
- **EventKit calendar integration** — auto-detect meetings (MeetingPrep)
- **Siri Shortcuts** — quick capture and daily lessons

## Monetization

- **Between the Lines:** Free 3/day, $5/month unlimited
- **WorkSpeak:** B2B to restaurant chains and employers
- **The Daily Catch-Up:** Daily content subscription
