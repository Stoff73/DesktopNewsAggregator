# Epic Details

## Epic 1: Foundation & Core Data Pipeline

**Goal:** Establish the foundational architecture with data storage, source configuration, article fetching capabilities, AND the basic UI shell. This epic delivers a functional news fetcher with a minimal but working interface, providing the foundation for visual enhancements in Epic 2.

### Story 1.1: Project Setup & Core Architecture

**As a** developer,
**I want** to set up the Xcode project with proper architecture and dependencies,
**so that** I have a solid foundation for building all features.

**Acceptance Criteria:**
1. Xcode project created with macOS 11.0+ deployment target
2. Project structure follows the defined architecture (Core, UI, Services, Storage, Utilities folders)
3. Swift Package Manager configured with initial dependencies (FeedKit, SwiftSoup, SQLite.swift)
4. Git repository initialized with .gitignore for Xcode/Swift projects
5. Basic app launches successfully showing a window with placeholder text
6. Build configurations set up for Debug and Release
7. Project compiles without warnings on both Intel and Apple Silicon

### Story 1.2: Basic UI Shell & Window

**As a** user,
**I want** to see a basic application window with layout structure,
**so that** I can visualize where content will appear.

**Acceptance Criteria:**
1. Main window created with 900x700 default size (resizable)
2. Basic toolbar with "Refresh" button (non-functional initially)
3. Empty scrollable content area where cards will appear
4. Placeholder text "No articles loaded" in content area
5. Status bar showing "Ready" or "Fetching..." states
6. Window position and size persist between launches
7. Basic menu bar with standard macOS menus (File, Edit, View, Window, Help)
8. UI responsive and renders correctly on both Retina and non-Retina displays

### Story 1.3: SQLite Database Setup

**As a** system,
**I want** to establish SQLite database with schema for articles and sources,
**so that** I can persist news data between sessions.

**Acceptance Criteria:**
1. SQLite database created in Application Support directory
2. Articles table created with all required fields (id, source_id, url, title, content, dates)
3. Sources table created with fields (id, name, url, type, enabled, last_fetch)
4. FTS5 virtual table configured for full-text search on articles
5. Database migrations framework in place for future schema changes
6. Basic CRUD operations working for both tables
7. Database file properly backed up by Time Machine

### Story 1.4: RSS Feed Parser Implementation

**As a** user,
**I want** the system to parse RSS/Atom feeds,
**so that** I can add RSS-based news sources.

**Acceptance Criteria:**
1. FeedKit integrated and successfully parsing sample RSS feeds
2. Parser extracts title, summary, content, publication date, and link from feed items
3. Support for both RSS 2.0 and Atom 1.0 formats
4. Graceful handling of malformed or partial feeds
5. Test coverage with at least 5 different real news site RSS feeds
6. Articles from RSS feeds saved to database with correct source association

### Story 1.5: Web Scraper Implementation

**As a** user,
**I want** the system to extract articles from regular web pages,
**so that** I can add non-RSS news sources.

**Acceptance Criteria:**
1. WebKit WebView configured for headless article extraction
2. SwiftSoup successfully parsing HTML from sample news sites
3. Intelligent content extraction identifying article title, body, and publish date
4. JavaScript-rendered content properly loaded before extraction
5. Fallback extraction strategies when primary selectors fail
6. Successfully extracts articles from at least 3 major news sites (CNN, BBC, TechCrunch)
7. Extracted articles saved to database with proper formatting

### Story 1.6: Fetch Orchestration with Basic UI Feedback

**As a** user,
**I want** all my configured sources to be fetched with visual progress indication,
**so that** I know the app is working.

**Acceptance Criteria:**
1. FetchCoordinator manages concurrent fetching from multiple sources
2. Refresh button in toolbar triggers fetch operation
3. Status bar updates to show "Fetching X of Y sources..."
4. Progress bar appears during fetch operation
5. Failed sources shown in status ("3 sources failed to fetch")
6. Simple list view shows fetched article titles (no cards yet)
7. All fetched articles appear in list within 10 seconds total
8. List updates live as articles are fetched and stored

**UI Checkpoint:** At the end of Epic 1, we have a working app that can fetch and display articles in a basic list format.

## Epic 2: Visual Intelligence System

**Goal:** Transform the basic list into the signature card-based visual intelligence system with color-coded keywords. This epic delivers the complete UI experience and core differentiator of the product.

### Story 2.1: Article Card Component UI

**As a** user,
**I want** to see articles displayed as beautiful cards instead of a list,
**so that** I can quickly scan through news content.

**Acceptance Criteria:**
1. Replace list view with card-based layout
2. Cards show title (16pt font) and summary (14pt font)
3. Source name and publish date displayed as metadata (12pt, gray)
4. Cards have consistent height (120pt) and responsive width
5. Smooth hover state with subtle shadow (0.95 scale, 5pt shadow)
6. Cards layout in responsive grid (1-3 columns based on window width)
7. Smooth scrolling at 60fps with up to 200 cards
8. Loading placeholder cards shown while content loads

### Story 2.2: Keyword Configuration UI

**As a** user,
**I want** a polished settings panel to configure keywords with colors,
**so that** I can easily customize my tracking.

**Acceptance Criteria:**
1. Settings window opens via Cmd+, or Preferences menu
2. Keywords tab shows current keywords in a list
3. "+" button opens popover with text field and color picker
4. macOS native color picker with preset swatches
5. Keywords displayed with their colors as preview
6. Delete button (trash icon) removes keywords
7. Changes apply immediately to main window
8. Maximum 5 keywords enforced with clear error message

### Story 2.3: Keyword Highlighting in Cards

**As a** user,
**I want** matching keywords to be highlighted with background colors in card text,
**so that** I can instantly spot topics of interest.

**Acceptance Criteria:**
1. Keywords in titles highlighted with configured background color
2. Keywords in summaries highlighted with same background color
3. Highlighting preserves text readability (appropriate contrast)
4. Multiple keywords in same text show with their respective colors
5. Partial word matches handled correctly (configurable per keyword)
6. Performance remains smooth even with 5 keywords across 200 cards
7. Highlighting updates immediately when keywords are changed

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

### Story 2.5: Complete Visual Polish

**As a** user,
**I want** a polished, native macOS interface,
**so that** the app feels professional and trustworthy.

**Acceptance Criteria:**
1. Keyword legend bar at top shows all active keywords with colors and counts
2. Toolbar refined with segmented control for view density (Comfortable/Compact)
3. Empty state illustration when no articles loaded
4. Loading states with skeleton cards during fetch
5. Error states with helpful messages and retry buttons
6. All animations smooth and purposeful (no jank)
7. Supports both light and dark mode following system preference
8. Window chrome uses macOS Big Sur+ style with proper materials

**UI Checkpoint:** At the end of Epic 2, the complete visual interface is implemented and polished.

## Epic 3: User Configuration & Persistence

**Goal:** Create comprehensive settings management and validate the complete MVP through manual testing. This epic makes the application production-ready for daily use.

### Story 3.1: Source Management Interface

**As a** user,
**I want** to add and configure news sources through a polished settings panel,
**so that** I can customize which sites I monitor.

**Acceptance Criteria:**
1. Settings window Sources tab shows table of configured sources
2. Add button opens sheet with URL field and type selector
3. Test button validates source with spinner and success/error feedback
4. Sources show status indicators (green = working, red = error, gray = disabled)
5. Enable/disable toggle for each source
6. Delete button with confirmation alert
7. Drag to reorder sources
8. Changes save automatically and persist

### Story 3.2: Source Auto-Detection

**As a** user,
**I want** the system to automatically detect feed URLs when I provide a website,
**so that** I don't need to find RSS feeds manually.

**Acceptance Criteria:**
1. When user enters main site URL, system checks for RSS autodiscovery
2. Common RSS URL patterns checked (/feed, /rss, /atom, etc.)
3. Link tags in HTML head parsed for feed references
4. If multiple feeds found, user prompted to select
5. If no feed found, falls back to web scraping mode
6. Detection process completes within 3 seconds
7. Clear feedback provided on detection results

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

### Story 3.4: First-Run Experience

**As a** user,
**I want** a helpful first-run experience,
**so that** I can quickly set up the app.

**Acceptance Criteria:**
1. On first launch, welcome window appears
2. Step 1: Add at least one news source with suggestions
3. Step 2: Configure at least one keyword with example
4. Step 3: Brief explanation of color coding system
5. Skip option available for experienced users
6. Sample data option to load demo sources and keywords
7. Welcome window doesn't show again after completion
8. Setup completable in under 2 minutes

### Story 3.5: Manual User Testing Phase

**As a** QA tester/user,
**I want** to thoroughly test the complete MVP functionality,
**so that** we can identify and fix issues before daily use.

**Acceptance Criteria:**
1. Test checklist document created covering all major features
2. Test with 15 real news sources from different sites
3. Test with maximum 5 keywords with different colors
4. Performance test with 200+ articles loaded
5. Test on both Intel (if available) and Apple Silicon Macs
6. Test in both light and dark mode
7. Memory usage stays under 200MB during normal use
8. 2-day testing period with personal daily use
9. Bug list compiled and prioritized (Critical, Major, Minor)
10. Critical bugs fixed before considering MVP complete

**Testing Checklist Items:**
- Add each type of source (RSS, web page)
- Verify fetch completes in under 10 seconds
- Confirm keywords highlight correctly
- Test clicking through to original articles
- Verify settings persist after quit/relaunch
- Check visual pattern recognition (< 10 seconds)
- Monitor memory usage over time
- Test error handling with unreachable sources
- Verify UI remains responsive during fetching
- Test window resizing and responsive layout

**MVP Complete:** At the end of Epic 3, we have a fully functional, tested, and polished news aggregator ready for daily use.

## Epic 4: AI Enhancement & Intelligence (Phase 2)

**Goal:** Integrate OpenAI for article summarization and implement the OpenAI Agents SDK for advanced research capabilities. This epic transforms the aggregator from a passive reader to an active intelligence assistant that can investigate topics deeply and provide AI-generated insights.

### Story 4.1: OpenAI API Integration Foundation

**As a** developer,
**I want** to integrate the OpenAI API with proper authentication and error handling,
**so that** I can use AI services throughout the application.

**Acceptance Criteria:**
1. OpenAI Swift client library integrated via SPM
2. API key securely stored in macOS Keychain
3. API key configuration UI in Advanced preferences tab
4. Test connection button validates API key
5. Rate limiting implemented (max 100 requests/minute)
6. Exponential backoff for API errors (2s, 4s, 8s, max 60s)
7. Cost tracking initialized with daily/monthly counters
8. Error messages user-friendly (not raw API errors)

### Story 4.2: AI-Powered Article Summarization

**As a** user,
**I want** AI-generated 2-line summaries instead of first sentences,
**so that** I get better quality summaries of articles.

**Acceptance Criteria:**
1. Summarization service uses gpt-4.1-mini model exclusively
2. Prompt optimized for 2-sentence summaries (max 40 words)
3. Summaries generated during fetch process (not on-demand)
4. Cache summaries in database to avoid regeneration
5. Fallback to first 2 sentences if API fails
6. Cost per article tracked and displayed (~$0.001/article)
7. Batch summarization for efficiency (up to 10 articles per request)
8. Summary quality noticeably better than first sentences

### Story 4.3: OpenAI Agents SDK Integration

**As a** developer,
**I want** to integrate the OpenAI Agents SDK for agent capabilities,
**so that** users can trigger deep research on articles.

**Acceptance Criteria:**
1. OpenAI Agents SDK integrated and initialized
2. Base agent created with access to article database
3. Agent tools registered: search_articles, get_article_content, find_related
4. Agent conversation state persisted between sessions
5. Agent responses stream to UI in real-time
6. Agent costs tracked separately from summarization
7. Agent timeout set to 30 seconds per interaction
8. Mock agent testing framework established

### Story 4.4: Research Agent Implementation

**As a** user,
**I want** to trigger deep research on any article with an AI agent,
**so that** I can understand topics more thoroughly.

**Acceptance Criteria:**
1. "Research" button added to article card hover state
2. Research panel slides in from right (400pt wide)
3. Agent investigates article topic using OpenAI Agents SDK
4. Agent searches for related articles in database
5. Agent provides structured research report with sections
6. Research results cached for 24 hours
7. User can ask follow-up questions to agent
8. Research typically completes in 10-15 seconds
9. Clear cost indicator shown (approximately $0.02-0.05 per research)

### Story 4.5: Smart Keyword Suggestions

**As a** user,
**I want** AI-suggested keywords based on my reading patterns,
**so that** I can discover relevant topics I might be missing.

**Acceptance Criteria:**
1. Agent analyzes last 100 read articles for patterns
2. Suggests 3-5 new keywords based on content themes
3. Suggestions appear in Keywords preferences with "AI suggested" badge
4. User can accept/reject each suggestion
5. Explanation provided for why each keyword was suggested
6. Suggestions refresh weekly or on-demand
7. Accepted suggestions track performance (how many matches)
8. Cost approximately $0.10 per suggestion batch

### Story 4.6: Daily Intelligence Brief

**As a** user,
**I want** an AI-generated morning intelligence brief,
**so that** I can quickly understand the key developments.

**Acceptance Criteria:**
1. "Generate Brief" button in toolbar
2. Agent analyzes all articles from last 24 hours
3. Creates structured brief with: Headlines, Trends, Deep Dives
4. Brief highlights connections between stories
5. Keywords prominently featured in brief
6. Brief generated in under 20 seconds
7. Export brief as Markdown or PDF
8. Cost approximately $0.15 per brief
9. Brief cached until next fetch cycle

## Epic 5: Advanced Visualization & Analytics (Phase 2)

**Goal:** Add multiple view modes, filtering capabilities, analytics tracking, and heat map visualization for deeper insights into news patterns and reading behavior.

### Story 5.1: Multiple View Modes

**As a** user,
**I want** different view modes for different consumption styles,
**so that** I can adapt the interface to my current needs.

**Acceptance Criteria:**
1. View mode selector in toolbar (segmented control)
2. Focus Mode: Full cards with summaries (current default)
3. Compact Mode: Single-line entries with title only
4. Reader Mode: Expanded cards with more content
5. Executive Mode: Only high-priority articles based on keyword frequency
6. Smooth animation between mode transitions
7. Selected mode persists between sessions
8. Keyboard shortcuts for mode switching (Cmd+1,2,3,4)

### Story 5.2: Advanced Filtering System

**As a** user,
**I want** powerful filtering options,
**so that** I can focus on specific content.

**Acceptance Criteria:**
1. Filter bar below toolbar with quick filters
2. Filter by keyword (checkbox list)
3. Filter by source (checkbox list)
4. Filter by date range (Today, This Week, All)
5. Filter by read/unread status
6. Combine multiple filters with AND logic
7. Filter count shown ("Showing 45 of 200 articles")
8. Clear all filters button
9. Filters persist during session

### Story 5.3: Reading Analytics Dashboard

**As a** user,
**I want** to see analytics about my reading patterns,
**so that** I can measure efficiency improvements.

**Acceptance Criteria:**
1. Analytics view accessible via View menu
2. Daily reading time chart (last 30 days)
3. Articles read vs. scanned metrics
4. Time saved compared to baseline (percentage)
5. Most frequent keywords chart
6. Most read sources ranking
7. Peak reading times heat map
8. Export analytics as CSV
9. Privacy notice that data is local-only

### Story 5.4: Keyword Heat Map Visualization

**As a** user,
**I want** a heat map showing keyword density by source,
**so that** I can see which sources cover which topics.

**Acceptance Criteria:**
1. Heat map view toggle in View menu
2. Sources on Y-axis, keywords on X-axis
3. Color intensity shows match frequency
4. Hover shows exact count
5. Click cell to filter articles
6. Exportable as image (PNG)
7. Updates in real-time as articles fetched
8. Handles up to 15 sources × 5 keywords clearly

### Story 5.5: Smart Sorting Options

**As a** user,
**I want** multiple sorting options for articles,
**so that** I can prioritize what to read first.

**Acceptance Criteria:**
1. Sort dropdown in toolbar
2. Sort by date (newest/oldest)
3. Sort by keyword relevance (most matches first)
4. Sort by source priority (user-defined)
5. Sort by AI-determined importance
6. Group by source option
7. Group by keyword option
8. Sort preference persists

### Story 5.6: Trend Detection

**As a** user,
**I want** to see emerging trends in my keywords,
**so that** I can spot important developments early.

**Acceptance Criteria:**
1. Trend indicator (↑↓→) next to keyword counts
2. Based on last 7 days of data
3. Significant changes highlighted (>50% increase)
4. Trend details on hover (was X, now Y)
5. Historical trend graph per keyword
6. Alert when keyword spikes unusually
7. Trends update after each fetch
8. Exportable trend report

## Epic 6: Ambient Awareness & Extensions (Phase 3)

**Goal:** Develop menu bar ticker, widgets, and notification system for peripheral awareness without opening the main application. This epic extends the aggregator beyond a traditional app into an always-available intelligence system.

### Story 6.1: Menu Bar Ticker

**As a** user,
**I want** a menu bar ticker showing keyword matches,
**so that** I can maintain awareness without opening the app.

**Acceptance Criteria:**
1. Menu bar icon with optional ticker text
2. Shows total keyword match count as badge
3. Click reveals dropdown with keyword breakdown
4. Quick access to refresh and open main window
5. Customizable update frequency (1, 5, 15 minutes)
6. Can disable ticker in preferences
7. Memory usage under 20MB
8. Updates within 500ms of new matches

### Story 6.2: Today Widget

**As a** user,
**I want** a macOS widget showing top articles,
**so that** I can glance at news from Notification Center.

**Acceptance Criteria:**
1. Widget available in Notification Center
2. Shows top 5 articles with keyword highlights
3. Configurable size (small, medium, large)
4. Click article to open in main app
5. Refresh button in widget
6. Shows last update time
7. Follows system appearance (light/dark)
8. Updates every 30 minutes automatically

### Story 6.3: Notification System

**As a** user,
**I want** notifications when important keywords spike,
**so that** I'm alerted to significant developments.

**Acceptance Criteria:**
1. Notification preferences per keyword
2. Threshold setting (X matches triggers notification)
3. Rate limiting (max 1 notification per keyword per hour)
4. Rich notifications with article preview
5. Click notification to open main app filtered
6. Notification Center grouping by keyword
7. Do Not Disturb respect
8. Sound/banner/alert style options

### Story 6.4: Spotlight Integration

**As a** user,
**I want** to search my news articles via Spotlight,
**so that** I can quickly find previously read content.

**Acceptance Criteria:**
1. Articles indexed in Core Spotlight
2. Search by title, content, or source
3. Keywords highlighted in Spotlight results
4. Custom result template with article preview
5. Click result opens article in app
6. Index updates after each fetch
7. Respects article age limit for index size
8. Can disable in preferences

### Story 6.5: Quick Look Extension

**As a** user,
**I want** to preview articles with Quick Look (spacebar),
**so that** I can read without opening browser.

**Acceptance Criteria:**
1. Spacebar triggers Quick Look on selected card
2. Shows full article content (cached)
3. Keywords remain highlighted
4. Navigation between articles with arrows
5. Close with spacebar or Escape
6. Supports standard Quick Look gestures
7. Falls back to web view if content unavailable
8. Performance smooth even with large articles

### Story 6.6: Share Extension

**As a** user,
**I want** to add articles from Safari to my aggregator,
**so that** I can save interesting articles I find while browsing.

**Acceptance Criteria:**
1. Share extension appears in Safari share menu
2. Captures article URL and content
3. Allows keyword tagging before saving
4. Saves to database with "Manual" source
5. Appears immediately in main app
6. Works with paywalled content if logged in
7. Confirmation notification after save
8. Handles errors gracefully
