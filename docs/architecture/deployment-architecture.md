# Deployment Architecture

## Distribution Strategy

```mermaid
graph LR
    Dev[Development Build]
    Dev --> TestFlight[TestFlight Beta]
    TestFlight --> DirectDL[Direct Download]
    TestFlight --> AppStore[Mac App Store]
    
    DirectDL --> Notarization[Apple Notarization]
    Notarization --> DMG[DMG Package]
    DMG --> Website[Website Download]
    
    AppStore --> Review[App Review]
    Review --> Publication[Public Release]
```

## Build Configuration

```swift
// Build Configurations
enum BuildConfiguration {
    case debug
    case release
    case appStore
    
    var bundleIdentifier: String {
        switch self {
        case .debug: return "com.yourcompany.desktop-news-aggregator.debug"
        case .release: return "com.yourcompany.desktop-news-aggregator"
        case .appStore: return "com.yourcompany.desktop-news-aggregator"
        }
    }
    
    var apiEndpoint: String? {
        switch self {
        case .debug: return "http://localhost:8765"
        case .release, .appStore: return nil // Local only for MVP
        }
    }
}
```

## Code Signing & Notarization

```bash