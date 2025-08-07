# Testing Strategy

## Test Coverage Requirements

| Component | Coverage Target | Priority |
|-----------|----------------|----------|
| Business Logic | 90% | Critical |
| UI Components | 80% | High |
| Services | 85% | High |
| Utilities | 95% | Medium |
| Integration | 75% | High |

## Unit Testing

```swift
// Example: Keyword Matching Tests
import XCTest
@testable import DesktopNewsAggregator

class KeywordMatchingTests: XCTestCase {
    var engine: KeywordMatchingEngine!
    
    override func setUp() {
        super.setUp()
        engine = KeywordMatchingEngine()
    }
    
    func testExactMatch() {
        let keyword = Keyword(
            term: "Swift",
            pattern: "Swift",
            color: .red,
            caseSensitive: false
        )
        
        let text = "Swift is a powerful programming language"
        let matches = engine.findMatches(in: text, keywords: [keyword])
        
        XCTAssertEqual(matches.count, 1)
        XCTAssertEqual(matches[0].locations[0].start, 0)
        XCTAssertEqual(matches[0].locations[0].end, 5)
    }
    
    func testRegexPattern() {
        let keyword = Keyword(
            term: "AI",
            pattern: "\\b(AI|artificial intelligence)\\b",
            color: .blue,
            caseSensitive: false
        )
        
        let text = "AI and artificial intelligence are transforming news"
        let matches = engine.findMatches(in: text, keywords: [keyword])
        
        XCTAssertEqual(matches.count, 1)
        XCTAssertEqual(matches[0].matchCount, 2)
    }
    
    func testOverlappingMatches() {
        let keywords = [
            Keyword(term: "Apple", pattern: "Apple", color: .green, priority: 1),
            Keyword(term: "Apple Silicon", pattern: "Apple Silicon", color: .orange, priority: 2)
        ]
        
        let text = "Apple Silicon powers new Macs"
        let matches = engine.findMatches(in: text, keywords: keywords)
        
        // Higher priority wins
        XCTAssertEqual(matches.count, 1)
        XCTAssertEqual(matches[0].keyword.term, "Apple Silicon")
    }
    
    func testPerformance() {
        let keywords = (0..<100).map { i in
            Keyword(term: "keyword\(i)", pattern: "keyword\(i)", color: .red)
        }
        
        let text = String(repeating: "Lorem ipsum dolor sit amet ", count: 1000)
        
        measure {
            _ = engine.findMatches(in: text, keywords: keywords)
        }
    }
}
```

## Integration Testing

```swift
// Example: Fetch Workflow Test
class FetchWorkflowTests: XCTestCase {
    var coordinator: FetchCoordinator!
    var mockNetwork: MockNetworkManager!
    var testDB: SQLiteManager!
    
    override func setUp() async throws {
        try await super.setUp()
        
        // Setup test database
        testDB = try SQLiteManager(inMemory: true)
        mockNetwork = MockNetworkManager()
        
        coordinator = FetchCoordinator(
            network: mockNetwork,
            database: testDB
        )
    }
    
    func testCompleteFetchCycle() async throws {
        // Setup mock RSS response
        mockNetwork.mockResponse = """
        <?xml version="1.0"?>
        <rss version="2.0">
            <channel>
                <item>
                    <title>Test Article</title>
                    <link>https://example.com/article</link>
                    <description>Test description</description>
                </item>
            </channel>
        </rss>
        """
        
        let source = Source(
            name: "Test Source",
            url: "https://example.com/feed",
            type: .rss
        )
        
        // Execute fetch
        let result = await coordinator.fetch(from: source)
        
        // Verify
        XCTAssertTrue(result.isSuccess)
        
        let articles = try await testDB.fetch(Article.self)
        XCTAssertEqual(articles.count, 1)
        XCTAssertEqual(articles[0].title, "Test Article")
    }
}
```

## UI Testing

```swift
// Example: Article Feed UI Test
import XCTest

class ArticleFeedUITests: XCTestCase {
    var app: XCUIApplication!
    
    override func setUp() {
        continueAfterFailure = false
        app = XCUIApplication()
        app.launchArguments = ["--uitesting"]
        app.launch()
    }
    
    func testArticleCardInteraction() {
        // Wait for feed to load
        let firstCard = app.otherElements["article-card-0"]
        XCTAssertTrue(firstCard.waitForExistence(timeout: 5))
        
        // Test tap to expand
        firstCard.tap()
        
        let readerView = app.otherElements["article-reader"]
        XCTAssertTrue(readerView.exists)
        
        // Test keyword highlighting visible
        let highlightedText = app.staticTexts.matching(
            NSPredicate(format: "value CONTAINS 'keyword'")
        )
        XCTAssertGreaterThan(highlightedText.count, 0)
    }
    
    func testKeywordConfiguration() {
        // Open settings
        app.buttons["settings-button"].tap()
        
        // Navigate to keywords
        app.buttons["keywords-tab"].tap()
        
        // Add new keyword
        app.buttons["add-keyword"].tap()
        app.textFields["keyword-term"].typeText("TestKeyword")
        app.colorWells["keyword-color"].tap()
        app.buttons["save-keyword"].tap()
        
        // Verify keyword appears
        XCTAssertTrue(app.staticTexts["TestKeyword"].exists)
    }
}
```

## Test Automation

```yaml