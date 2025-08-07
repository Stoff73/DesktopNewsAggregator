# Components

Based on the architectural patterns, tech stack, and data models, here are the major logical components across the application.

## UI Components

### MainWindowView
**Purpose:** Primary application window containing article feed and navigation
**Responsibilities:**
- Display article cards in scrollable feed
- Handle view mode transitions (Focus, Compact, Reader, Executive)
- Coordinate keyword highlighting across all visible text
- Manage real-time updates via Combine subscriptions
**Key Interactions:** ArticleCardView, NavigationSidebar, KeywordToolbar, SearchBar

### ArticleCardView
**Purpose:** Individual article display component with visual intelligence features
**Responsibilities:**
- Render article preview with title, summary, and metadata
- Apply keyword color highlighting in real-time
- Handle tap/click interactions for article expansion
- Display match count badges and source indicators
**Key Interactions:** KeywordHighlighter, ArticleDetailView, ShareMenu

### KeywordConfigurationView
**Purpose:** Interface for managing keywords and color assignments
**Responsibilities:**
- Add/edit/delete keywords with regex pattern support
- Color picker integration for visual mapping
- Live preview of keyword highlighting
- Import/export keyword sets
**Key Interactions:** KeywordRepository, ColorPicker, RegexValidator

### SourceManagementView
**Purpose:** Configure and monitor news sources
**Responsibilities:**
- Add RSS feeds and website sources
- Configure fetch schedules per source
- Display source health and last fetch status
- Test source connectivity and parsing
**Key Interactions:** SourceRepository, FetchCoordinator, SourceValidator

### ExecutiveDashboardView
**Purpose:** High-level intelligence view for Phase 2
**Responsibilities:**
- Display AI-generated intelligence briefs
- Show trending keywords and patterns
- Visualize article velocity and sentiment
- Present research reports and insights
**Key Interactions:** AgentService, ChartView, IntelligenceBriefView

## Service Components

### FetchCoordinator
**Purpose:** Orchestrate article fetching from multiple sources
**Responsibilities:**
- Schedule and execute fetch operations
- Manage concurrent source fetches
- Handle rate limiting and retries
- Update source status and error tracking
**Key Interactions:** RSSFeedService, WebScrapingService, ArticleRepository

### RSSFeedService
**Purpose:** Parse and process RSS/Atom feeds
**Responsibilities:**
- Fetch and parse RSS/Atom XML
- Extract article metadata and content
- Handle feed encoding and format variations
- Validate feed structure and content
**Key Interactions:** FeedKit, NetworkService, ArticleFactory

### WebScrapingService
**Purpose:** Extract articles from websites without feeds
**Responsibilities:**
- Load web pages using WebKit
- Apply CSS/XPath selectors for content extraction
- Handle JavaScript-rendered content
- Clean and normalize extracted HTML
**Key Interactions:** WebKit, SwiftSoup, ContentCleaner

### KeywordMatchingEngine
**Purpose:** High-performance keyword detection and highlighting
**Responsibilities:**
- Compile and cache regex patterns
- Find all keyword matches in article text
- Resolve overlapping matches by priority
- Generate highlighted text ranges
**Key Interactions:** KeywordRepository, TextProcessor, HighlightCache

### AIIntegrationService (Phase 2)
**Purpose:** Interface with OpenAI for summarization and analysis
**Responsibilities:**
- Manage API authentication and rate limiting
- Send articles for summarization
- Cache AI responses for cost optimization
- Handle API errors and fallbacks
**Key Interactions:** OpenAI API, TokenCounter, ResponseCache

### AgentOrchestrator (Phase 2)
**Purpose:** Coordinate autonomous research agents
**Responsibilities:**
- Spawn and manage research agents
- Coordinate multi-step research workflows
- Aggregate agent findings into reports
- Manage agent conversation context
**Key Interactions:** OpenAI Agents SDK, ResearchRepository, ToolRegistry

## Data Access Components

### SQLiteManager
**Purpose:** Core database connection and query management
**Responsibilities:**
- Manage database connections and transactions
- Execute migrations and schema updates
- Handle connection pooling
- Provide query builder interface
**Key Interactions:** SQLite.swift, MigrationManager, QueryBuilder

### ArticleRepository
**Purpose:** Article persistence and retrieval
**Responsibilities:**
- CRUD operations for articles
- Full-text search via FTS5
- Batch operations for performance
- Automatic cleanup of old articles
**Key Interactions:** SQLiteManager, ArticleCache, SearchIndex

### KeywordRepository
**Purpose:** Keyword configuration persistence
**Responsibilities:**
- Store keyword patterns and colors
- Track match statistics
- Manage keyword profiles (Phase 2)
- Export/import keyword sets
**Key Interactions:** SQLiteManager, KeywordValidator, ProfileManager

### SourceRepository
**Purpose:** News source configuration storage
**Responsibilities:**
- Persist source configurations
- Track fetch history and errors
- Store scraping selectors
- Manage source priorities
**Key Interactions:** SQLiteManager, SourceValidator, FetchScheduler

### CacheManager
**Purpose:** Multi-level caching for performance
**Responsibilities:**
- Memory cache for hot data
- Disk cache for images and content
- Cache invalidation strategies
- Size and age-based eviction
**Key Interactions:** NSCache, FileManager, CachePolicy

## Supporting Components

### NetworkManager
**Purpose:** Centralized network operations
**Responsibilities:**
- HTTP request execution
- Connection monitoring
- Request queuing and retry
- Certificate pinning (Phase 3)
**Key Interactions:** URLSession, Reachability, RequestQueue

### MigrationManager
**Purpose:** Database schema evolution
**Responsibilities:**
- Track schema versions
- Execute migration scripts
- Handle rollback on failure
- Validate migration integrity
**Key Interactions:** SQLiteManager, MigrationScript, VersionTracker

### NotificationService
**Purpose:** User notifications and alerts
**Responsibilities:**
- New article notifications
- Keyword match alerts
- Fetch error notifications
- System tray updates
**Key Interactions:** UserNotifications, NotificationCenter, TrayManager

### AnalyticsEngine
**Purpose:** Usage tracking and insights
**Responsibilities:**
- Track feature usage
- Monitor performance metrics
- Generate usage reports
- Privacy-preserving analytics
**Key Interactions:** OSLog, MetricsCollector, ReportGenerator
