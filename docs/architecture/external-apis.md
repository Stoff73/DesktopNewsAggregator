# External APIs

Since this is a native macOS application for MVP, external API integrations are minimal initially, expanding significantly in Phase 2.

## MVP Phase - News Sources Only

### RSS/Atom Feeds
**Purpose:** Primary content source for most news sites
**Endpoints:** Site-specific RSS URLs (e.g., https://techcrunch.com/feed/)
**Authentication:** None required for public feeds
**Rate Limits:** Respect site-specific crawl delays (typically 5-15 minutes)
**Data Format:** XML (RSS 2.0, Atom 1.0)
**Error Handling:** Fallback to cached content, exponential backoff on failures

### Website HTML (Scraping)
**Purpose:** Fallback for sites without RSS feeds
**Method:** Direct HTML fetching with WebKit
**Authentication:** None (public content only)
**Rate Limits:** Minimum 30-second delay between requests per domain
**Data Format:** HTML with site-specific selectors
**Compliance:** Respect robots.txt, identify with proper User-Agent

## Phase 2 - AI Integration

### OpenAI API
**Purpose:** Article summarization and intelligence generation
**Base URL:** https://api.openai.com/v1
**Authentication:** Bearer token via API key
**Model:** gpt-4.1-mini (cost-optimized)
**Rate Limits:** 10,000 TPM, 50 RPM for tier 1
**Endpoints Used:**
- `/chat/completions` - Summarization and analysis
- `/embeddings` - Semantic search (future)

**Request Example:**
```swift
struct OpenAIRequest: Codable {
    let model: String = "gpt-4.1-mini"
    let messages: [Message]
    let temperature: Double = 0.3
    let max_tokens: Int = 150
    
    struct Message: Codable {
        let role: String
        let content: String
    }
}
```

**Cost Management:**
- Cache all responses indefinitely
- Batch similar articles for single prompts
- Use streaming for long-form content
- Implement token counting before requests

### OpenAI Agents API
**Purpose:** Autonomous research and deep analysis
**Authentication:** Same as OpenAI API
**Capabilities:**
- Web browsing for fact-checking
- Code interpretation for data analysis
- File handling for report generation

**Agent Configuration:**
```swift
struct AgentConfig {
    let name: String = "NewsResearcher"
    let instructions: String = "Research and analyze news articles for deeper insights"
    let tools: [String] = ["web_browser", "code_interpreter"]
    let model: String = "gpt-4.1-mini"
}
```

## Phase 3 - Platform Services

### CloudKit
**Purpose:** Cross-device synchronization
**Authentication:** iCloud account
**Containers:** Private database only
**Record Types:** Article, Keyword, Source, Settings
**Sync Strategy:** Differential sync with conflict resolution

### Mac App Store Connect API
**Purpose:** Distribution and analytics
**Authentication:** App Store Connect API key
**Usage:** Automated builds, crash reports, usage analytics
