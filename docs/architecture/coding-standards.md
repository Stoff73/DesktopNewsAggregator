# Coding Standards

## Swift Style Guide

### Naming Conventions
```swift
// MARK: - Types and Protocols
// Use PascalCase for types and protocols
struct ArticleCard { }
protocol ArticleFetchable { }
enum ReadStatus { }

// MARK: - Variables and Functions
// Use camelCase for variables and functions
let articleCount = 10
var isLoading = false
func fetchArticles() { }

// MARK: - Constants
// Use camelCase for instance constants, PascalCase for type constants
let maximumRetries = 3
static let DefaultTimeout: TimeInterval = 30

// MARK: - Acronyms
// Treat acronyms as words
let urlString = "https://example.com"  // Not URLString
let jsonData = Data()                   // Not JSONData
let aiService = AIService()             // Exception: Well-known service names
```

### Code Organization
```swift
// MARK: - File Structure
// 1. Imports
import SwiftUI
import Combine

// 2. Protocols
protocol ArticleDisplayable { }

// 3. Main Type
struct ArticleCardView: View {
    // MARK: - Properties
    // Public properties first
    let article: Article
    
    // Private properties
    @State private var isExpanded = false
    @StateObject private var viewModel = ArticleViewModel()
    
    // MARK: - Body
    var body: some View {
        // View implementation
    }
    
    // MARK: - Private Methods
    private func toggleExpansion() {
        withAnimation(.spring()) {
            isExpanded.toggle()
        }
    }
}

// 4. Extensions
extension ArticleCardView {
    // Grouped functionality
}
```

### Best Practices
```swift
// MARK: - Guard Statements
// Prefer early returns with guard
func processArticle(_ article: Article?) {
    guard let article = article else {
        logger.warning("Article is nil")
        return
    }
    // Process article
}

// MARK: - Optionals
// Use nil-coalescing for defaults
let title = article.title ?? "Untitled"

// Use optional chaining
let wordCount = article.content?.split(separator: " ").count

// MARK: - Closures
// Use trailing closure syntax
articles.filter { $0.isUnread }
    .sorted { $0.publishedDate > $1.publishedDate }
    .prefix(10)

// MARK: - Async/Await
// Prefer async/await over completion handlers
func fetchArticles() async throws -> [Article] {
    let data = try await networkManager.fetch(url)
    return try decoder.decode([Article].self, from: data)
}

// MARK: - Error Handling
// Use typed errors
enum ArticleError: LocalizedError {
    case notFound
    case invalidFormat
    case networkFailure(Error)
    
    var errorDescription: String? {
        switch self {
        case .notFound:
            return "Article not found"
        case .invalidFormat:
            return "Invalid article format"
        case .networkFailure(let error):
            return "Network error: \(error.localizedDescription)"
        }
    }
}
```

## Documentation Standards

```swift
/// Fetches articles from the specified source and processes them.
/// 
/// This method performs the following steps:
/// 1. Validates the source configuration
/// 2. Fetches raw data from the source
/// 3. Parses and validates articles
/// 4. Applies keyword matching
/// 5. Stores in database
///
/// - Parameters:
///   - source: The news source to fetch from
///   - force: If true, ignores rate limiting
/// - Returns: Array of processed articles
/// - Throws: `FetchError` if the fetch fails
///
/// - Note: This method respects rate limiting unless forced
/// - Warning: Forcing may result in temporary IP ban
func fetchArticles(from source: Source, force: Bool = false) async throws -> [Article] {
    // Implementation
}
```

## SwiftLint Configuration

```yaml