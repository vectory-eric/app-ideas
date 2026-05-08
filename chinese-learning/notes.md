# Chinese Learning App — Notes

## SnapStudy

### Monetization
- Freemium: 5 captures/day free; $5.99/mo or $39.99/yr unlocks unlimited captures, AI sentence breakdowns, grammar explanations, advanced SRS scheduling

### Risks
- Novli launched a similar photo-capture concept in 2025 — if they expand into Share Sheet + SRS the window narrows fast
- Apple Live Text already does Chinese OCR — wrapper risk if value doesn't clearly extend past dictionary lookup
- HelloChinese could ship a Share Sheet extension in any release — incumbent move is the realer threat than Apple

### Target Users
- Intermediate learners (HSK 3-5)
- Expats and study-abroad students living in Chinese-speaking environments
- Heritage speakers consuming Douyin / WeChat / Chinese media
- Remote workers at Chinese companies

### MVP Scope
- Share Sheet extension + VisionKit OCR
- NLLanguageRecognizer for Chinese detection, CC-CEDICT (free) for word lookup
- Simple SRS queue, no AI in v1 — raw share-to-flashcard is the core value to test
- 8-10 weeks to TestFlight

---

## GlanceChinese

### Monetization
- $2.99/mo or $19.99/yr — low price, high retention

### Risks
- Passive exposure teaches recognition not production — users may churn when they realize they can't speak or write
- Must position as supplement not primary tool, which caps willingness to pay
- iOS-only audience cuts addressable market in half — fine for a side bet but not a flagship

### Target Users
- Busy professionals (25-45) who *want* to learn but never have time to open an app
- Lapsed Duolingo users who lost streaks
- Casual learners studying for cultural / family reasons

### MVP Scope
- Lock Screen + Home Screen widgets with stroke animation and vocab cycling
- Interactive widgets (iOS 17+) for one-tap flashcard review without opening the app
- App itself is just the settings panel and progress dashboard
- Live Activities and Siri Shortcut as v1.1
- Weekend-to-2-week MVP

---

## BridgeChinese

### Monetization
- $9.99/mo or $59.99/yr — premium for ongoing content curation + AI adaptation

### Risks
- Content operations is the real product (copyright, freshness, cultural sensitivity), not the app
- Du Chinese is the closest competitor — differentiation must come from AI personalization + real-world (not textbook-graded) sourcing
- 3-month build but content pipeline is the bottleneck, not iOS code

### Target Users
- Intermediate learners (HSK 3-5) stuck after HelloChinese
- Self-directed adults who read English news daily and want the same in Chinese
- Returning learners coming back after a break

### MVP Scope
- AI-adapted seed content (news snippets, social posts, short stories) at 3 difficulty levels
- Tap-any-word contextual definitions via CC-CEDICT
- Vocabulary auto-pulled into SRS from reading sessions
- Weekly difficulty auto-adjustment based on reading speed + lookup frequency
- 3 months including content pipeline setup

---

## ToneCoach

### Risks
- Narrow retention window — once tones are "good enough," users churn (3-6 month per-user lifetime)
- HelloChinese already has crude tone feedback; some users won't pay extra for a specialist
- Mic quality varies wildly across iPhone generations — visual-feedback fidelity may suffer on older hardware

### Target Users
- Beginner-to-intermediate learners (HSK 1-4) who keep being told their tones are wrong but don't know how
- Heritage speakers with approximate tones picked up from family

---

## BizChinese

### Risks
- Small TAM and hard to reach via consumer marketing
- Corporate buyers may prefer human tutors or enterprise platforms
- Weak iOS moat — mostly a content play
- Content creation requires domain expertise in business Chinese, not just language teaching

### Target Users
- Business professionals (30-50) working with Chinese colleagues, clients, or suppliers
- White-collar workers in tech, finance, manufacturing, logistics

### Verticals to Consider
- Tech (start here — most concentrated and self-serve buyer behavior)
- Finance
- Manufacturing
- Logistics / supply chain

---

## Heritage Bridge

### Risks
- Audience hard to reach via standard SEO/ASO — "ABC" / "heritage learner" not consistently self-identified
- Read Bean already serves this niche; differentiation needs a sharp wedge
- Beginner content competes with HelloChinese which heritage speakers may already use

### Target Users
- ABCs (American-born Chinese) who speak Mandarin with family but can't read or write
- Adult heritage learners reconnecting with Chinese after years away
- Second-generation diaspora kids in their teens / twenties

---

## Traveler Mandarin

### Risks
- Short retention per user — trip ends, app sits unused until next trip
- Competing with Apple Translate / Google Translate which already do basic camera translation
- Differentiation must be "cultural cheat sheet" not "translator wrapper"

### Target Users
- Short-term visitors and tourists
- Business travelers on infrequent China trips
- Study-abroad students in their first weeks

---

## AI conversation tutor (Deprioritized)

### Why Deprioritized
- Crowded space — Speak ($1B valuation, $78M raised), SuperChinese, Kaiwa, Langua, Tutorlily all racing here
- Capital concentration means a new entrant has to outspend incumbents on inference + content; fragile economics
- Tech is ahead of product design, but the moat will go to whoever owns distribution — and incumbents already have it

### Target Users
- Intermediate-to-advanced learners wanting conversation practice without a human tutor

---

## Generic beginner course (Deprioritized)

### Why Deprioritized
- HelloChinese + Yuanfudao 2025 partnership locks the beginner funnel
- Duolingo's free tier swallows the long tail
- Beginner content is increasingly commoditized by AI lesson generation

### Target Users
- Absolute beginners — but the market is saturated and incumbents are AI-investing

---

## Handwriting-only practice (Deprioritized)

### Why Deprioritized
- Declining relevance — native speakers type pinyin and forget how to write rare characters
- Skritter dominates the niche with stroke order + SRS
- PencilKit on iPad could differentiate but the TAM keeps shrinking

### Target Users
- Calligraphy enthusiasts and HSK exam preppers — small and stable, not growing
