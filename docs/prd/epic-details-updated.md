# Epic Details (Updated with PO Recommendations)

## Epic 1: Foundation & Core Data Pipeline

**Goal:** Establish the foundational architecture with data storage, source configuration, article fetching capabilities, AND the basic UI shell. This epic delivers a functional news fetcher with a minimal but working interface, providing the foundation for visual enhancements in Epic 2.

### Story 1.1: Project Setup & Core Architecture

**As a** developer,
**I want** to set up the Xcode project with proper architecture, dependencies, and coding standards,
**so that** I have a solid foundation for building all features with consistent quality.

**Acceptance Criteria:**
1. Xcode project created with macOS 11.0+ deployment target
2. Project structure follows the defined architecture (Core, UI, Services, Storage, Utilities folders)
3. Swift Package Manager configured with initial dependencies (FeedKit, SwiftSoup, SQLite.swift)
4. Git repository initialized with .gitignore for Xcode/Swift projects
5. Basic app launches successfully showing a window with placeholder text
6. Build configurations set up for Debug and Release
7. Project compiles without warnings on both Intel and Apple Silicon
8. **NEW:** `.swiftlint.yml` configured with documentation standards
9. **NEW:** `Documentation/coding-standards.md` created with Swift commenting conventions
10. **NEW:** Xcode snippets configured for documentation comments

**Developer Notes:**
```swift
// Required SwiftLint configuration for documentation:
documentation_comments:
  - warning: Missing documentation for public interfaces
  - exclude: Tests, Views
```

### Story 1.2: Basic UI Shell & Window with Error Handling Foundation

**As a** user,
**I want** to see a basic application window with layout structure and error handling,
**so that** I can visualize where content will appear and understand any issues.

**Acceptance Criteria:**
1. Main window created with 900x700 default size (resizable)
2. Basic toolbar with "Refresh" button (non-functional initially)
3. Empty scrollable content area where cards will appear
4. Placeholder text "No articles loaded" in content area
5. Status bar showing "Ready" or "Fetching..." states
6. Window position and size persist between launches
7. Basic menu bar with standard macOS menus (File, Edit, View, Window, Help)
8. UI responsive and renders correctly on both Retina and non-Retina displays
9. **NEW:** Error message infrastructure implemented with `UserFacingError` enum
10. **NEW:** Toast/alert presentation system for user feedback

**Developer Notes:**
```swift
// Error handling structure to implement:
enum UserFacingError {
    case feedUnavailable(source: String)
    case networkTimeout
    case parsingFailed(details: String)
    case storageError
    
    var userMessage: String { /* Friendly messages */ }
    var technicalDetails: String { /* For logs */ }
    var recoveryAction: String? { /* What user can do */ }
}
```

### Story 1.3: SQLite Database Setup with Migration Framework

**As a** system,
**I want** to establish SQLite database with schema for articles and sources and migration capability,
**so that** I can persist news data between sessions and evolve the schema safely.

**Acceptance Criteria:**
1. SQLite database created in Application Support directory
2. Articles table created with all required fields (id, source_id, url, title, content, dates)
3. Sources table created with fields (id, name, url, type, enabled, last_fetch)
4. FTS5 virtual table configured for full-text search on articles
5. **UPDATED:** Database migrations framework implemented with version tracking
6. Basic CRUD operations working for both tables
7. Database file properly backed up by Time Machine
8. **NEW:** Migration test suite with dry-run capability
9. **NEW:** `Database/Migrations/` directory structure established
10. **NEW:** Version tracking table in database

**Developer Notes:**
```swift
// Migration framework structure:
class DatabaseMigrator {
    static let currentVersion = 1
    
    func migrate(from oldVersion: Int, to newVersion: Int) async throws {
        for version in (oldVersion + 1)...newVersion {
            try await applyMigration(version)
        }
    }
}
```

### Story 1.4: RSS Feed Parser Implementation with Resilient Fallback

**As a** user,
**I want** the system to parse RSS/Atom feeds with fallback strategies,
**so that** I can add RSS-based news sources reliably even with malformed feeds.

**Acceptance Criteria:**
1. FeedKit integrated and successfully parsing sample RSS feeds
2. Parser extracts title, summary, content, publication date, and link from feed items
3. Support for both RSS 2.0 and Atom 1.0 formats
4. **UPDATED:** Three-tier parsing strategy implemented:
   - Full Parse: Complete article with description
   - Partial Parse: Title, link, date only
   - Minimal Parse: Title and link only
5. Test coverage with at least 5 different real news site RSS feeds
6. Articles from RSS feeds saved to database with correct source association
7. **NEW:** Parse result enum indicates quality level (full/partial/degraded)
8. **NEW:** Source health monitoring tracks parse success rates
9. **NEW:** Test suite includes malformed/partial feeds

**Developer Notes:**
```swift
// Implement resilient parser:
enum ParseResult {
    case success([Article])
    case partial([Article], warning: String)
    case degraded([Article])
    case failure(FeedSource)
}
```

### Story 1.5: Web Scraper Implementation with Error Recovery

**As a** user,
**I want** the system to extract articles from regular web pages with fallback strategies,
**so that** I can add non-RSS news sources reliably.

**Acceptance Criteria:**
1. WebKit WebView configured for headless article extraction
2. SwiftSoup successfully parsing HTML from sample news sites
3. Intelligent content extraction identifying article title, body, and publish date
4. JavaScript-rendered content properly loaded before extraction
5. **UPDATED:** Multiple fallback extraction strategies when primary selectors fail
6. Successfully extracts articles from at least 3 major news sites (CNN, BBC, TechCrunch)
7. Extracted articles saved to database with proper formatting
8. **NEW:** Selector discovery process documented
9. **NEW:** Fallback to RSS-only content when scraping fails
10. **NEW:** User notification of degraded content quality

### Story 1.6: Fetch Orchestration with Basic UI Feedback

**As a** user,
**I want** all my configured sources to be fetched with visual progress indication,
**so that** I know the app is working and can understand any issues.

**Acceptance Criteria:**
1. FetchCoordinator manages concurrent fetching from multiple sources
2. Refresh button in toolbar triggers fetch operation
3. Status bar updates to show "Fetching X of Y sources..."
4. Progress bar appears during fetch operation
5. **UPDATED:** Failed sources shown with specific error types in status
6. Simple list view shows fetched article titles (no cards yet)
7. All fetched articles appear in list within 10 seconds total
8. List updates live as articles are fetched and stored
9. **NEW:** Error recovery suggestions shown for failed sources
10. **NEW:** Logging framework captures technical details for debugging

**UI Checkpoint:** At the end of Epic 1, we have a working app that can fetch and display articles in a basic list format with robust error handling.

## Epic 2: Visual Intelligence System

**Goal:** Transform the basic list into the signature card-based visual intelligence system with color-coded keywords. This epic delivers the complete UI experience and core differentiator of the product.

### Story 2.1: Article Card Component UI with Error States

**As a** user,
**I want** to see articles displayed as beautiful cards with clear error states,
**so that** I can quickly scan through news content and understand any issues.

**Acceptance Criteria:**
1. Replace list view with card-based layout
2. Cards show title (16pt font) and summary (14pt font)
3. Source name and publish date displayed as metadata (12pt, gray)
4. Cards have consistent height (120pt) and responsive width
5. Smooth hover state with subtle shadow (0.95 scale, 5pt shadow)
6. Cards layout in responsive grid (1-3 columns based on window width)
7. Smooth scrolling at 60fps with up to 200 cards
8. Loading placeholder cards shown while content loads
9. **NEW:** Error state cards for failed article loads
10. **NEW:** Degraded content indicator for partial parses
11. **NEW:** Retry button on error cards

### Story 2.2: Keyword Configuration UI with Validation

**As a** user,
**I want** a polished settings panel to configure keywords with colors and validation,
**so that** I can easily customize my tracking with helpful guidance.

**Acceptance Criteria:**
1. Settings window opens via Cmd+, or Preferences menu
2. Keywords tab shows current keywords in a list
3. "+" button opens popover with text field and color picker
4. macOS native color picker with preset swatches
5. Keywords displayed with their colors as preview
6. Delete button (trash icon) removes keywords
7. Changes apply immediately to main window
8. Maximum 5 keywords enforced with clear error message
9. **NEW:** Keyword validation (min/max length, special characters)
10. **NEW:** Suggested keywords based on recent articles
11. **NEW:** Warning for keywords with poor color contrast

### Story 2.3: Keyword Highlighting in Cards with Accessibility

**As a** user,
**I want** matching keywords to be highlighted with accessible colors in card text,
**so that** I can instantly spot topics of interest regardless of vision abilities.

**Acceptance Criteria:**
1. Keywords in titles highlighted with configured background color
2. Keywords in summaries highlighted with same background color
3. **UPDATED:** Highlighting ensures WCAG AA contrast ratio (4.5:1)
4. Multiple keywords in same text show with their respective colors
5. Partial word matches handled correctly (configurable per keyword)
6. Performance remains smooth even with 5 keywords across 200 cards
7. Highlighting updates immediately when keywords are changed
8. **NEW:** VoiceOver announces keyword matches in cards
9. **NEW:** High contrast mode option for accessibility
10. **NEW:** Underline option as alternative to color highlighting

### Story 2.4: Visual Card Indicators

**As a** user,
**I want** cards containing keywords to have colored borders or badges,
**so that** I can identify relevant articles at a glance without reading.

**Acceptance Criteria:**
1. Cards with matching keywords show 3pt colored left border
2. Border color matches the keyword's configured color
3. Multiple keyword matches show the highest priority keyword color
4. Small badge in top-right shows count if multiple keywords match
5. Cards without keywords have subtle gray border
6. Visual indicators visible in both light and dark mode
7. Color patterns create clear visual rhythm across card grid
8. **NEW:** Option to customize indicator style (border/badge/both)
9. **NEW:** Accessibility labels describe visual indicators

### Story 2.5: Complete Visual Polish with Accessibility Testing

**As a** user,
**I want** a polished, native macOS interface that works for everyone,
**so that** the app feels professional and inclusive.

**Acceptance Criteria:**
1. Keyword legend bar at top shows all active keywords with colors and counts
2. Toolbar refined with segmented control for view density (Comfortable/Compact)
3. Empty state illustration when no articles loaded
4. Loading states with skeleton cards during fetch
5. Error states with helpful messages and retry buttons
6. All animations smooth and purposeful (no jank)
7. Supports both light and dark mode following system preference
8. Window chrome uses macOS Big Sur+ style with proper materials
9. **NEW:** Full keyboard navigation implemented
10. **NEW:** Accessibility audit passing WCAG AA
11. **NEW:** XCUITest accessibility test suite created

**Developer Notes:**
```swift
// Add accessibility testing:
func testKeywordHighlightingAccessibility() {
    XCTAssertEqual(
        app.cells.firstMatch.accessibilityLabel,
        "Technology article, 3 keyword matches, 2 hours ago"
    )
}
```

**UI Checkpoint:** At the end of Epic 2, the complete visual interface is implemented, polished, and accessible.

## Epic 3: User Configuration & Persistence

**Goal:** Create comprehensive settings management with great onboarding and validate the complete MVP through manual testing. This epic makes the application production-ready for daily use.

### Story 3.1: Source Management Interface with Health Monitoring

**As a** user,
**I want** to add and configure news sources with health monitoring,
**so that** I can maintain a reliable set of sources.

**Acceptance Criteria:**
1. Settings window Sources tab shows table of configured sources
2. Add button opens sheet with URL field and type selector
3. Test button validates source with spinner and success/error feedback
4. Sources show status indicators (green = working, red = error, gray = disabled)
5. Enable/disable toggle for each source
6. Delete button with confirmation alert
7. Drag to reorder sources
8. Changes save automatically and persist
9. **NEW:** Source health metrics shown (success rate, last successful fetch)
10. **NEW:** Automatic disable option for consistently failing sources
11. **NEW:** Suggested alternative sources for failing ones

### Story 3.2: Source Auto-Detection with Smart Suggestions

**As a** user,
**I want** the system to intelligently detect and suggest feed URLs,
**so that** setup is quick and easy.

**Acceptance Criteria:**
1. When user enters main site URL, system checks for RSS autodiscovery
2. Common RSS URL patterns checked (/feed, /rss, /atom, etc.)
3. Link tags in HTML head parsed for feed references
4. If multiple feeds found, user prompted to select
5. If no feed found, falls back to web scraping mode
6. Detection process completes within 3 seconds
7. Clear feedback provided on detection results
8. **NEW:** Popular sources quick-add gallery
9. **NEW:** Category-based source suggestions
10. **NEW:** Import OPML file option

### Story 3.3: Complete Preferences Window

**As a** user,
**I want** a comprehensive preferences window for all settings,
**so that** I can fully customize the app.

**Acceptance Criteria:**
1. Preferences window with tabs (General, Sources, Keywords, Advanced)
2. General tab: launch at login, default article count, article age limit
3. Sources tab: full source management (from Story 3.1)
4. Keywords tab: keyword configuration (from Epic 2)
5. Advanced tab: cache management, debug logging, reset to defaults
6. All preferences persist using UserDefaults
7. Changes apply immediately without restart
8. Window size and selected tab remembered
9. **NEW:** Export/import settings option
10. **NEW:** Preferences search field for finding settings

### Story 3.4: Enhanced First-Run Experience with Onboarding

**As a** user,
**I want** a delightful first-run experience with visual onboarding,
**so that** I understand the value immediately.

**Acceptance Criteria:**
1. **UPDATED:** Welcome window with animated value proposition
2. Step 1: "Your News, Visualized" intro with keyword animation
3. Step 2: Add sources with popular suggestions grid
4. Step 3: Configure keywords with smart suggestions
5. Step 4: Visual tutorial showing color-coding in action
6. Skip option available for experienced users
7. Sample data option to load demo sources and keywords
8. Welcome window doesn't show again after completion
9. **NEW:** Time to first value under 2 minutes
10. **NEW:** Help tooltips on first use of major features
11. **NEW:** "Quick Start" preset configurations

**Developer Notes:**
```swift
// Onboarding flow structure:
struct OnboardingView {
    let pages = [
        OnboardingPage(
            title: "Your News, Visualized",
            description: "See patterns in seconds with color-coded keywords",
            animation: "keyword-highlight-demo"
        ),
        // Additional pages...
    ]
}
```

### Story 3.5: Manual Testing & Validation

**As a** product owner,
**I want** comprehensive manual testing of the MVP,
**so that** I can verify all features work correctly.

**Acceptance Criteria:**
1. Test with 15+ configured news sources
2. Test with 5 keywords with different colors
3. Verify 200+ articles load and display correctly
4. Test on both Intel and Apple Silicon Macs
5. Test in both light and dark modes
6. Verify memory usage stays under 200MB
7. Confirm 60fps scrolling performance
8. Test all error states and recovery paths
9. **NEW:** Accessibility testing with VoiceOver
10. **NEW:** Automated test suite execution in CI/CD
11. **NEW:** Performance profiling with Instruments

### Story 3.6: Production Readiness & Documentation

**As a** developer/user,
**I want** complete documentation and production setup,
**so that** the app is ready for distribution.

**Acceptance Criteria:**
1. README.md with setup and usage instructions
2. In-app help documentation
3. Troubleshooting guide for common issues
4. App notarization with Apple
5. DMG creation script for distribution
6. Version 1.0 release notes prepared
7. **NEW:** Error message documentation
8. **NEW:** API documentation for future extensions
9. **NEW:** Migration guide for future updates
10. **NEW:** Support email template responses

**MVP Checkpoint:** At the end of Epic 3, we have a complete, production-ready news aggregator with visual intelligence.

## Epic 4: AI Enhancement & Intelligence (Phase 2)

**Goal:** Integrate OpenAI capabilities to provide intelligent summaries, agent-based research, and enhanced keyword suggestions. This epic transforms the aggregator into an intelligent news assistant.

### Story 4.1: OpenAI Integration Foundation

**As a** developer,
**I want** to integrate OpenAI API with proper error handling,
**so that** AI features can be built reliably.

**Acceptance Criteria:**
1. OpenAI SDK integrated via Swift Package Manager
2. API key storage in Keychain
3. Rate limiting implementation (max 100 requests/minute)
4. Error handling for API failures with user feedback
5. Fallback to non-AI mode when API unavailable
6. Token usage tracking and display
7. **NEW:** Cost estimation and warnings
8. **NEW:** Retry logic with exponential backoff
9. Model selection limited to gpt-4o-mini as specified

### Story 4.2: Article Summarization

**As a** user,
**I want** AI-generated summaries of articles,
**so that** I can understand content even faster.

**Acceptance Criteria:**
1. "Summarize" button on article cards
2. Summary generated using OpenAI API
3. Summary appears in expandable section of card
4. Summary limited to 2-3 sentences
5. Loading state while summary generates
6. Summaries cached to avoid repeated API calls
7. Batch summarization option for multiple articles
8. **NEW:** Summary quality feedback mechanism

### Story 4.3: OpenAI Agents Integration

**As a** user,
**I want** AI agents to research and surface important information,
**so that** I don't miss critical news.

**Acceptance Criteria:**
1. OpenAI Agents SDK integrated as specified
2. Research agent configured for news analysis
3. Agent runs periodically (configurable interval)
4. Agent highlights important articles
5. Agent suggests new keywords based on trends
6. Agent activity log visible in preferences
7. **NEW:** Agent actions require user confirmation
8. Manual trigger option for agent analysis

### Story 4.4: Smart Keyword Suggestions

**As a** user,
**I want** AI-suggested keywords based on my reading patterns,
**so that** I can discover relevant topics.

**Acceptance Criteria:**
1. AI analyzes read articles for common themes
2. Keyword suggestions appear in settings
3. One-click addition of suggested keywords
4. Suggestions refresh weekly
5. Explanation provided for each suggestion
6. **NEW:** Trending topics in your sources highlighted
7. Opt-out option for AI suggestions

### Story 4.5: Intelligent Article Ranking

**As a** user,
**I want** articles ranked by relevance to my interests,
**so that** I see the most important news first.

**Acceptance Criteria:**
1. AI scores articles based on keyword matches and reading history
2. Sort option for "Relevance" in toolbar
3. Relevance score shown as small indicator
4. Manual override to pin important articles
5. Learning improves over time
6. **NEW:** Explanation of ranking factors available
7. Option to disable AI ranking

## Epic 5: Advanced Visualization & Analytics (Phase 2)

**Goal:** Provide multiple view modes and analytics to help users understand trends and patterns in their news consumption.

### Story 5.1: List View Mode

**As a** user,
**I want** a compact list view option,
**so that** I can see more articles at once.

**Acceptance Criteria:**
1. Toggle between card and list views
2. List shows title, source, date, keyword indicators
3. Maintain 60fps scrolling performance
4. Keyboard navigation support
5. Selection and multi-select support
6. **NEW:** Customizable list columns
7. Sort options in list headers

### Story 5.2: Keyword Trend Analytics

**As a** user,
**I want** to see keyword frequency over time,
**so that** I can understand news trends.

**Acceptance Criteria:**
1. Analytics tab in main window
2. Line chart showing keyword frequency by day/week
3. Stacked area chart for multiple keywords
4. Date range selector
5. Export chart as image
6. **NEW:** Correlation analysis between keywords
7. Peak detection and annotations

### Story 5.3: Source Analytics

**As a** user,
**I want** to understand which sources provide the most valuable content,
**so that** I can optimize my source list.

**Acceptance Criteria:**
1. Source statistics dashboard
2. Articles per source breakdown
3. Keyword matches per source
4. Source reliability metrics
5. Recommendations to add/remove sources
6. **NEW:** Source bias analysis
7. Reading time per source tracking

### Story 5.4: Advanced Filtering

**As a** user,
**I want** powerful filtering options,
**so that** I can focus on specific content.

**Acceptance Criteria:**
1. Filter bar with multiple criteria
2. Filter by date range, source, keyword
3. Exclude filters (NOT conditions)
4. Save filter presets
5. Quick filter buttons for common queries
6. **NEW:** Natural language filter input
7. Filter history/recent filters

### Story 5.5: Export & Reporting

**As a** user,
**I want** to export my news data and create reports,
**so that** I can share insights with others.

**Acceptance Criteria:**
1. Export articles to markdown/PDF
2. Generate weekly digest email
3. Export analytics charts
4. OPML export of sources
5. JSON export for data analysis
6. **NEW:** Automated report scheduling
7. Custom report templates

## Epic 6: Ambient Awareness & Extensions (Phase 3)

**Goal:** Extend the application beyond the main window to provide ambient awareness of important news throughout the day.

### Story 6.1: Menu Bar Integration

**As a** user,
**I want** a menu bar icon showing news status,
**so that** I can monitor news without the main window open.

**Acceptance Criteria:**
1. Menu bar icon with unread count badge
2. Click to show recent articles dropdown
3. Keyword match indicators in dropdown
4. Quick access to refresh
5. Preferences accessible from menu
6. **NEW:** Customizable menu bar display
7. Do not disturb mode

### Story 6.2: Today Widget

**As a** user,
**I want** a Today widget showing top articles,
**so that** I can see news from notification center.

**Acceptance Criteria:**
1. Widget shows 3-5 top articles
2. Keyword highlighting preserved
3. Click to open main app
4. Refresh on notification center open
5. Size variants (small, medium, large)
6. **NEW:** Widget configuration options
7. Performance optimized for battery

### Story 6.3: Keyboard Shortcuts & Quick Actions

**As a** user,
**I want** keyboard shortcuts for common actions,
**so that** I can navigate efficiently.

**Acceptance Criteria:**
1. Cmd+R to refresh
2. Cmd+F for search
3. Cmd+1/2/3 for view modes
4. Arrow keys for navigation
5. Space to preview article
6. Customizable shortcuts
7. **NEW:** Shortcuts HUD overlay
8. Services menu integration

### Story 6.4: Notification System

**As a** user,
**I want** notifications for important keyword matches,
**so that** I'm alerted to critical news.

**Acceptance Criteria:**
1. Optional notifications for keyword matches
2. Importance threshold configuration
3. Quiet hours setting
4. Rich notifications with article preview
5. Click to open specific article
6. **NEW:** Notification grouping by keyword
7. Summary notifications option

### Story 6.5: Apple Watch Companion

**As a** user,
**I want** to see news alerts on my Apple Watch,
**so that** I can stay informed on the go.

**Acceptance Criteria:**
1. Watch app with article list
2. Keyword indicators on watch
3. Complication showing unread count
4. Haptic feedback for important news
5. Handoff to Mac
6. **NEW:** Voice summary option
7. Simplified reading mode

### Story 6.6: Mac App Store Release

**As a** product owner,
**I want** to release on the Mac App Store,
**so that** users can easily discover and install the app.

**Acceptance Criteria:**
1. App Store Connect configuration
2. App sandboxing implemented
3. App Store screenshots created
4. App Store description written
5. Review guidelines compliance
6. In-app purchase for Pro features (if applicable)
7. **NEW:** App Store optimization (ASO)
8. Review response templates prepared

---

## Testing Strategy Additions

### Continuous Testing Throughout Development

**New testing requirements added to each epic:**

1. **Unit Tests**: Minimum 80% code coverage for business logic
2. **UI Tests**: Critical user paths covered with XCUITest
3. **Accessibility Tests**: VoiceOver and keyboard navigation verified
4. **Performance Tests**: Memory and CPU profiling with Instruments
5. **Integration Tests**: API mocking and error scenario coverage

### CI/CD Pipeline Requirements

```yaml
# GitHub Actions workflow
name: CI
on: [push, pull_request]
jobs:
  test:
    - run: swift test
    - run: xcodebuild test -scheme UITests
    - run: xcrun accessibility-inspector audit
    - run: swiftlint
```

---

## Documentation Requirements

### Developer Documentation
- API documentation with DocC
- Architecture decision records (ADRs)
- Setup guide for new developers
- Troubleshooting guide

### User Documentation  
- In-app help system
- Video tutorials for key features
- FAQ section
- Support contact information

---

## Success Metrics

### MVP Success Criteria (End of Epic 3)
- ✅ 60% reduction in news consumption time
- ✅ Pattern recognition in under 3 seconds
- ✅ 60fps scrolling with 200+ articles
- ✅ Memory usage under 200MB
- ✅ Launch time under 5 seconds
- ✅ Fetch time under 10 seconds

### Phase 2 Success Criteria (End of Epic 5)
- AI summary satisfaction > 80%
- Agent suggestions acceptance > 50%
- Analytics feature usage > 60%
- User retention > 70% after 30 days

### Phase 3 Success Criteria (End of Epic 6)  
- Menu bar feature usage > 80%
- Notification engagement > 40%
- App Store rating > 4.5 stars
- Organic growth > 20% monthly