# Tech Stack

This is the DEFINITIVE technology selection for the entire project. All development must use these exact versions and technologies.

## Technology Stack Table

| Category | Technology | Version | Purpose | Rationale |
|----------|------------|---------|---------|-----------|
| Frontend Language | Swift | 5.9+ | Native macOS UI development | Type-safe, performant, first-class Apple platform support |
| Frontend Framework | SwiftUI + AppKit | macOS 11+ | Modern declarative UI with fallbacks | SwiftUI for modern components, AppKit for advanced features unavailable in SwiftUI |
| UI Component Library | Native macOS | System | System-native components | Ensures consistent macOS look and feel, automatic dark mode support |
| State Management | Combine + @Published | iOS 13+ | Reactive data flow | Native Apple framework, perfect SwiftUI integration, no external dependencies |
| Backend Language | Swift | 5.9+ | Local data processing and services | Same language across stack, optimal for macOS integration |
| Backend Framework | Foundation + Concurrency | macOS 11+ | Async operations and networking | Modern Swift concurrency with async/await, backwards compatible with completion handlers |
| API Style | Local Services | N/A | Internal service protocols | No external API for MVP, protocols enable future remote APIs |
| Database | SQLite | 3.39+ | Local article storage | Embedded, zero-config, excellent FTS5 full-text search capabilities |
| Cache | NSCache + File System | System | Image and content caching | Native memory-aware caching with automatic purging under pressure |
| File Storage | ~/Library/Application Support | System | Article cache and user data | Standard macOS location, Time Machine compatible |
| Authentication | macOS Keychain | System | API key storage (Phase 2) | Secure credential storage with biometric protection |
| Frontend Testing | XCTest | Xcode 15+ | Unit and UI testing | Native Apple testing framework with Xcode integration |
| Backend Testing | XCTest | Xcode 15+ | Service and logic testing | Same framework for consistency, async testing support |
| E2E Testing | XCUITest | Xcode 15+ | End-to-end UI automation | Native UI testing with accessibility integration |
| Build Tool | Xcode Build System | 15.0+ | Compilation and packaging | Standard Apple toolchain, notarization support |
| Bundler | Swift Package Manager | 5.9+ | Dependency management | Native package management, no external tools needed |
| IaC Tool | N/A | N/A | Local app, no infrastructure | Desktop application requires no cloud infrastructure |
| CI/CD | Xcode Cloud | Optional | Automated builds (Phase 3) | Apple's native CI/CD, Mac App Store integration |
| Monitoring | os_log + Console | System | Logging and diagnostics | Native unified logging with streaming and filtering |
| Logging | OSLog | System | Structured logging | Performance-optimized, privacy-preserving logging |
| CSS Framework | N/A | N/A | Native UI styling | SwiftUI modifiers and AppKit appearance APIs |

## Additional Technology Choices

**Third-Party Dependencies (via SPM):**
- **FeedKit** (4.1+) - RSS/Atom feed parsing
- **SwiftSoup** (2.6+) - HTML parsing for web scraping
- **SQLite.swift** (0.14+) - Type-safe SQLite wrapper

**Phase 2 AI Integration:**
- **OpenAI Swift Client** (custom wrapper) - API communication
- **OpenAI Agents SDK** (Swift bindings) - Agent orchestration
- **Model:** gpt-4.1-mini exclusively for cost optimization

**Development Tools:**
- **Xcode** 15.0+ - Primary IDE
- **SwiftLint** 0.54+ - Code consistency
- **Instruments** - Performance profiling
- **Reality Composer Pro** (Phase 3) - visionOS widgets
