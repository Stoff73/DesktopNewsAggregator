# Security and Performance

## Security Measures

### Data Protection
```swift
// Keychain storage for sensitive data
class KeychainManager {
    func store(apiKey: String, for service: String) throws {
        let query: [String: Any] = [
            kSecClass as String: kSecClassGenericPassword,
            kSecAttrService as String: service,
            kSecAttrAccount as String: "api-key",
            kSecValueData as String: apiKey.data(using: .utf8)!,
            kSecAttrAccessible as String: kSecAttrAccessibleWhenUnlocked
        ]
        
        let status = SecItemAdd(query as CFDictionary, nil)
        guard status == errSecSuccess else {
            throw KeychainError.unableToStore
        }
    }
}

// Encrypted database fields
extension SQLiteManager {
    func encryptField(_ value: String, key: Data) -> Data {
        // AES-256 encryption
        return CryptoKit.AES.GCM.seal(value.data(using: .utf8)!, using: key).combined!
    }
}
```

### Network Security
- HTTPS only for all external requests
- Certificate pinning for critical endpoints (Phase 3)
- Request signing for API calls (Phase 2)
- Rate limiting to prevent abuse

### Input Validation
```swift
struct InputValidator {
    static func validateURL(_ string: String) -> Bool {
        guard let url = URL(string: string),
              let scheme = url.scheme,
              ["http", "https"].contains(scheme.lowercased()) else {
            return false
        }
        return true
    }
    
    static func validateKeywordPattern(_ pattern: String) -> Bool {
        do {
            _ = try NSRegularExpression(pattern: pattern)
            return true
        } catch {
            return false
        }
    }
    
    static func sanitizeHTML(_ html: String) -> String {
        // Remove script tags and dangerous attributes
        let cleaned = html
            .replacingOccurrences(of: "<script[^>]*>.*?</script>", with: "", options: .regularExpression)
            .replacingOccurrences(of: "on\\w+\\s*=", with: "", options: .regularExpression)
        return cleaned
    }
}
```

## Performance Optimizations

### Memory Management
```swift
// Automatic memory pressure handling
class MemoryManager {
    init() {
        NotificationCenter.default.addObserver(
            self,
            selector: #selector(handleMemoryWarning),
            name: NSNotification.Name.NSProcessInfoPowerStateDidChange,
            object: nil
        )
    }
    
    @objc private func handleMemoryWarning() {
        // Clear caches
        ImageCache.shared.removeAll()
        ArticleCache.shared.trimToSize(maxSize: 10_000_000) // 10MB
        
        // Force garbage collection
        autoreleasepool { }
    }
}
```

### Rendering Performance
```swift
// Lazy loading and view recycling
struct ArticleFeedView: View {
    @State private var visibleRange: Range<Int> = 0..<20
    
    var body: some View {
        ScrollViewReader { proxy in
            LazyVStack(spacing: 8) {
                ForEach(visibleArticles) { article in
                    ArticleCardView(article: article)
                        .onAppear {
                            prefetchIfNeeded(article)
                        }
                        .onDisappear {
                            releaseIfNeeded(article)
                        }
                }
            }
        }
    }
    
    private func prefetchIfNeeded(_ article: Article) {
        // Prefetch next 5 articles
        let index = articles.firstIndex(of: article) ?? 0
        for i in index..<min(index + 5, articles.count) {
            ImageCache.shared.prefetch(articles[i].imageUrl)
        }
    }
}
```

### Database Performance
```swift
// Query optimization
extension ArticleRepository {
    func fetchOptimized(limit: Int = 100) async throws -> [Article] {
        let query = """
            SELECT a.*, 
                   GROUP_CONCAT(km.keyword_id) as keyword_ids
            FROM articles a
            LEFT JOIN keyword_matches km ON a.id = km.article_id
            WHERE a.fetched_date > ?
            GROUP BY a.id
            ORDER BY a.published_date DESC
            LIMIT ?
        """
        
        // Use prepared statement
        let statement = try db.prepare(query)
        return try statement.bind(Date().addingTimeInterval(-86400), limit).run()
    }
}
```

## Performance Metrics

| Metric | Target | Measurement Method |
|--------|--------|-------------------|
| App Launch | <2s | Time to first render |
| Article Fetch | <3s | Network + processing |
| Scroll FPS | 60fps | Instruments profiling |
| Memory Usage | <200MB | Activity Monitor |
| CPU Usage (idle) | <1% | Activity Monitor |
| Search Response | <100ms | Timer from input |
| Keyword Highlight | <50ms | Processing time |
| Database Query | <10ms | SQLite profiling |
