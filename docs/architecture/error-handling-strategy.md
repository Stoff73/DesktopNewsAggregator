# Error Handling Strategy

## Error Types Hierarchy

```swift
// MARK: - Base Error Protocol
protocol AppError: LocalizedError {
    var code: String { get }
    var isRecoverable: Bool { get }
    var userAction: String? { get }
    var logLevel: LogLevel { get }
}

// MARK: - Domain-Specific Errors
enum NetworkError: AppError {
    case noConnection
    case timeout(TimeInterval)
    case invalidResponse(Int)
    case rateLimited(retryAfter: Date)
    case certificateInvalid
    
    var code: String {
        switch self {
        case .noConnection: return "NET_001"
        case .timeout: return "NET_002"
        case .invalidResponse: return "NET_003"
        case .rateLimited: return "NET_004"
        case .certificateInvalid: return "NET_005"
        }
    }
    
    var isRecoverable: Bool {
        switch self {
        case .noConnection, .timeout, .rateLimited:
            return true
        case .invalidResponse, .certificateInvalid:
            return false
        }
    }
}

enum DatabaseError: AppError {
    case connectionFailed
    case migrationFailed(version: Int)
    case constraintViolation(String)
    case corruptedData
    case diskFull
    
    var logLevel: LogLevel {
        switch self {
        case .corruptedData, .migrationFailed:
            return .critical
        case .diskFull:
            return .error
        default:
            return .warning
        }
    }
}

enum ParsingError: AppError {
    case invalidFormat(expected: String, got: String)
    case missingRequiredField(String)
    case malformedHTML
    case unsupportedFeedVersion(String)
}
```

## Error Handling Patterns

```swift
// MARK: - Centralized Error Handler
actor ErrorHandler {
    static let shared = ErrorHandler()
    
    private var errorCounts: [String: Int] = [:]
    private var suppressedErrors: Set<String> = []
    
    func handle(_ error: AppError, context: ErrorContext) async {
        // Log error
        await log(error, context: context)
        
        // Track error frequency
        errorCounts[error.code, default: 0] += 1
        
        // Check if should suppress
        if shouldSuppress(error) {
            return
        }
        
        // Attempt recovery
        if error.isRecoverable {
            await attemptRecovery(error, context: context)
        }
        
        // Notify user if needed
        if shouldNotifyUser(error) {
            await notifyUser(error)
        }
        
        // Report critical errors
        if error.logLevel == .critical {
            await reportToCrashlytics(error, context: context)
        }
    }
    
    private func attemptRecovery(_ error: AppError, context: ErrorContext) async {
        switch error {
        case let networkError as NetworkError:
            await handleNetworkRecovery(networkError, context: context)
        case let dbError as DatabaseError:
            await handleDatabaseRecovery(dbError, context: context)
        default:
            break
        }
    }
}

// MARK: - Error Context
struct ErrorContext {
    let function: String
    let file: String
    let line: Int
    let additionalInfo: [String: Any]
    
    init(
        function: String = #function,
        file: String = #file,
        line: Int = #line,
        additionalInfo: [String: Any] = [:]
    ) {
        self.function = function
        self.file = URL(fileURLWithPath: file).lastPathComponent
        self.line = line
        self.additionalInfo = additionalInfo
    }
}
```

## Retry Strategies

```swift
// MARK: - Retry Policy
struct RetryPolicy {
    let maxAttempts: Int
    let delay: TimeInterval
    let backoffMultiplier: Double
    let maxDelay: TimeInterval
    let jitter: Bool
    
    static let `default` = RetryPolicy(
        maxAttempts: 3,
        delay: 1.0,
        backoffMultiplier: 2.0,
        maxDelay: 60.0,
        jitter: true
    )
    
    static let aggressive = RetryPolicy(
        maxAttempts: 5,
        delay: 0.5,
        backoffMultiplier: 1.5,
        maxDelay: 30.0,
        jitter: true
    )
}

// MARK: - Retry Implementation
func withRetry<T>(
    policy: RetryPolicy = .default,
    operation: () async throws -> T
) async throws -> T {
    var lastError: Error?
    
    for attempt in 1...policy.maxAttempts {
        do {
            return try await operation()
        } catch {
            lastError = error
            
            // Check if error is retryable
            if let appError = error as? AppError,
               !appError.isRecoverable {
                throw error
            }
            
            // Calculate delay
            if attempt < policy.maxAttempts {
                let baseDelay = policy.delay * pow(policy.backoffMultiplier, Double(attempt - 1))
                var delay = min(baseDelay, policy.maxDelay)
                
                if policy.jitter {
                    delay *= Double.random(in: 0.8...1.2)
                }
                
                try await Task.sleep(nanoseconds: UInt64(delay * 1_000_000_000))
            }
        }
    }
    
    throw lastError ?? NetworkError.timeout(policy.maxDelay)
}
```

## User-Facing Error Messages

```swift
// MARK: - Error Presentation
extension AppError {
    var userMessage: String {
        switch self {
        case let netError as NetworkError:
            switch netError {
            case .noConnection:
                return "No internet connection. Please check your network settings."
            case .timeout:
                return "Request timed out. Please try again."
            case .rateLimited(let retryAfter):
                let formatter = RelativeDateTimeFormatter()
                return "Too many requests. Try again \(formatter.string(from: retryAfter))"
            default:
                return "Network error occurred. Please try again later."
            }
            
        case let dbError as DatabaseError:
            switch dbError {
            case .diskFull:
                return "Storage is full. Please free up space and try again."
            case .corruptedData:
                return "Data corruption detected. Please restart the application."
            default:
                return "Database error occurred. Please restart the application."
            }
            
        default:
            return errorDescription ?? "An unexpected error occurred."
        }
    }
}

// MARK: - Error Alert
struct ErrorAlert: ViewModifier {
    @Binding var error: AppError?
    
    func body(content: Content) -> some View {
        content
            .alert(
                "Error",
                isPresented: .constant(error != nil),
                presenting: error
            ) { error in
                if error.isRecoverable {
                    Button("Retry") {
                        // Retry action
                    }
                    Button("Cancel", role: .cancel) {
                        self.error = nil
                    }
                } else {
                    Button("OK") {
                        self.error = nil
                    }
                }
            } message: { error in
                Text(error.userMessage)
            }
    }
}
```
