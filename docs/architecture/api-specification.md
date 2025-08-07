# API Specification

Since this is a native macOS application with no external API for MVP, this section defines the internal service protocols and the future local REST API for agent integration.

## Internal Service Protocols (MVP)

The application uses protocol-based service architecture for clean separation of concerns:

```swift
// MARK: - Article Fetching Services

protocol ArticleFetchService {
    func fetchArticles(from source: Source) async throws -> [Article]
    func fetchAll(sources: [Source]) async -> [FetchResult]
}

protocol RSSFeedService: ArticleFetchService {
    func parseFeed(at url: URL) async throws -> [Article]
}

protocol WebScrapingService: ArticleFetchService {
    func scrapeArticles(from url: URL, selectors: Source.WebSelectors) async throws -> [Article]
}

// MARK: - Data Persistence Services

protocol ArticleRepository {
    func save(_ article: Article) async throws
    func fetch(matching keywords: [Keyword]) async throws -> [Article]
    func fetchRecent(limit: Int) async throws -> [Article]
    func markAsRead(_ articleId: UUID) async throws
    func deleteOlderThan(_ date: Date) async throws -> Int
}

protocol SourceRepository {
    func save(_ source: Source) async throws
    func fetchAll() async throws -> [Source]
    func update(_ source: Source) async throws
    func delete(_ sourceId: UUID) async throws
}

protocol KeywordRepository {
    func save(_ keyword: Keyword) async throws
    func fetchAll() async throws -> [Keyword]
    func updateMatchCount(for keywordId: UUID, increment: Int) async throws
}

// MARK: - Keyword Processing Services

protocol KeywordMatchingService {
    func findMatches(in article: Article, keywords: [Keyword]) -> [KeywordMatch]
    func highlight(text: String, with keywords: [Keyword]) -> NSAttributedString
}

// MARK: - AI Services (Phase 2)

protocol SummarizationService {
    func summarize(_ article: Article) async throws -> String
    func summarizeBatch(_ articles: [Article]) async throws -> [String]
}

protocol AgentService {
    func research(article: Article) async throws -> ResearchReport
    func generateBrief(articles: [Article]) async throws -> IntelligenceBrief
    func suggestKeywords(from articles: [Article]) async throws -> [Keyword]
}
```

## Local REST API Specification (Phase 2)

For agent integration and future extensibility, a local REST API will be exposed:

```yaml
openapi: 3.0.0
info:
  title: Desktop News Aggregator Local API
  version: 1.0.0
  description: Local API for agent integration and external tool access
servers:
  - url: http://localhost:8765
    description: Local development server

paths:
  /api/articles:
    get:
      summary: Retrieve articles with optional filters
      parameters:
        - name: keywords
          in: query
          schema:
            type: array
            items:
              type: string
        - name: sources
          in: query
          schema:
            type: array
            items:
              type: string
        - name: since
          in: query
          schema:
            type: string
            format: date-time
        - name: limit
          in: query
          schema:
            type: integer
            default: 100
      responses:
        '200':
          description: List of articles
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: '#/components/schemas/Article'
    
  /api/articles/{id}:
    get:
      summary: Get specific article by ID
      parameters:
        - name: id
          in: path
          required: true
          schema:
            type: string
      responses:
        '200':
          description: Article details
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Article'
    
  /api/articles/{id}/research:
    post:
      summary: Trigger AI research on article
      parameters:
        - name: id
          in: path
          required: true
          schema:
            type: string
      requestBody:
        content:
          application/json:
            schema:
              type: object
              properties:
                depth:
                  type: string
                  enum: [quick, standard, deep]
                focus:
                  type: array
                  items:
                    type: string
      responses:
        '202':
          description: Research initiated
          content:
            application/json:
              schema:
                type: object
                properties:
                  taskId:
                    type: string
                  estimatedTime:
                    type: integer
  
  /api/keywords:
    get:
      summary: List all configured keywords
      responses:
        '200':
          description: Keyword list
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: '#/components/schemas/Keyword'
    
    post:
      summary: Add new keyword
      requestBody:
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/Keyword'
      responses:
        '201':
          description: Keyword created
  
  /api/sources:
    get:
      summary: List all configured sources
      responses:
        '200':
          description: Source list
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: '#/components/schemas/Source'
    
    post:
      summary: Add new source
      requestBody:
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/Source'
      responses:
        '201':
          description: Source created
  
  /api/search:
    post:
      summary: Full-text search across articles
      requestBody:
        content:
          application/json:
            schema:
              type: object
              properties:
                query:
                  type: string
                filters:
                  type: object
                limit:
                  type: integer
      responses:
        '200':
          description: Search results
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: '#/components/schemas/Article'
  
  /api/intelligence/brief:
    post:
      summary: Generate AI intelligence brief
      requestBody:
        content:
          application/json:
            schema:
              type: object
              properties:
                timeRange:
                  type: string
                  enum: [today, week, month]
                keywords:
                  type: array
                  items:
                    type: string
      responses:
        '200':
          description: Generated brief
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/IntelligenceBrief'

components:
  schemas:
    Article:
      type: object
      properties:
        id:
          type: string
        sourceId:
          type: string
        url:
          type: string
        title:
          type: string
        summary:
          type: string
        content:
          type: string
        publishedDate:
          type: string
          format: date-time
        keywordMatches:
          type: array
          items:
            $ref: '#/components/schemas/KeywordMatch'
    
    Keyword:
      type: object
      properties:
        id:
          type: string
        term:
          type: string
        color:
          type: object
          properties:
            red:
              type: number
            green:
              type: number
            blue:
              type: number
    
    Source:
      type: object
      properties:
        id:
          type: string
        name:
          type: string
        url:
          type: string
        type:
          type: string
          enum: [rss, website, api]
    
    KeywordMatch:
      type: object
      properties:
        keywordId:
          type: string
        locations:
          type: array
          items:
            type: object
            properties:
              start:
                type: integer
              end:
                type: integer
              field:
                type: string
    
    IntelligenceBrief:
      type: object
      properties:
        id:
          type: string
        generatedAt:
          type: string
          format: date-time
        headlines:
          type: array
          items:
            type: string
        trends:
          type: array
          items:
            type: object
        keyInsights:
          type: array
          items:
            type: string
```
