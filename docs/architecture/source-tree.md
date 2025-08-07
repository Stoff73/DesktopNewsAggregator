# Source Tree

```
DesktopNewsAggregator/
├── DesktopNewsAggregator.xcodeproj
├── DesktopNewsAggregator/
│   ├── App/
│   │   ├── DesktopNewsAggregatorApp.swift
│   │   ├── AppDelegate.swift
│   │   ├── Info.plist
│   │   └── Entitlements.entitlements
│   ├── Core/
│   │   ├── Models/
│   │   │   ├── Article.swift
│   │   │   ├── Source.swift
│   │   │   ├── Keyword.swift
│   │   │   ├── KeywordMatch.swift
│   │   │   └── UserSettings.swift
│   │   ├── Database/
│   │   │   ├── SQLiteManager.swift
│   │   │   ├── Migrations/
│   │   │   │   ├── Migration_001_Initial.swift
│   │   │   │   └── MigrationManager.swift
│   │   │   └── Repositories/
│   │   │       ├── ArticleRepository.swift
│   │   │       ├── SourceRepository.swift
│   │   │       └── KeywordRepository.swift
│   │   └── Extensions/
│   │       ├── Date+Extensions.swift
│   │       ├── Color+Codable.swift
│   │       └── String+Regex.swift
│   ├── UI/
│   │   ├── Views/
│   │   │   ├── MainWindow/
│   │   │   │   ├── MainWindowView.swift
│   │   │   │   ├── ArticleFeedView.swift
│   │   │   │   ├── ArticleCardView.swift
│   │   │   │   ├── ArticleReaderView.swift
│   │   │   │   └── SidebarView.swift
│   │   │   ├── Settings/
│   │   │   │   ├── SettingsWindow.swift
│   │   │   │   ├── GeneralSettingsView.swift
│   │   │   │   ├── SourcesSettingsView.swift
│   │   │   │   ├── KeywordsSettingsView.swift
│   │   │   │   └── AISettingsView.swift
│   │   │   └── Components/
│   │   │       ├── KeywordHighlighter.swift
│   │   │       ├── SearchBar.swift
│   │   │       ├── LoadingView.swift
│   │   │       └── EmptyStateView.swift
│   │   ├── ViewModels/
│   │   │   ├── AppState.swift
│   │   │   ├── ArticleViewModel.swift
│   │   │   ├── FeedViewModel.swift
│   │   │   └── SettingsViewModel.swift
│   │   └── Styles/
│   │       ├── DesignSystem.swift
│   │       ├── ViewModifiers.swift
│   │       └── ButtonStyles.swift
│   ├── Services/
│   │   ├── Fetching/
│   │   │   ├── FetchCoordinator.swift
│   │   │   ├── RSSFeedService.swift
│   │   │   ├── WebScrapingService.swift
│   │   │   └── ArticleFactory.swift
│   │   ├── Processing/
│   │   │   ├── KeywordMatchingEngine.swift
│   │   │   ├── TextProcessor.swift
│   │   │   └── ContentCleaner.swift
│   │   ├── AI/ (Phase 2)
│   │   │   ├── AIIntegrationService.swift
│   │   │   ├── AgentOrchestrator.swift
│   │   │   ├── PromptTemplates.swift
│   │   │   └── TokenCounter.swift
│   │   └── Network/
│   │       ├── NetworkManager.swift
│   │       ├── RequestBuilder.swift
│   │       └── RateLimiter.swift
│   ├── Storage/
│   │   ├── CacheManager.swift
│   │   ├── DiskCache.swift
│   │   ├── ImageCache.swift
│   │   └── KeychainManager.swift
│   ├── Utilities/
│   │   ├── Logger.swift
│   │   ├── ErrorHandler.swift
│   │   ├── BackgroundTaskScheduler.swift
│   │   └── NotificationService.swift
│   └── Resources/
│       ├── Assets.xcassets/
│       │   ├── AppIcon.appiconset/
│       │   ├── Colors.colorset/
│       │   └── Images.imageset/
│       └── Localizable.strings
├── DesktopNewsAggregatorTests/
│   ├── Unit/
│   │   ├── KeywordMatchingTests.swift
│   │   ├── ArticleRepositoryTests.swift
│   │   ├── FeedParsingTests.swift
│   │   └── AIServiceTests.swift
│   ├── Integration/
│   │   ├── FetchWorkflowTests.swift
│   │   ├── DatabaseTests.swift
│   │   └── CacheTests.swift
│   └── UI/
│       ├── ArticleFeedUITests.swift
│       ├── KeywordHighlightUITests.swift
│       └── SettingsUITests.swift
├── Package.swift
├── README.md
├── .gitignore
├── .swiftlint.yml
└── docs/
    ├── architecture.md
    ├── prd.md
    └── api-docs/
```