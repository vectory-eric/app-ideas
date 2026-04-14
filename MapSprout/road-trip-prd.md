# PRODUCT REQUIREMENTS — MapSprout: Road Trip Mode

> **Code convention:** Road Trip features use `RT`-prefixed codes (F-RT-001, US-RT-001, etc.) to namespace within the existing requirements system. File headers should reference these codes per CLAUDE.md conventions.

## 1) Overview

1.1 Road Trip is a new mode in MapSprout where kids take a **virtual road trip across America**, "visiting" states using MapKit 3D Flyover and Look Around.
1.2 Instead of answering quiz questions from a menu, kids **explore states visually first**, then answer challenges about what they just saw.
1.3 The core loop is: **fly to a state → explore it visually → answer challenges → unlock it → plan the next stop**.
1.4 Road Trip uses a **curated question bank** (no AI/Foundation Models required). Works on all iPhones running iOS 17+.
1.5 This is an evolution of the existing MapSprout app — it builds on top of the current data layer (D-001 through D-019) and mastery system (F-009).

### Platform Requirements

- **Minimum iOS:** 17.0 (required for SwiftUI `Map` with `MapCameraPosition`, `LookAroundPreview`, `.mapStyle(.imagery(elevation: .realistic))`)
- **Minimum hardware for 3D terrain:** A12 Bionic (iPhone XS/XR, 2018+). On older A11/A10 devices, MapKit silently degrades to 2D satellite — the flyover camera animation still works, just flat. No crash, no error.
- **Network required:** Road Trip requires an internet connection for MapKit satellite tiles, 3D terrain, and Look Around. This is a departure from the existing app's fully offline design (Section 7.1 of .Requirements.md). See F-RT-011 for the connectivity gate.
- **Feature gating for iOS 16:** If the app's deployment target remains below iOS 17, all Road Trip code must be wrapped in `@available(iOS 17, *)` checks, and the Home Hub card (F-RT-001) must be hidden on iOS 16 devices.

### Implementation Context

The current app uses **no MapKit** — all maps are pure SwiftUI with SVG path rendering (`StateShapeView`, `StateShape`, `USMapLayout`). Road Trip introduces MapKit as a **new dependency** alongside the existing custom map system. The custom SVG map continues to power the Explore Map (S-006) and quiz tap-location questions; MapKit is used exclusively for Road Trip's 3D Flyover and Look Around features.

Key existing infrastructure to reuse:
- **Quiz engine**: `QuizSession`, `Question`, `QuestionBuilder` in `Shared/` — Road Trip challenges reuse `QuizSessionView` (S-004) for rendering
- **Progress tracking**: `LearningProgressStore` (event-sourced, `@Observable`)
- **Streak**: `StreakStore` — `recordSessionCompleted()` deduplicates by calendar day, so Road Trip + Today's Quest on the same day count as one streak day
- **State data**: `StateInfo` via `StateListLoader`, content packs via `StateContentLoader` (D-016 symbols, D-017 numbers, D-019 geography)
- **Navigation**: `HomeHubRoute` enum + `NavigationStack` in `HomeHubView` — add `.roadTrip` case + matching `navigationDestination` branch
- **Sound/haptics**: `SoundHelper` (synthesized audio, no bundled files) — all methods are `@MainActor`, safe to call from SwiftUI views
- **Animations**: `Animation+Tokens.swift` (`.springSnappy`, `.springGentle`, `.entranceFade`) — note: `.entranceFade` is a single fixed delay, not a stagger factory; stagger must be coded manually with offset delays per card (matching HomeHubView pattern)
- **Celebrations**: `ConfettiOverlay` (16-particle burst) — self-triggering on `onAppear`; to replay, remove and re-add the view via conditional rendering (`if showConfetti { ConfettiOverlay() }`)

---

## 2) Goals

2.1 Give kids a reason to **explore states beyond quizzing** — seeing a 3D flyover of the Grand Canyon teaches more than "Arizona's capital is Phoenix."
2.2 Create a **progression system with a narrative** — "I'm on a road trip" is more motivating than "I've completed 34/51 locations."
2.3 **Increase session depth and retention** — the current quiz-only loop has a ceiling problem (kids finish and stop). Road Trip adds visual exploration that makes each state memorable.
2.4 Work on **all iOS 17+ devices** — no Foundation Models dependency. 3D terrain requires A12+; older devices get 2D satellite fallback automatically.
2.5 Reuse existing mastery, content, and progress infrastructure.

---

## 3) User Stories

### Road Trip Setup
- **US-RT-001** As a user, I want to start a Road Trip from the Home Hub so that I have a new way to explore states.
- **US-RT-002** As a user, I want to pick a starting state or have one suggested so that my trip feels personal.
- **US-RT-003** As a user, I want to see my road trip route on the map so that I know where I've been and where I'm going.

### State Visit (Core Loop)
- **US-RT-004** As a user, I want to "fly" to a state via a MapKit 3D Flyover animation so that I feel like I'm really going there.
- **US-RT-005** As a user, I want to explore the state capital at street level (Look Around) so that I can see what it actually looks like.
- **US-RT-006** As a user, I want to see 3-5 key facts about the state during my visit (capital, nickname, fun fact, one symbol, one number) so that I learn before being quizzed.
- **US-RT-007** As a user, I want to answer 3-5 challenge questions about the state I just explored so that I prove what I learned.
- **US-RT-008** As a user, I want challenges to reference what I just saw ("You flew over the Grand Canyon — which state is it in?") so that visual exploration and quizzing feel connected.
- **US-RT-009** As a user, I want to "unlock" a state with a stamp/badge after completing its challenges so that I feel a sense of progress.

### Route & Progression
- **US-RT-010** As a user, I want the app to suggest my next stop based on neighboring states so that the road trip feels geographically logical.
- **US-RT-011** As a user, I want to be able to pick my own next stop from neighboring states so that I have some choice.
- **US-RT-012** As a user, I want to see visited states colored on the Road Trip Map so that I can see my journey.
- **US-RT-013** As a user, I want to revisit a state I've already been to so that I can re-explore or practice again.

### Non-Contiguous States
- **US-RT-014** As a user, I want Alaska and Hawaii to appear as "Fly There" special stops so that I can visit them even though they have no bordering states.

### Session Structure
- **US-RT-015** As a user, I want each state visit to take about 3-5 minutes so that I can do one or two stops per session.
- **US-RT-016** As a user, I want to pause my road trip and resume later so that I don't have to finish in one sitting.
- **US-RT-017** As a user, I want to see a trip summary after each session showing states visited and facts learned.

### Connectivity
- **US-RT-018** As a user, I want to be told when I need internet for Road Trip so that I'm not confused by a blank map.
- **US-RT-019** As a user, I want the quiz challenges to still work even if I lose connection mid-visit, since questions use bundled data.

### Integration with Existing Features
- **US-RT-020** As a user, I want road trip challenges to update my mastery levels so that progress carries over to the main map.
- **US-RT-021** As a user, I want states I've already mastered (Champ!) to show as "visited" on the road trip map so I don't have to redo them.
- **US-RT-022** As a user, I want road trip sessions to count toward my daily streak.

### Accessibility
- **US-RT-023** As a user with Reduce Motion enabled, I want to skip the flyover animation and see a static satellite view so that I'm not disoriented.

---

## 4) Features

### F-RT-001 Road Trip Entry Point
- What it does: Adds a "Road Trip" card on the Home Hub alongside "Explore Map" and "My Passport."
- Implementation:
  - Add `.roadTrip` case to `HomeHubRoute` enum in `HomeHubView.swift`
  - Add a matching `case .roadTrip: RoadTripMapView(...)` branch in the `navigationDestination(for: HomeHubRoute.self)` switch (currently at lines 121-132)
  - Add a new action card following the existing pattern (dot color, icon, background tint, chevron, `SoundHelper.haptic(.light)` on tap)
  - Stagger the card entrance with a manual `.delay()` offset added to `.entranceFade`, matching how existing cards are staggered in HomeHubView
  - Gate visibility: `entitlementStore.state != .expired` (Road Trip is a premium feature, included in trial and active subscriptions)
- When it appears: Home Hub (S-003), after baseline is completed (`userProfileStore.hasCompletedBaseline`). Hidden on iOS 16 devices.
- Goes to: S-RT-001 (Road Trip Map) via `NavigationStack` push.
- If something goes wrong: Hide the card; existing app functionality unaffected.

### F-RT-002 Road Trip Map
- What it does: Shows the US map with the user's road trip route overlaid — visited states are colored, the current stop is highlighted, and suggested next stops pulse gently.
- Implementation: Create `RoadTripMapView.swift` in `Views/`. Reuse the existing SVG map infrastructure (`MasteryUSMapView` pattern) with a new coloring scheme:
  - Visited states: gold fill (`Color.mapSproutGold` at 85% opacity, matching `.star` mastery)
  - Current stop: highlighted with scale effect (`1.05x` + shadow, matching `StateShapeView` tap animation)
  - Suggested next stops: pulsing opacity animation
  - Unvisited: gray (`Color.mapSproutSage`)
  - Alaska and Hawaii: always visible in insets with a small airplane icon and "Fly There!" label when unvisited (see F-RT-010)
- Data: Uses `StateListLoader` (D-001) + `StateContentLoader.geography()` (D-019) for bordering states + `RoadTripStore` (D-RT-001).
- **Initial state (new trip):** When no active trip exists, S-RT-001 shows the route selection flow (see F-RT-009) before rendering the map with route progress.
- If something goes wrong: Show the standard Explore Map as fallback.

### F-RT-003 State Flyover
- What it does: When a user taps a state to visit, transitions to a **MapKit `Map` view** with an animated 3D camera flyover to the state capital.
- Implementation: Create `StateFlyoverView.swift` in `Components/`. This introduces `import MapKit` as a new framework dependency.
  - Use SwiftUI `Map(position:)` with `@State var position: MapCameraPosition`
  - **Animate with `mapCameraKeyframeAnimator(trigger:keyframes:)`** — this is the purpose-built API for cinematic camera moves with independent control over `centerCoordinate`, `distance`, `heading`, and `pitch`. Do NOT use `withAnimation` on `MapCameraPosition` (unreliable, known issues with repeated updates per Apple Developer Forums).
  - Start: wide view of the US
  - End: zoomed to capital at ~45-60 degree pitch, ~5km altitude
  - Duration: 3-4 seconds via keyframe timing
  - Capital coordinates: from `StateInfo.capitalLatitude` / `StateInfo.capitalLongitude` (see D-001 Extension)
  - Map style: `.mapStyle(.imagery(elevation: .realistic))` for 3D terrain (iOS 17+, A12+ hardware; auto-degrades to 2D on older chips)
  - **`accessibilityReduceMotion` handling:** If enabled, skip the animated flyover entirely. Show a static `Map` view centered on the capital at the destination camera position. This is an explicit design decision, not polish — the flyover is the most motion-intensive element.
- Fallback: If 3D terrain data is unavailable for a location, `.mapStyle(.imagery)` provides flat satellite with the same camera animation.
- If something goes wrong: Skip animation, show static satellite view centered on capital.

### F-RT-004 Street-Level Exploration (Look Around)
- What it does: After the flyover lands, presents an optional **Look Around** card that the user can tap to explore the capital at street level.
- Implementation: Create `LookAroundCardView.swift` in `Components/`. **Both `LookAroundPreview` and the `.lookAroundViewer()` modifier are native SwiftUI (iOS 17+) — no UIKit wrapping needed.**
  - Use `MKLookAroundSceneRequest(coordinate:)` (async) to check availability — fire this during the flyover animation so the result is ready when flyover completes
  - Show `LookAroundPreview(scene: $scene)` as a compact card at the bottom of the flyover view
  - Attach `.lookAroundViewer(isPresented: $showingViewer, scene: $scene)` — tapping the preview opens full-screen interactive viewer
  - "Done exploring" button dismisses and proceeds to State Visit Card
- Fallback: If Look Around is unavailable (expected for ~30-40% of capitals — see Section 7), show a **curated landmark photo** for the state with the capital name and a one-line landmark description. Do NOT fall back to the SVG state silhouette — it provides no new value over the Explore Map. Curated photos must be sourced as part of content creation (D-RT-004).
- If something goes wrong: Skip Look Around, proceed directly to state facts.

### F-RT-005 State Visit Card
- What it does: After flyover/exploration, shows a summary card with key facts about the state.
- Implementation: Create `StateVisitCardView.swift` in `Components/`. Reuse data from existing loaders — this is a compact version of `LocationDetailSheetView`:
  - Capital: from `StateInfo.capital`
  - Nickname + fun fact: from `StateInfo` (D-002, same as F-013)
  - One symbol: from `StateContentLoader.symbols()` (D-016) — rotate bird/flower/tree based on visit count
  - One stat: from `StateContentLoader.numbers()` (D-017) — rotate population/area/highest point
  - Style: use `Color.mapSproutWarmCream` background, `Color.mapSproutCoral` highlights, `.system(.subheadline, design: .rounded)` fonts consistent with existing cards
- Purpose: This is the **teaching moment** — the user absorbs facts before being challenged on them.
- If something goes wrong: Show capital + nickname only (minimum viable visit).

### F-RT-006 Road Trip Challenges
- What it does: Presents 3-5 challenge questions about the state the user just visited.
- Implementation: **Do NOT add new cases to `QuestionKind`** — it conforms to `CaseIterable` and is used in exhaustive switches throughout `QuestionBuilder.makeQuestion()` and `QuestionBuilder.validKinds()`. Adding road trip cases would bleed into standard quiz sessions.
  Instead:
  - Create `RoadTripQuestionBuilder` in `Shared/` that reads from `road_trip_questions.json` (D-RT-002) and produces `Question` objects using **existing `QuestionKind` values** with custom prompts:
    - Visual recall → `QuestionKind.locationToCapital` with road trip prompt text ("You just flew over [State]...")
    - Fact check → `QuestionKind.capitalToLocation` with nickname/fact prompt
    - Symbol match → `QuestionKind.identifyHighlighted` with symbol prompt
    - Number comparison → `QuestionKind.locationToCapital` with comparison prompt
    - Map tap → `QuestionKind.tapLocationOnMap` (direct reuse)
  - `RoadTripQuestionBuilder` calls `StateContentLoader.symbols(for:)` and `StateContentLoader.numbers(for:)` directly (both are `nonisolated`, safe to call from any context)
  - Create `QuizSession.buildRoadTripVisit(for state: StateInfo, allStates: [StateInfo], visitCount: Int) -> QuizSession` factory method. The `visitCount` parameter controls difficulty tier selection.
  - Reuse existing `QuizSessionView` (S-004) for the challenge UI — it already handles all question rendering, hints (F-006), "I need help" (F-007), and feedback (F-008)
  - Set `isPracticeOnly: false` so results update mastery
  - **Before recording results:** call `learningProgressStore.startSession(kind: .roadTrip)` — requires adding `.roadTrip` to `AnswerEvent.SessionKind` (see D-RT-001 implementation notes)
- Difficulty: Use `LearningProgressStore.masteryLevel(for:)` to select question tier. `.new`/`.learning` → tier 1-2. `.gettingThere`+ → tier 2-3.
- If something goes wrong: Fall back to standard `QuizSession.buildPracticeState()` for that state.

### F-RT-007 State Unlock & Stamp
- What it does: After completing challenges, the user gets a celebratory "stamp" animation.
- Implementation: Create `StampUnlockView.swift` in `Components/`:
  - Stamp animation: state silhouette (reuse `StateShapeView` with gold fill) drops from above with `.springSnappy` animation + rotation
  - Sound: `SoundHelper.playCelebration()` + `SoundHelper.haptic(.medium)`
  - Update `RoadTripStore` to mark state as visited
  - Call `LearningProgressStore.recordResult()` to update mastery per F-009
  - **Celebration sequencing:** If the state reaches `.star` (Champ!), show the stamp animation first (1.5s), THEN trigger the Champ celebration with `ConfettiOverlay` after a brief delay. Do not fire both simultaneously. Use `ConfettiOverlay` via conditional rendering: `if showChampConfetti { ConfettiOverlay() }`.
- If something goes wrong: Mark state as visited without animation.

### F-RT-008 Re-Visiting a State
- What it does: Allows kids to revisit states they've already unlocked on the road trip.
- When it appears: Kid taps a visited (gold) state on the Road Trip Map.
- Behavior:
  - The flyover and Look Around replay (kids love re-watching the flyover)
  - New questions are drawn from the pool (different from the first visit, if available)
  - Results update mastery as normal (helps reinforce learning)
  - The state is NOT re-stamped (already visited)
  - Does NOT count as a new visit for trip progress
  - Counts toward daily streak if it's the first session of the day

### F-RT-009 Themed Routes
- What it does: Pre-built road trip routes with narrative structure plus a Free Roam option.
- **v1 scope: Free Roam only.** Themed routes (Coast to Coast, Southern Swing) are deferred to v1.1 to reduce v1 scope. Free Roam delivers the full core loop without route definition JSON, route-aware next-stop logic, or route switching flows.
- Free Roam: User picks every stop. All 51 locations available. No set route.
- **Route selection UI:** Since v1 is Free Roam only, S-RT-001 skips the route picker and goes directly to the map. When themed routes are added in v1.1, add an initial route picker state to S-RT-001 that appears when no active trip exists.
- If something goes wrong: N/A for v1 — Free Roam has no route logic.

### F-RT-010 Next Stop Suggestion
- What it does: After unlocking a state, suggests 2-3 neighboring states as the next stop.
- Implementation: Use `StateContentLoader.geography(for:)?.borderingStates` (D-019) to get neighbors. **Handle `nil` — `borderingStates` is `[String]?` (optional).** If `nil`, skip directly to "Pick any state."
  - Filter by `RoadTripStore.visitedAbbreviations` to find unvisited neighbors
  - Display as tappable state chips (similar to bordering states chips in `GeographySectionContent`)
  - If all bordering states are visited (or `borderingStates` is `nil`), find nearest unvisited using `USMapLayout` centroids
  - **Alaska and Hawaii:** Always show as special "Fly There!" suggestions at the bottom of the next stop list, regardless of current location. These are reachable from any state since they have no contiguous neighbors. Display with an airplane icon to distinguish from driving stops.
  - Always show "Pick any state" option → returns to S-RT-001
- If something goes wrong: Show full map and let user pick freely.

### F-RT-011 Network Connectivity Gate
- What it does: Checks for network connectivity before entering Road Trip and handles mid-session connectivity loss.
- When it appears: When user taps the Road Trip card on Home Hub.
- Behavior:
  - **No connection at entry:** Show a friendly message: "Road Trip needs an internet connection for 3D maps. Try Explore Map to practice offline!" Disable the "Visit Next Stop" button on S-RT-001. The map itself (SVG-based) still renders.
  - **Connection lost mid-flyover:** MapKit shows gray/blank tiles. The "Start Challenges" button remains active — quiz challenges use bundled JSON and work fully offline (US-RT-019). Show a subtle banner: "Map couldn't load — but you can still answer challenges!"
  - **Connection lost on Look Around:** `MKLookAroundSceneRequest` will return `nil`. The fallback photo is shown (same as coverage gap).
- Implementation: Use `NWPathMonitor` (Network framework) for reachability. Check `path.status == .satisfied` on Road Trip entry.
- If something goes wrong: Allow entry anyway — worst case is gray map tiles, but challenges still work.

### F-RT-012 Trip Completion & Session Tracking
- What it does: Defines what constitutes a "completed session" for streak and trip progress purposes.
- Rules:
  - **Streak credit:** Visiting at least one state (completing its challenges) in a Road Trip session counts as a daily session for `StreakStore.recordSessionCompleted()`. Note: `StreakStore` deduplicates by calendar day — if the user also completes Today's Quest the same day, only one streak day is recorded. This is correct behavior.
  - **Trip completion:** A Free Roam trip is "complete" when all 51 locations have been visited. Show celebration on S-RT-005.
  - **Session end:** A session ends when the user taps "Take a Break" or closes the app. `RoadTripStore` persists trip state on every visit completion, so no progress is lost.

---

## 5) Screens

### S-RT-001 Road Trip Map
- What's on it:
  - US map with visited/unvisited coloring (reuses SVG map from `MasteryUSMapView` with road trip color scheme)
  - Alaska and Hawaii in insets with "Fly There!" labels when unvisited
  - Trip progress ("12 of 51 states visited")
  - "Visit Next Stop" button (styled like "Play Today's Quest" — coral background, shadow, `SoundHelper.haptic(.medium)` on tap). Disabled with message when offline (F-RT-011).
  - "Take a Break" button (returns to Home Hub)
  - Tapping a visited state opens the revisit flow (F-RT-008)
  - Long-pressing any state opens the Location Detail Sheet (S-008 via `.sheet(item:)`)
- How to get there: From Home Hub "Road Trip" card via `HomeHubRoute.roadTrip`, or "Continue Trip" from Trip Summary
- Goes to: S-RT-002 (State Visit) or S-003 (Home Hub)
- Navigation: Pushed onto `NavigationStack` via `HomeHubRoute`

### S-RT-002 State Visit
- What's on it:
  - MapKit 3D Flyover view (F-RT-003) — **this is the only screen using MapKit**; all other maps remain SVG-based
  - If `accessibilityReduceMotion`: static satellite view at destination, no animation
  - Look Around card if available, curated landmark photo if not (F-RT-004)
  - State Visit Card with facts (F-RT-005)
  - "Start Challenges" button (works even if offline — challenges use bundled data)
- How to get there: From S-RT-001 (tapping a state or "Visit Next Stop")
- Goes to: S-RT-003 (Challenges)
- Navigation: Pushed onto `NavigationStack` path

### S-RT-003 Road Trip Challenges
- What's on it:
  - Reuses existing `QuizSessionView` (S-004) with a `QuizSession` built by `QuizSession.buildRoadTripVisit()`
  - All existing quiz UI applies: progress indicator, question rendering, hint button (F-006), "I need help" (F-007), feedback overlay, `SoundHelper.playCorrect()`/`playWrong()` + haptics
  - `isPracticeOnly: false` — results update `LearningProgressStore`
  - Session kind: `.roadTrip` (set via `learningProgressStore.startSession(kind: .roadTrip)`)
- How to get there: From S-RT-002 ("Start Challenges")
- Goes to: S-RT-004 (State Unlock) on completion

### S-RT-004 State Unlock
- What's on it:
  - Stamp animation (F-RT-007), then Champ confetti if applicable (sequenced, not simultaneous)
  - State name + "Visited!"
  - Quick stats (questions correct, hint usage — from `QuizSession.results`)
  - Next stop suggestions with AK/HI "Fly There!" options (F-RT-010)
  - "Continue Trip" / "Take a Break"
- How to get there: After completing challenges in S-RT-003
- Goes to: S-RT-002 (next state) or S-RT-001 (map overview) or S-003 (Home Hub)

### S-RT-005 Trip Summary
- What's on it:
  - Session stats: states visited this session, total trip progress
  - States visited this session (mini `StateShapeView` row)
  - "Continue Trip" / "Back to Home"
  - If all 51 visited: "Trip Complete!" with `SoundHelper.playCelebration()` + `ConfettiOverlay` (via conditional rendering)
- How to get there: "Take a Break" or trip completion
- Goes to: S-RT-001 or S-003

---

## 6) Data

### D-RT-001 Road Trip Progress
- Implementation: Create `RoadTripStore.swift` in `Data/` as a new `@MainActor @Observable` store (following `LearningProgressStore` / `StreakStore` pattern). Inject via SwiftUI Environment with a new `EnvironmentKey`.
- **Add `@State private var roadTripStore = RoadTripStore()` to `MapSproutApp.swift`** alongside existing stores. Call `await roadTripStore.rebuild()` during the app bootstrap sequence (after `LearningProgressStore.rebuild()`). Inject via `.environment(\.roadTripStore, roadTripStore)`.
- Persisted fields:
  - `tripID: String` (UUID, namespaces each trip's events)
  - `visitedStops: [RoadTripStop]` (abbreviation + timestamp, ordered)
  - `currentAbbreviation: String?` (current/last stop)
  - `tripStatus: TripStatus` (`.active` / `.completed`)
  - `completedTrips: [CompletedTrip]` (history of past trips — `tripID`, completion date, visit count)
- **Event schema — `RoadTripEvent`:**
  ```swift
  struct RoadTripEvent: Codable, Sendable {
      let id: String           // UUID
      let tripID: String       // which trip this belongs to
      let abbreviation: String // state visited
      let timestamp: Date
      let kind: Kind           // .visitCompleted, .tripStarted, .tripCompleted

      enum Kind: String, Codable {
          case tripStarted
          case visitCompleted
          case tripCompleted
      }
  }
  ```
- **Persistence — `LocalUserDataStore` modifications required:**
  - `LocalUserDataStore` is a closed `actor` with hardcoded arrays/URLs for each event type. Adding `RoadTripEvent` requires:
    - New `roadTripEvents: [RoadTripEvent]` in-memory array
    - New file URL (`road_trip_events.json`)
    - `appendRoadTripEvent(_ event: RoadTripEvent)` method
    - `mergeRemoteRoadTripEvents(_ events: [RoadTripEvent])` method
    - Updates to `loadAll()` and `deleteAllUserData()`
  - **Estimated scope:** ~80 lines of new code in `LocalUserDataStore.swift`
- **CloudKit sync — `CloudSyncCoordinator` modifications required:**
  - Add `"RoadTripEvent"` as a new `recordType` in `nextRecordZoneChangeBatch`, `handleFetchedChanges`, `handleSentChanges`, `schedulePendingUploads`, and `PendingState`
  - **Estimated scope:** ~40 lines of new code across 5 methods
- **`AnswerEvent.SessionKind` extension:** Add `.roadTrip` case to the enum in `SyncModels.swift` (currently has `.baseline`, `.todaysQuest`, `.practice`, `.migration`). Handle in `LearningProgressStore.rebuild()` the same way `.todaysQuest` is handled (updates `correctCount`/`wrongCount`).
- Rebuild: `func rebuild() async` replays `RoadTripEvent` list to reconstruct active trip and completed trips history.

### D-RT-002 Road Trip Question Bank
- For each state: 8-12 curated challenge questions
- `RoadTripQuestionBuilder` reads from JSON and produces `Question` objects using **existing `QuestionKind` values** (does NOT add new enum cases — see F-RT-006):
  ```json
  {
    "CA": [
      {
        "prompt": "You just flew over the Golden State. What is its capital?",
        "kind": "locationToCapital",
        "correct": "Sacramento",
        "choices": ["Sacramento", "Los Angeles", "San Francisco", "San Diego"],
        "hint": "It's in central California, near the Sierra Nevada",
        "tier": 1
      }
    ]
  }
  ```
  Note: `kind` values in the JSON map to existing `QuestionKind` raw values (`locationToCapital`, `capitalToLocation`, `tapLocationOnMap`, `identifyHighlighted`, `typeTheName`). The custom prompt text provides the road trip flavor.
- Total: ~500-600 questions (51 locations x 8-12 each)
- Format: `road_trip_questions.json`, loaded via a new `RoadTripContentLoader` (separate from `StateContentLoader`, since this returns `[String: [RoadTripQuestion]]` — a dictionary of arrays, which matches the existing `loadDictionary<T>` pattern where `T = [RoadTripQuestion]`)

### D-RT-003 Route Definitions (DEFERRED to v1.1)
- Themed route definitions are not needed for v1 (Free Roam only).
- When added in v1.1, load from `road_trip_routes.json`. Note: `StateContentLoader.loadDictionary` returns `[String: T]?` but route JSON is an array `[RouteDefinition]`. Will need a new `loadArray<T>(filename:bundle:)` method added to the loader, or a separate `RouteLoader`.

### D-RT-004 Landmark Photos (Look Around Fallback)
- One curated photo per state: a recognizable landmark or capital scene
- Used when Look Around is unavailable (~30-40% of capitals expected)
- Format: bundled asset catalog images, keyed by state abbreviation
- **Must be sourced during content creation phase (B-RT-006)**

### D-001 Extension: Capital Coordinates
- Add `capitalLatitude: Double?` and `capitalLongitude: Double?` to `StateInfo` struct as **optionals with `nil` default** (matching `statehoodDate`/`statehoodOrder` pattern). Using non-optional `Double` would crash decoding if any entry is missing.
- Add corresponding cases to `StateInfo.CodingKeys`: `case capitalLatitude = "capital_latitude"`, `case capitalLongitude = "capital_longitude"` (matching existing snake_case convention: `map_shape_id`, `statehood_date`)
- Extend `states.json` with coordinates for all 51 capitals
- Used exclusively by the MapKit flyover (F-RT-003)

---

## 7) Look Around Coverage

Not all state capitals have Look Around data. **Expect ~60-70% coverage** (30-40% of capitals will lack street-level imagery).

- **Likely covered:** Sacramento, Austin, Columbus, Albany, Tallahassee, Raleigh, Nashville, Denver, and other large/medium capitals
- **Likely NOT covered:** Montpelier VT, Pierre SD, Frankfort KY, Carson City NV, Juneau AK, and other small/rural capitals
- **Fallback strategy:** Curated landmark photo + capital name + one-line landmark description (D-RT-004). The flyover animation always works — 3D terrain/satellite has much broader coverage than street-level.

**Action required:** Build a `#Preview` or test utility that runs `MKLookAroundSceneRequest` for all 51 capital coordinates and outputs a coverage report. Do this in Week 1 of development (B-RT-004).

**Go/no-go threshold:** If fewer than 25 of 51 capitals have Look Around coverage (<50%), evaluate whether the feature adds enough value to ship. The flyover is the primary wow factor; Look Around is a bonus. If coverage is too low, deprioritize the Look Around card and invest more in the curated photo fallback quality.

---

## 8) Integration with Existing Features

8.1 Road Trip challenges update mastery via `LearningProgressStore.recordResult()` (F-009). **Must call `startSession(kind: .roadTrip)` first** — requires adding `.roadTrip` to `AnswerEvent.SessionKind`.
8.2 Road Trip sessions count toward daily streak via `StreakStore.recordSessionCompleted()` (F-015). Call after first state visit of a session. Note: deduplicates by calendar day — Road Trip + Today's Quest on same day = one streak day.
8.3 States unlocked in Road Trip reflect on the Explore Map (S-006) — `MasteryUSMapView` reads from `LearningProgressStore`, so mastery changes from Road Trip automatically appear.
8.4 The Location Detail Sheet (S-008 / `LocationDetailSheetView`) is accessible from Road Trip Map via `.sheet(item:)` on long-press, matching the Explore Map pattern.
8.5 Road Trip progress syncs via `CloudSyncCoordinator` — add `RoadTripEvent` as a new synced record type (see D-RT-001 for scope).
8.6 Road Trip does NOT replace existing quiz modes — Today's Quest (F-004) and Practice remain available from Home Hub.
8.7 Road Trip is a **premium feature**: gated by `entitlementStore.state`. Visible during trial and active subscription. Hidden when expired.

---

## 9) New & Modified Files

### New Files
| File | Location | Type | Purpose |
|------|----------|------|---------|
| `RoadTripStore.swift` | `Data/` | Store | `@Observable` trip progress (D-RT-001) |
| `RoadTripEvent.swift` | `Data/` | Model | Event type for trip persistence |
| `RoadTripContentLoader.swift` | `Data/` | Loader | Loads road trip questions JSON (D-RT-002) |
| `RoadTripQuestionBuilder.swift` | `Shared/` | Builder | Constructs `Question` from road trip JSON |
| `RoadTripMapView.swift` | `Views/` | Screen | S-RT-001 — route map with SVG overlay |
| `StateVisitView.swift` | `Views/` | Screen | S-RT-002 — flyover + Look Around + facts |
| `StampUnlockView.swift` | `Views/` | Screen | S-RT-004 — unlock celebration |
| `TripSummaryView.swift` | `Views/` | Screen | S-RT-005 — session/trip summary |
| `StateFlyoverView.swift` | `Components/` | Component | MapKit 3D camera flyover (F-RT-003) |
| `LookAroundCardView.swift` | `Components/` | Component | Look Around preview + viewer (F-RT-004) |
| `StateVisitCardView.swift` | `Components/` | Component | Compact fact card (F-RT-005) |
| `NextStopSuggestionView.swift` | `Components/` | Component | Neighbor state chips + AK/HI (F-RT-010) |
| `road_trip_questions.json` | bundle | Data | Curated question bank (D-RT-002) |
| Landmark photos (51) | Assets.xcassets | Images | Look Around fallback (D-RT-004) |

S-RT-003 (Challenges) reuses existing `QuizSessionView` — no new file needed.

### Modified Files
| File | Change |
|------|--------|
| `HomeHubView.swift` | Add `.roadTrip` to `HomeHubRoute`, add `navigationDestination` branch, add action card |
| `MapSproutApp.swift` | Add `RoadTripStore` as `@State`, call `rebuild()` in bootstrap, inject via `.environment()` |
| `LocalUserDataStore.swift` | Add `roadTripEvents` array, file URL, append/merge methods, update `loadAll()`/`deleteAllUserData()` (~80 lines) |
| `CloudSyncCoordinator.swift` | Add `RoadTripEvent` record type handling in 5 methods (~40 lines) |
| `SyncModels.swift` | Add `.roadTrip` case to `AnswerEvent.SessionKind` |
| `LearningProgressStore.swift` | Handle `.roadTrip` session kind in `rebuild()` (same as `.todaysQuest`) |
| `StateInfo.swift` | Add optional `capitalLatitude`/`capitalLongitude` + `CodingKeys` entries |
| `states.json` | Add `capital_latitude`/`capital_longitude` for all 51 locations |
| `QuizSession.swift` | Add `static func buildRoadTripVisit(for:allStates:visitCount:)` factory method |

---

## 10) Build Steps

- **B-RT-001** Extend `StateInfo` with optional capital coordinates + `CodingKeys`. Update `states.json`. Create `RoadTripEvent` model. Modify `LocalUserDataStore` (~80 lines) and `CloudSyncCoordinator` (~40 lines) to support `RoadTripEvent`. Add `.roadTrip` to `AnswerEvent.SessionKind`. Create `RoadTripStore` with event-sourced persistence. Wire up in `MapSproutApp.swift`. _(D-001 ext, D-RT-001)_
- **B-RT-002** Add `HomeHubRoute.roadTrip` + `navigationDestination` branch + Road Trip card to `HomeHubView`. Build `RoadTripMapView` (S-RT-001) with SVG map, AK/HI "Fly There!" labels, and connectivity check (F-RT-011). _(S-RT-001, F-RT-001, F-RT-002, F-RT-011)_
- **B-RT-003** Build `StateFlyoverView` (F-RT-003) — SwiftUI `Map` with `mapCameraKeyframeAnimator`. Implement `accessibilityReduceMotion` static fallback. Test all 51 capitals for 3D terrain availability. _(F-RT-003)_
- **B-RT-004** Build `LookAroundCardView` (F-RT-004) — `MKLookAroundSceneRequest` + `LookAroundPreview` + `.lookAroundViewer()`. **Run coverage audit for all 51 capitals and document results.** Build curated photo fallback. _(F-RT-004)_
- **B-RT-005** Build `StateVisitView` (S-RT-002) — compose flyover, Look Around/photo fallback, and `StateVisitCardView` (F-RT-005). _(S-RT-002, F-RT-005)_
- **B-RT-006** Create `road_trip_questions.json` (D-RT-002) — write 8-12 questions per state using existing `QuestionKind` raw values. Source 51 landmark photos (D-RT-004). Build `RoadTripContentLoader` and `RoadTripQuestionBuilder`. Add `QuizSession.buildRoadTripVisit()` factory. _(D-RT-002, D-RT-004)_ **[Content-heavy — can parallelize with B-RT-003/004. Note: B-RT-007 depends on this completing.]**
- **B-RT-007** Wire S-RT-003 (Challenges) — invoke existing `QuizSessionView` with road trip `QuizSession`. Call `startSession(kind: .roadTrip)` before recording. Verify hints, "I need help", feedback, and mastery updates work. _(S-RT-003, F-RT-006)_ **[Blocked by B-RT-006]**
- **B-RT-008** Build `StampUnlockView` (S-RT-004) with stamp animation + sequenced Champ confetti. Build `NextStopSuggestionView` with AK/HI "Fly There!" options (F-RT-010). Implement revisit flow (F-RT-008). _(S-RT-004, F-RT-007, F-RT-008, F-RT-010)_
- **B-RT-009** Build `TripSummaryView` (S-RT-005). Wire streak credit via `StreakStore.recordSessionCompleted()` (F-RT-012). _(S-RT-005, F-RT-012)_
- **B-RT-010** Integration: verify `LearningProgressStore` mastery updates with `.roadTrip` session kind, `CloudSyncCoordinator` sync for `RoadTripEvent`, `MasteryUSMapView` cross-reference, entitlement gating. _(Section 8)_
- **B-RT-011** Polish: flyover-to-facts transitions, sound design (consider new `SoundHelper.playFlyover()` synthesized sound), dark mode, `accessibilityReduceMotion` end-to-end test.

---

## 11) Effort Estimate

| Phase | Scope | Estimate |
|-------|-------|----------|
| Data layer + StateInfo extension + RoadTripStore + LocalUserDataStore + CloudSync | B-RT-001 | 1 week |
| MapKit integration (Flyover + Look Around + coverage audit) | B-RT-003, B-RT-004 | 1-2 weeks |
| Road Trip Map + Home Hub card + connectivity gate | B-RT-002 | 3-4 days |
| State Visit flow (screens + transitions) | B-RT-005, B-RT-007, B-RT-008 | 1-2 weeks |
| Question bank + landmark photos content creation | B-RT-006 | 2-3 weeks (can parallelize) |
| Integration + polish | B-RT-009, B-RT-010, B-RT-011 | 1 week |
| **Total** | | **6-8 weeks** |

Content creation (500-600 questions + 51 landmark photos) is the bottleneck and can run in parallel with engineering work starting from B-RT-003.

---

## 12) Future Enhancements (Not in v1)

- **Themed routes** (Coast to Coast, Southern Swing, Route 66, National Parks) — requires `road_trip_routes.json` + route picker UI + route-aware next-stop logic + route switching/abandonment flow. Deferred from v1 to reduce scope.
- **Route line overlay** — connecting lines between visited state centroids on the Road Trip Map. Deferred for visual simplicity in v1.
- **Trip history in Passport** — completed trips displayed in the Passport screen (S-007). Requires Passport UI update.
- **Foundation Models integration** — AI-generated follow-up questions for Apple Intelligence devices, with curated fallback for older devices
- **Multiplayer road trip** — Race a friend to visit all states first (SharePlay)
- **Widgets** — "Next stop on your road trip: Montana!" on the Home Screen (WidgetKit)
- **Siri Shortcuts** — "Hey Siri, continue my road trip" (App Intents)
- **World geography expansion** — Same mechanic applied to countries/continents
