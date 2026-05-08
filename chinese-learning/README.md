# Chinese Learning App

A Chinese learning app for intermediate learners stuck at the HSK 3-5 plateau — where textbook Chinese diverges from real Chinese.

## The Problem

Most Chinese learning apps optimize for beginners. HelloChinese owns the HSK 1-3 funnel; Pleco owns the dictionary; Anki owns flashcards; AI conversation tutors (Speak, SuperChinese, Kaiwa, Langua) are flooding the upper end. The gap that no one owns is the **intermediate plateau** — learners who can read pinyin and 500 characters but can't follow a podcast, read a menu, or skim a news article. Every Reddit thread, every roundup, and every honest review names this as where learners quit. Apple's WWDC 2025 Live Translation push commoditizes basic translation but doesn't *teach* — the learning layer is wide open.

## Concept Ideas

### Top 3 Bets

| #   | Concept           | Description                                                              | Why                                                                                                                              |
| --- | ----------------- | ------------------------------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------- |
| 1   | **SnapStudy**     | Capture any Chinese text via Share Sheet or camera; auto-build SRS deck  | Sits at the intersection of biggest pain (plateau), strongest iOS moat (Share Sheet + VisionKit), and existing user behavior. 2-3 month MVP. |
| 2   | **GlanceChinese** | Widget-first ambient learning across Lock Screen, Home Screen, Live Activities | Most defensible — impossible without iOS. Weekend MVP. Low-friction supplement positioning, not primary tool.                    |
| 3   | **BridgeChinese** | AI-graded real-world content (news, social, podcasts) that scales with you | Largest underserved segment with proven willingness to pay. Du Chinese is the only competitor of note and hasn't evolved much.   |

### Other Ideas

| #   | Concept              | Description                                                                | Notes                                                                |
| --- | -------------------- | -------------------------------------------------------------------------- | -------------------------------------------------------------------- |
| 4   | **ToneCoach**        | Real-time pitch contour overlay shows exactly how your tones drift         | Sharp specialist wedge but narrow retention window (3-6 mo per user). |
| 5   | **BizChinese**       | Scenario-based business Mandarin: meetings, email, WeChat etiquette        | Premium pricing justified, but small TAM and weak iOS moat.          |
| 6   | **Heritage Bridge**  | Literacy-first track for ABCs / heritage speakers who speak but can't read | Underserved segment the research flagged; Read Bean is the only competitor. |
| 7   | **Traveler Mandarin** | Camera-first survival Chinese — order food, taxis, signs, small talk      | Currently a Pleco + Google Translate cobble. High-volume but bursty retention. |

### Deprioritized

| #   | Concept                       | Why Deprioritized                                                                                            |
| --- | ----------------------------- | ------------------------------------------------------------------------------------------------------------ |
| 8   | **AI conversation tutor**     | Crowded and well-funded — Speak ($1B valuation), SuperChinese, Kaiwa, Langua all racing. Tech ahead of design but capital wins distribution. |
| 9   | **Generic beginner course**   | HelloChinese + Yuanfudao 2025 partnership locks the funnel. Beginner content commoditized by AI.             |
| 10  | **Handwriting-only practice** | Declining relevance — natives type pinyin. Skritter owns the niche. PencilKit could differentiate but TAM shrinking. |

## iOS Mechanics to Explore

- **Share Sheet extension** — context capture from any app, one tap (SnapStudy)
- **VisionKit OCR** — free, native Chinese character recognition (SnapStudy, Traveler Mandarin)
- **WidgetKit** including interactive widgets (iOS 17+) — passive micro-exposure (GlanceChinese)
- **Live Activities / Dynamic Island** — habit reinforcement, word-of-the-hour (GlanceChinese)
- **App Intents / Siri Shortcuts** — natural-language entry points
- **On-device CoreML pitch analysis** — instant tone feedback without server round-trip (ToneCoach)
- **EventKit calendar integration** — pre-meeting vocab surfacing (BizChinese)
- **PencilKit (iPad)** — natural Apple Pencil character writing (Heritage Bridge, deprioritized handwriting)

## Monetization

- **SnapStudy:** Free 5 captures/day, $5.99/mo or $39.99/yr unlocks unlimited + AI sentence breakdowns + advanced SRS
- **GlanceChinese:** $2.99/mo or $19.99/yr — low price, high retention positioning
- **BridgeChinese:** $9.99/mo or $59.99/yr — premium for ongoing content curation + AI adaptation
- **ToneCoach:** $4.99/mo or $29.99/yr — specialist pairing tool
- **BizChinese:** $14.99/mo or $99.99/yr — pro pricing, employer-reimbursed potential
