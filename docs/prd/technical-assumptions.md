# Technical Assumptions

## Repository Structure: Monorepo

The project will use a **Monorepo** structure to maintain all components in a single repository. This simplifies dependency management for a solo developer project and ensures version consistency across the macOS app and future extensions. The repository will follow the structure outlined in the brief:
```
DesktopNewsAggregator/
├── Core/           # Business logic, models, agents
├── UI/             # SwiftUI/AppKit views
├── Services/       # Fetching, parsing, AI
├── Storage/        # SQLite, caching
└── Utilities/      # Shared helpers
```

## Service Architecture

**MVP Architecture**: **Monolithic** macOS application with modular internal structure
- Single application bundle with clear separation of concerns
- Protocol-oriented design to enable future microservices extraction
- Service layer abstraction for fetch sources (RSS, web scraping, future APIs)
- Local SQLite database for all persistence

**Phase 2 Evolution**: OpenAI Agents SDK integration
- **OpenAI Agents SDK** as the foundation for ALL agent capabilities
- Agent orchestration layer built on top of OpenAI Agents SDK
- Local REST API exposing agent functionality to UI
- Background agent tasks using OpenAI Agents SDK tools

**Rationale**: Starting monolithic reduces complexity for MVP while the OpenAI Agents SDK provides the proven foundation for all future agent expansions.

## Testing Requirements

**MVP Testing Strategy**: **Manual testing with basic unit tests**
- Core business logic unit tests (keyword matching, data models)
- Manual testing checklist for UI flows
- Debug logging for troubleshooting

**Phase 2 Testing**: **Unit + Integration + Agent Testing**
- Comprehensive unit test coverage (>70%)
- Integration tests for source fetching
- **OpenAI Agents SDK mock testing for agent behaviors**
- **Agent conversation replay testing**
- UI snapshot tests for regression prevention
- Performance benchmarks for Intel vs Apple Silicon

**Phase 3 Testing**: **Full Testing Pyramid**
- End-to-end tests including agent workflows
- API contract tests for agent integration
- Accessibility automated testing
- Load testing for 500+ articles
- Agent cost simulation testing

## Additional Technical Assumptions and Requests

**Language & Framework Choices:**
- **Swift 5.5+** with async/await for modern concurrency (fallback to completion handlers for macOS 11)
- **SwiftUI** for modern UI components with @available guards
- **AppKit** for advanced features not available in SwiftUI
- **Combine** framework for reactive data flow

**Third-Party Dependencies (MVP):**
- **FeedKit** - RSS/Atom feed parsing (MIT license)
- **SwiftSoup** - HTML parsing for web scraping (MIT license)
- **SQLite.swift** - Type-safe SQLite wrapper (MIT license)

**AI & Agent Integration (Phase 2):**
- **OpenAI Agents SDK** - MANDATORY for all agent implementations
  - Research agents using OpenAI Agents SDK tools
  - Summary agents built with OpenAI Agents SDK
  - Custom tools registered with OpenAI Agents SDK
  - Agent state persistence using SDK capabilities
- **OpenAI Swift Client** - For direct API calls (summaries)
- **gpt-4.1-mini model** exclusively for cost optimization
- Token usage monitoring and cost tracking
- Agent conversation caching for efficiency

**OpenAI Agents SDK Architecture (Phase 2):**
```swift
// Agent definitions using OpenAI Agents SDK
- Base News Agent: Quick queries about current articles
- Research Agent: Deep investigation using SDK tools
- Summary Agent: Content processing with SDK
- Trend Agent: Pattern analysis across articles
```

**Development Tools & Practices:**
- **Xcode 15+** for development
- **SwiftLint** for code consistency
- **Git** with feature branches
- **GitHub** for repository hosting
- **Semantic versioning** for releases
- **OpenAI Agents SDK Playground** for agent prompt iteration

**Performance Optimizations:**
- Lazy loading for article cards
- Virtual scrolling for large lists
- Background queues for fetching
- Image thumbnail caching
- Adaptive quality based on hardware
- **Agent response streaming using OpenAI Agents SDK**
- **Agent result caching to minimize API costs**

**Data Architecture:**
- **SQLite with FTS5** for full-text search
- **JSON** for configuration files
- **Keychain** for sensitive data (API keys including OpenAI)
- **UserDefaults** for user preferences
- Article cache expiry after 7 days
- **Agent conversation history in SQLite**
- **Agent-compatible JSON structure for all articles**

**Deployment & Distribution (MVP):**
- Direct download DMG from GitHub releases
- Notarized for macOS Gatekeeper
- Auto-update mechanism using Sparkle (Phase 2)
- Mac App Store distribution (Phase 3)

**Security Considerations:**
- No telemetry or analytics in MVP
- All network requests over HTTPS
- Local-only data storage
- **OpenAI API key secure storage in Keychain**
- **Agent cost limits enforced**
- Optional iCloud sync (Phase 3)
- Sandboxed when possible

**Hardware Requirements:**
- Intel Macs 2015+ with 4GB RAM minimum
- Apple Silicon with 8GB RAM recommended
- 500MB disk space for app and cache
- Internet connection for fetching and agent operations

**Critical Technical Decisions:**
1. **No React/Electron** - Native performance is essential for the visual intelligence system
2. **SQLite over Core Data** - Better full-text search and future agent API exposure
3. **WebKit over URLSession for scraping** - Handles JavaScript-heavy sites
4. **Monorepo over multiple repos** - Simplifies solo development
5. **SwiftUI-first with AppKit fallback** - Modern but pragmatic
6. **OpenAI Agents SDK for ALL agents** - Proven, maintained, powerful agent framework
7. **gpt-4.1-mini exclusively** - Cost optimization while maintaining quality
