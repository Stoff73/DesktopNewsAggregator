# Data Models

Define the core data models/entities that will be shared between frontend and backend components of the macOS application.

## Article

**Purpose:** Core entity representing a news article fetched from any source with associated metadata and keyword matches

**Key Attributes:**
- `id`: UUID - Unique identifier for the article
- `sourceId`: String - Reference to the source this article came from
- `url`: String - Original article URL for click-through
- `title`: String - Article headline for display
- `summary`: String? - AI-generated or extracted summary (2 lines)
- `content`: String? - Full article content for search and analysis
- `publishedDate`: Date - When the article was originally published
- `fetchedDate`: Date - When we fetched this article
- `imageUrl`: String? - Thumbnail image for visual enhancement
- `keywordMatches`: [KeywordMatch] - Detected keyword occurrences
- `readStatus`: ReadStatus - Unread, scanned, or read
- `aiSummary`: String? - Cached OpenAI-generated summary
- `aiSummaryDate`: Date? - When the AI summary was generated

### TypeScript Interface
```swift
struct Article: Identifiable, Codable {
    let id: UUID
    let sourceId: String
    let url: String
    let title: String
    var summary: String?
    let content: String?
    let publishedDate: Date
    let fetchedDate: Date
    let imageUrl: String?
    var keywordMatches: [KeywordMatch]
    var readStatus: ReadStatus
    var aiSummary: String?
    var aiSummaryDate: Date?
    
    enum ReadStatus: String, Codable {
        case unread, scanned, read
    }
}
```

### Relationships
- Many-to-One with Source (each article belongs to one source)
- Many-to-Many with Keywords (through KeywordMatch)
- One-to-Many with ResearchReports (Phase 2)

## Source

**Purpose:** Represents a news source configuration including fetch method and metadata

**Key Attributes:**
- `id`: UUID - Unique identifier
- `name`: String - Display name (e.g., "TechCrunch")
- `url`: String - Base URL or feed URL
- `type`: SourceType - RSS, website, or API
- `enabled`: Bool - Whether to include in fetches
- `lastFetchDate`: Date? - Last successful fetch
- `lastFetchError`: String? - Last error message if failed
- `fetchInterval`: TimeInterval - Custom fetch frequency
- `priority`: Int - Ordering and fetch priority
- `selectors`: WebSelectors? - CSS/XPath for web scraping

### TypeScript Interface
```swift
struct Source: Identifiable, Codable {
    let id: UUID
    var name: String
    var url: String
    var type: SourceType
    var enabled: Bool
    var lastFetchDate: Date?
    var lastFetchError: String?
    var fetchInterval: TimeInterval
    var priority: Int
    var selectors: WebSelectors?
    
    enum SourceType: String, Codable {
        case rss, website, api
    }
    
    struct WebSelectors: Codable {
        let titleSelector: String
        let contentSelector: String
        let dateSelector: String?
        let imageSelector: String?
    }
}
```

### Relationships
- One-to-Many with Articles (source has many articles)
- Many-to-Many with KeywordProfiles (Phase 2)

## Keyword

**Purpose:** User-configured keyword with associated color for visual pattern recognition

**Key Attributes:**
- `id`: UUID - Unique identifier
- `term`: String - The keyword or phrase to match
- `pattern`: String - Regex pattern for matching
- `color`: Color - RGB color for highlighting
- `priority`: Int - Resolution order for overlapping matches
- `caseSensitive`: Bool - Whether matching is case-sensitive
- `wholeWord`: Bool - Match whole words only
- `profileId`: UUID? - Associated profile (Phase 2)
- `createdDate`: Date - When keyword was added
- `matchCount`: Int - Total historical matches

### TypeScript Interface
```swift
struct Keyword: Identifiable, Codable {
    let id: UUID
    var term: String
    var pattern: String
    var color: CodableColor
    var priority: Int
    var caseSensitive: Bool
    var wholeWord: Bool
    var profileId: UUID?
    let createdDate: Date
    var matchCount: Int
    
    struct CodableColor: Codable {
        let red: Double
        let green: Double
        let blue: Double
        let alpha: Double
    }
}
```

### Relationships
- Many-to-Many with Articles (through KeywordMatch)
- Many-to-One with KeywordProfile (Phase 2)

## KeywordMatch

**Purpose:** Junction entity tracking specific keyword occurrences within articles

**Key Attributes:**
- `id`: UUID - Unique identifier
- `articleId`: UUID - Reference to article
- `keywordId`: UUID - Reference to keyword
- `locations`: [TextRange] - Where matches occur in text
- `matchCount`: Int - Number of occurrences
- `context`: String - Snippet around match for preview

### TypeScript Interface
```swift
struct KeywordMatch: Identifiable, Codable {
    let id: UUID
    let articleId: UUID
    let keywordId: UUID
    var locations: [TextRange]
    var matchCount: Int
    var context: String
    
    struct TextRange: Codable {
        let start: Int
        let end: Int
        let field: Field
        
        enum Field: String, Codable {
            case title, summary, content
        }
    }
}
```

### Relationships
- Many-to-One with Article
- Many-to-One with Keyword

## UserSettings

**Purpose:** Application preferences and configuration persisted across sessions

**Key Attributes:**
- `id`: UUID - Unique identifier
- `fetchSchedule`: [FetchTime] - Configured fetch times
- `viewMode`: ViewMode - Current display mode
- `articleRetention`: TimeInterval - How long to keep articles
- `maxArticlesPerSource`: Int - Limit per source
- `enableAISummaries`: Bool - Phase 2 feature flag
- `openAIKey`: String? - Encrypted API key (Phase 2)
- `colorScheme`: ColorScheme - Light, dark, or auto

### TypeScript Interface
```swift
struct UserSettings: Codable {
    let id: UUID
    var fetchSchedule: [FetchTime]
    var viewMode: ViewMode
    var articleRetention: TimeInterval
    var maxArticlesPerSource: Int
    var enableAISummaries: Bool
    var openAIKey: String?
    var colorScheme: ColorScheme
    
    struct FetchTime: Codable {
        let hour: Int
        let minute: Int
        let enabled: Bool
    }
    
    enum ViewMode: String, Codable {
        case focus, compact, reader, executive
    }
    
    enum ColorScheme: String, Codable {
        case light, dark, auto
    }
}
```

### Relationships
- One-to-One with User (implicit single user)
- One-to-Many with KeywordProfiles (Phase 2)
