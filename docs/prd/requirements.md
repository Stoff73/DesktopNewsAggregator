# Requirements

## Functional Requirements

**Data Foundation (Prerequisites)**
- **[MVP]** FR1: Articles shall be stored locally in SQLite with full-text search capabilities and keyword analytics tracking
- **[MVP]** FR2: The system shall support adding/removing news sources including RSS feeds and direct URLs (10-15 sources minimum)
- **[MVP]** FR3: The system shall extract article content from JavaScript-heavy websites using headless WebKit
- **[Phase 2]** FR4: Background fetch operations shall run without blocking the UI, with configurable schedules (morning/lunch/evening)
- **[Phase 2]** FR5: Extracted content shall explicitly remove all advertisements, comments, social elements, and auto-playing media

**Core Display**
- **[MVP]** FR6: The application shall display news articles in a card-based interface with headline and 2-line summary (first 2 sentences for MVP, AI in Phase 2)
- **[Phase 2]** FR7: The system shall generate AI-powered summaries using OpenAI API (gpt-4.1-mini model) with caching and fallback to first paragraph on failure
- **[Phase 2]** FR8: The application shall provide three viewing modes: Focus Mode (full cards), Compact Mode (headlines only), and Reader Mode (expanded)
- **[Phase 3]** FR9: An Executive Summary mode shall display only high-priority articles based on keyword frequency and configurable thresholds
- **[MVP]** FR10: Each article card shall link directly to the original source website when clicked

**Visual Intelligence System**
- **[MVP]** FR11: Users shall configure keywords with up to 5 keywords/phrases for MVP (expanding to 20 per profile in Phase 2), each with custom colors
- **[MVP]** FR12: Article cards shall highlight matching keywords with configured background colors in title and summary text
- **[MVP]** FR13: Cards containing keywords shall display colored borders or badges matching the keyword's color for instant scanning
- **[Phase 2]** FR14: When multiple keywords match, cards shall display stacked badges or multi-color borders with conflict resolution (priority-based)
- **[Phase 2]** FR15: The system shall display color-coded frequency counters showing keyword occurrence across all fetched articles
- **[Phase 2]** FR16: Users shall switch between multiple keyword profiles (e.g., "Morning Brief", "Competitor Watch", "Research") instantly

**Advanced Features**
- **[Phase 2]** FR17: Users shall filter articles by keyword, source, or date with results updating in real-time
- **[Phase 3]** FR18: The system shall provide a color density heat map showing keyword concentration by source
- **[Phase 2]** FR19: The application shall support keyboard navigation (spacebar=scroll, arrows=navigate, Cmd+R=refresh)
- **[MVP]** FR20: A basic settings panel shall manage sources and keywords with color pickers (simplified for MVP)
- **[Phase 2]** FR21: The system shall display fetch failures with retry options and time until next automatic retry

**Ambient Awareness Features**
- **[Phase 3]** FR22: The system shall provide a menu bar ticker displaying real-time keyword match counts and alerts
- **[Phase 3]** FR23: A Today Widget shall show keyword summary and top articles without opening the main application
- **[Phase 3]** FR24: Notification Center integration shall alert users when keyword thresholds are exceeded

**Analytics & Measurement**
- **[Phase 2]** FR25: The system shall track and display reading time analytics including time per article, daily totals, and efficiency trends
- **[Phase 2]** FR26: A dashboard shall show reading time reduction percentage compared to baseline (first week of usage)
- **[Phase 2]** FR27: The system shall track which articles were actually read vs. just scanned

**Future-Ready Architecture**
- **[Phase 3]** FR28: The system shall expose a local REST API for agent integration with endpoints for search, retrieval, and keyword analysis
- **[Phase 3]** FR29: Article data shall be structured in agent-compatible JSON format with metadata, entities, and relationships
- **[Phase 3]** FR30: The system shall support plugin architecture for future agent tools and extensions

## Non-Functional Requirements

**Performance**
- **[MVP]** NFR1: The application shall launch in under 5 seconds
- **[MVP]** NFR2: All sources shall be fetched and displayed within 10 seconds
- **[MVP]** NFR3: Visual pattern recognition shall enable topic identification in under 10 seconds
- **[MVP]** NFR4: Smooth scrolling at minimum 30fps with up to 200 articles
- **[MVP]** NFR5: Memory usage shall not exceed 200MB
- **[Phase 3]** NFR6: Menu bar ticker shall update within 500ms of new keyword matches

**Platform & Compatibility**
- **[MVP]** NFR7: The system shall support macOS 11.0+ (Big Sur and above) on Intel (2015+) and Apple Silicon
- **[MVP]** NFR8: The UI shall scale responsively from 13" to 32" displays
- **[MVP]** NFR9: The application shall run without admin privileges or kernel extensions
- **[Phase 3]** NFR10: Widgets shall function independently with minimal memory footprint (<20MB)

**Reliability & Accuracy**
- **[Phase 2]** NFR11: The system shall achieve 99% successful fetch rate with exponential backoff retry
- **[MVP]** NFR12: Keyword matching shall achieve 90% accuracy (relaxed for MVP)
- **[Phase 2]** NFR13: Background fetches shall respect system resources and pause during high CPU/memory pressure
- **[Phase 2]** NFR14: Reading analytics shall be accurate within 5% of actual time spent

**Cost & Security**
- **[Phase 2]** NFR15: OpenAI API costs shall not exceed $50/month through aggressive caching
- **[Phase 2]** NFR16: All API keys and credentials shall be stored in macOS Keychain
- **[Phase 2]** NFR17: The summary cache shall persist between sessions to minimize API calls
- **[Phase 3]** NFR18: Local API shall require authentication token to prevent unauthorized access

**Accessibility**
- **[Phase 2]** NFR19: Color-blind users shall have pattern/shape alternatives for keyword identification
- **[Phase 2]** NFR20: All UI elements shall support VoiceOver for screen reader compatibility
- **[Phase 3]** NFR21: High contrast mode shall be available for users with visual impairments

**Measurable Success Criteria**
- **[Phase 2]** NFR22: The system shall demonstrate 60% reduction in news consumption time within 2 weeks of regular use
- **[MVP]** NFR23: Pattern recognition shall enable users to identify day's key topics in under 10 seconds
- **[Phase 2]** NFR24: The system shall handle research analyst workloads (20-30 keywords, 15+ sources) without performance degradation
