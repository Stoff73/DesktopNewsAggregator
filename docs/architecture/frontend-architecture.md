# Frontend Architecture

## SwiftUI View Architecture

```swift
// View Hierarchy
DesktopNewsAggregatorApp
├── MainWindow
│   ├── NavigationSplitView
│   │   ├── Sidebar
│   │   │   ├── SourceList
│   │   │   ├── KeywordList
│   │   │   └── ViewModeSelector
│   │   ├── ContentView
│   │   │   ├── ArticleFeed
│   │   │   │   └── ArticleCard[]
│   │   │   └── SearchBar
│   │   └── DetailView
│   │       └── ArticleReader
│   └── Toolbar
│       ├── RefreshButton
│       ├── ViewModeToggle
│       └── SettingsButton
├── SettingsWindow
│   ├── GeneralSettings
│   ├── SourcesSettings
│   ├── KeywordsSettings
│   └── AISettings (Phase 2)
└── MenuBarExtra (Phase 3)
    └── TickerView
```

## State Management Architecture

```swift
// MVVM with Combine
class AppState: ObservableObject {
    @Published var articles: [Article] = []
    @Published var keywords: [Keyword] = []
    @Published var sources: [Source] = []
    @Published var viewMode: ViewMode = .focus
    @Published var selectedArticle: Article?
    @Published var searchQuery: String = ""
    
    private var cancellables = Set<AnyCancellable>()
    
    init() {
        setupBindings()
        loadInitialData()
    }
}

class ArticleViewModel: ObservableObject {
    @Published var article: Article
    @Published var highlightedTitle: AttributedString
    @Published var highlightedSummary: AttributedString
    @Published var isLoadingAISummary: Bool = false
    
    private let keywordEngine: KeywordMatchingEngine
    private let aiService: AIIntegrationService?
    
    func loadAISummary() async { }
    func markAsRead() { }
    func share() { }
}
```

## Component Communication

```mermaid
graph LR
    AppState["@StateObject AppState"]
    ArticleFeed["ArticleFeedView"]
    ArticleCard["ArticleCardView"]
    KeywordConfig["KeywordConfigView"]
    
    AppState -->|@EnvironmentObject| ArticleFeed
    AppState -->|@EnvironmentObject| KeywordConfig
    ArticleFeed -->|@ObservedObject| ArticleCard
    
    ArticleCard -->|Binding| ArticleFeed
    KeywordConfig -->|Publisher| AppState
```

## View Modifiers and Styles

```swift
// Custom View Modifiers
struct KeywordHighlight: ViewModifier {
    let keywords: [Keyword]
    let text: String
    
    func body(content: Content) -> some View {
        content.overlay(
            HighlightedText(text: text, keywords: keywords)
        )
    }
}

// Design System
struct DesignSystem {
    static let cardCornerRadius: CGFloat = 12
    static let cardPadding: CGFloat = 16
    static let cardSpacing: CGFloat = 8
    static let animationDuration: TimeInterval = 0.25
    
    static let colors = Colors(
        primary: Color("AccentColor"),
        cardBackground: Color(NSColor.controlBackgroundColor),
        textPrimary: Color(NSColor.labelColor),
        textSecondary: Color(NSColor.secondaryLabelColor)
    )
}
```

## Performance Optimizations

**Rendering Performance:**
- LazyVStack for article feed
- Lazy loading of images
- View recycling with identifiable
- Debounced search with Combine
- Background keyword processing

**Memory Management:**
- Weak references in view models
- Image cache with size limits
- Automatic view model cleanup
- Pagination for large feeds

## Accessibility

```swift
struct ArticleCardView: View {
    var body: some View {
        VStack {
            // Content
        }
        .accessibilityElement(children: .combine)
        .accessibilityLabel(article.title)
        .accessibilityHint("Double tap to read article")
        .accessibilityAddTraits(.isButton)
        .accessibilityValue("\(article.keywordMatches.count) keyword matches")
    }
}
```
