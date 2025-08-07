# Monitoring and Observability

## Logging Strategy

```swift
// MARK: - Structured Logging
import os.log

struct Logger {
    private let subsystem = Bundle.main.bundleIdentifier ?? "com.desktop-news-aggregator"
    private let category: String
    private let osLog: OSLog
    
    init(category: String) {
        self.category = category
        self.osLog = OSLog(subsystem: subsystem, category: category)
    }
    
    func debug(_ message: String, metadata: [String: Any] = [:]) {
        os_log(.debug, log: osLog, "%{public}@ %{private}@", message, metadata.description)
    }
    
    func info(_ message: String, metadata: [String: Any] = [:]) {
        os_log(.info, log: osLog, "%{public}@ %{private}@", message, metadata.description)
    }
    
    func warning(_ message: String, metadata: [String: Any] = [:]) {
        os_log(.error, log: osLog, "⚠️ %{public}@ %{private}@", message, metadata.description)
    }
    
    func error(_ message: String, error: Error? = nil, metadata: [String: Any] = [:]) {
        var meta = metadata
        if let error = error {
            meta["error"] = error.localizedDescription
        }
        os_log(.fault, log: osLog, "❌ %{public}@ %{private}@", message, meta.description)
    }
    
    func measure<T>(_ label: String, operation: () throws -> T) rethrows -> T {
        let start = CFAbsoluteTimeGetCurrent()
        defer {
            let duration = CFAbsoluteTimeGetCurrent() - start
            os_log(.info, log: osLog, "⏱ %{public}@ took %.3f seconds", label, duration)
        }
        return try operation()
    }
}

// MARK: - Log Categories
enum LogCategory {
    static let network = Logger(category: "Network")
    static let database = Logger(category: "Database")
    static let ui = Logger(category: "UI")
    static let fetching = Logger(category: "Fetching")
    static let keywords = Logger(category: "Keywords")
    static let ai = Logger(category: "AI")
}
```

## Performance Monitoring

```swift
// MARK: - Metrics Collection
actor MetricsCollector {
    static let shared = MetricsCollector()
    
    private var metrics: [String: [Double]] = [:]
    private var counters: [String: Int] = [:]
    
    func record(_ metric: String, value: Double) {
        metrics[metric, default: []].append(value)
        
        // Keep only last 1000 values
        if metrics[metric]!.count > 1000 {
            metrics[metric]!.removeFirst()
        }
    }
    
    func increment(_ counter: String, by value: Int = 1) {
        counters[counter, default: 0] += value
    }
    
    func getStatistics(for metric: String) -> MetricStatistics? {
        guard let values = metrics[metric], !values.isEmpty else {
            return nil
        }
        
        let sorted = values.sorted()
        return MetricStatistics(
            count: values.count,
            mean: values.reduce(0, +) / Double(values.count),
            median: sorted[sorted.count / 2],
            p95: sorted[Int(Double(sorted.count) * 0.95)],
            p99: sorted[Int(Double(sorted.count) * 0.99)],
            min: sorted.first!,
            max: sorted.last!
        )
    }
    
    func generateReport() -> MetricsReport {
        var report = MetricsReport()
        
        // Performance metrics
        report.fetchTime = getStatistics(for: "fetch.duration")
        report.searchTime = getStatistics(for: "search.duration")
        report.renderTime = getStatistics(for: "render.duration")
        
        // Counters
        report.totalArticles = counters["articles.fetched"] ?? 0
        report.totalKeywordMatches = counters["keywords.matched"] ?? 0
        report.errorCount = counters["errors.total"] ?? 0
        
        return report
    }
}

struct MetricStatistics {
    let count: Int
    let mean: Double
    let median: Double
    let p95: Double
    let p99: Double
    let min: Double
    let max: Double
}
```

## Crash Reporting

```swift
// MARK: - Crash Handler
class CrashReporter {
    static let shared = CrashReporter()
    
    func configure() {
        // Register exception handler
        NSSetUncaughtExceptionHandler { exception in
            self.handleException(exception)
        }
        
        // Register signal handlers
        signal(SIGABRT) { _ in
            CrashReporter.shared.handleSignal("SIGABRT")
        }
        signal(SIGSEGV) { _ in
            CrashReporter.shared.handleSignal("SIGSEGV")
        }
        signal(SIGBUS) { _ in
            CrashReporter.shared.handleSignal("SIGBUS")
        }
    }
    
    private func handleException(_ exception: NSException) {
        let report = CrashReport(
            type: .exception,
            reason: exception.reason ?? "Unknown",
            callStack: exception.callStackSymbols,
            userInfo: exception.userInfo ?? [:],
            timestamp: Date()
        )
        
        saveCrashReport(report)
    }
    
    private func handleSignal(_ signal: String) {
        let report = CrashReport(
            type: .signal,
            reason: signal,
            callStack: Thread.callStackSymbols,
            userInfo: [:],
            timestamp: Date()
        )
        
        saveCrashReport(report)
    }
    
    private func saveCrashReport(_ report: CrashReport) {
        // Save to disk for later submission
        let crashLogPath = FileManager.default
            .urls(for: .applicationSupportDirectory, in: .userDomainMask)[0]
            .appendingPathComponent("Crashes")
            .appendingPathComponent("\(report.timestamp.timeIntervalSince1970).crash")
        
        try? FileManager.default.createDirectory(
            at: crashLogPath.deletingLastPathComponent(),
            withIntermediateDirectories: true
        )
        
        if let data = try? JSONEncoder().encode(report) {
            try? data.write(to: crashLogPath)
        }
    }
}
```

## Analytics Dashboard

```swift
// MARK: - Usage Analytics
struct AnalyticsEvent {
    let name: String
    let properties: [String: Any]
    let timestamp: Date
}

class Analytics {
    static let shared = Analytics()
    private let logger = LogCategory.ui
    
    func track(_ event: String, properties: [String: Any] = [:]) {
        // Privacy-preserving analytics
        let sanitized = sanitizeProperties(properties)
        
        logger.info("Analytics: \(event)", metadata: sanitized)
        
        Task {
            await MetricsCollector.shared.increment("analytics.\(event)")
        }
    }
    
    private func sanitizeProperties(_ properties: [String: Any]) -> [String: Any] {
        var sanitized: [String: Any] = [:]
        
        for (key, value) in properties {
            // Remove PII
            if !["email", "name", "apiKey"].contains(key) {
                sanitized[key] = value
            }
        }
        
        return sanitized
    }
}

// MARK: - Common Analytics Events
extension Analytics {
    func trackAppLaunch() {
        track("app.launched", properties: [
            "version": Bundle.main.infoDictionary?["CFBundleShortVersionString"] ?? "unknown",
            "build": Bundle.main.infoDictionary?["CFBundleVersion"] ?? "unknown"
        ])
    }
    
    func trackArticleRead(_ article: Article) {
        track("article.read", properties: [
            "source": article.sourceId,
            "hasKeywords": !article.keywordMatches.isEmpty,
            "readTime": Date().timeIntervalSince(article.fetchedDate)
        ])
    }
    
    func trackKeywordAdded(_ keyword: Keyword) {
        track("keyword.added", properties: [
            "hasRegex": keyword.pattern != keyword.term,
            "priority": keyword.priority
        ])
    }
}
```
