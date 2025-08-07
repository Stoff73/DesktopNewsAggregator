# Database Schema

## SQLite Database Structure

```sql
-- Enable Foreign Keys
PRAGMA foreign_keys = ON;

-- Enable FTS5 Extension
-- Loaded at runtime via SQLite.swift

-- Sources Table
CREATE TABLE sources (
    id TEXT PRIMARY KEY,
    name TEXT NOT NULL,
    url TEXT NOT NULL UNIQUE,
    type TEXT NOT NULL CHECK(type IN ('rss', 'website', 'api')),
    enabled INTEGER NOT NULL DEFAULT 1,
    last_fetch_date INTEGER,
    last_fetch_error TEXT,
    fetch_interval INTEGER NOT NULL DEFAULT 900,
    priority INTEGER NOT NULL DEFAULT 0,
    selectors TEXT, -- JSON for web scraping selectors
    created_at INTEGER NOT NULL,
    updated_at INTEGER NOT NULL
);

CREATE INDEX idx_sources_enabled ON sources(enabled);
CREATE INDEX idx_sources_priority ON sources(priority DESC);

-- Articles Table
CREATE TABLE articles (
    id TEXT PRIMARY KEY,
    source_id TEXT NOT NULL,
    url TEXT NOT NULL UNIQUE,
    title TEXT NOT NULL,
    summary TEXT,
    content TEXT,
    published_date INTEGER NOT NULL,
    fetched_date INTEGER NOT NULL,
    image_url TEXT,
    read_status TEXT NOT NULL DEFAULT 'unread' CHECK(read_status IN ('unread', 'scanned', 'read')),
    ai_summary TEXT,
    ai_summary_date INTEGER,
    created_at INTEGER NOT NULL,
    updated_at INTEGER NOT NULL,
    FOREIGN KEY (source_id) REFERENCES sources(id) ON DELETE CASCADE
);

CREATE INDEX idx_articles_source ON articles(source_id);
CREATE INDEX idx_articles_published ON articles(published_date DESC);
CREATE INDEX idx_articles_fetched ON articles(fetched_date DESC);
CREATE INDEX idx_articles_read_status ON articles(read_status);

-- Full-Text Search Virtual Table
CREATE VIRTUAL TABLE articles_fts USING fts5(
    title,
    summary,
    content,
    content=articles,
    content_rowid=rowid,
    tokenize='porter unicode61'
);

-- Triggers to keep FTS in sync
CREATE TRIGGER articles_ai AFTER INSERT ON articles BEGIN
    INSERT INTO articles_fts(rowid, title, summary, content)
    VALUES (new.rowid, new.title, new.summary, new.content);
END;

CREATE TRIGGER articles_ad AFTER DELETE ON articles BEGIN
    DELETE FROM articles_fts WHERE rowid = old.rowid;
END;

CREATE TRIGGER articles_au AFTER UPDATE ON articles BEGIN
    UPDATE articles_fts SET 
        title = new.title,
        summary = new.summary,
        content = new.content
    WHERE rowid = new.rowid;
END;

-- Keywords Table
CREATE TABLE keywords (
    id TEXT PRIMARY KEY,
    term TEXT NOT NULL,
    pattern TEXT NOT NULL,
    color_red REAL NOT NULL,
    color_green REAL NOT NULL,
    color_blue REAL NOT NULL,
    color_alpha REAL NOT NULL DEFAULT 1.0,
    priority INTEGER NOT NULL DEFAULT 0,
    case_sensitive INTEGER NOT NULL DEFAULT 0,
    whole_word INTEGER NOT NULL DEFAULT 0,
    profile_id TEXT,
    created_date INTEGER NOT NULL,
    match_count INTEGER NOT NULL DEFAULT 0,
    created_at INTEGER NOT NULL,
    updated_at INTEGER NOT NULL
);

CREATE INDEX idx_keywords_priority ON keywords(priority DESC);
CREATE INDEX idx_keywords_profile ON keywords(profile_id);

-- Keyword Matches Junction Table
CREATE TABLE keyword_matches (
    id TEXT PRIMARY KEY,
    article_id TEXT NOT NULL,
    keyword_id TEXT NOT NULL,
    locations TEXT NOT NULL, -- JSON array of match locations
    match_count INTEGER NOT NULL,
    context TEXT,
    created_at INTEGER NOT NULL,
    FOREIGN KEY (article_id) REFERENCES articles(id) ON DELETE CASCADE,
    FOREIGN KEY (keyword_id) REFERENCES keywords(id) ON DELETE CASCADE
);

CREATE INDEX idx_matches_article ON keyword_matches(article_id);
CREATE INDEX idx_matches_keyword ON keyword_matches(keyword_id);
CREATE UNIQUE INDEX idx_matches_unique ON keyword_matches(article_id, keyword_id);

-- User Settings Table (Single Row)
CREATE TABLE user_settings (
    id TEXT PRIMARY KEY DEFAULT 'main',
    fetch_schedule TEXT NOT NULL, -- JSON array of fetch times
    view_mode TEXT NOT NULL DEFAULT 'focus',
    article_retention INTEGER NOT NULL DEFAULT 604800, -- 7 days in seconds
    max_articles_per_source INTEGER NOT NULL DEFAULT 50,
    enable_ai_summaries INTEGER NOT NULL DEFAULT 0,
    openai_key TEXT, -- Encrypted
    color_scheme TEXT NOT NULL DEFAULT 'auto',
    created_at INTEGER NOT NULL,
    updated_at INTEGER NOT NULL,
    CHECK (id = 'main') -- Ensure single row
);

-- Research Reports Table (Phase 2)
CREATE TABLE research_reports (
    id TEXT PRIMARY KEY,
    article_id TEXT NOT NULL,
    agent_id TEXT,
    report_type TEXT NOT NULL,
    content TEXT NOT NULL,
    metadata TEXT, -- JSON
    created_at INTEGER NOT NULL,
    FOREIGN KEY (article_id) REFERENCES articles(id) ON DELETE CASCADE
);

CREATE INDEX idx_reports_article ON research_reports(article_id);
CREATE INDEX idx_reports_created ON research_reports(created_at DESC);

-- Migration Tracking
CREATE TABLE schema_migrations (
    version INTEGER PRIMARY KEY,
    applied_at INTEGER NOT NULL
);

-- Initial Data
INSERT INTO user_settings (
    id, fetch_schedule, view_mode, article_retention,
    max_articles_per_source, enable_ai_summaries, color_scheme,
    created_at, updated_at
) VALUES (
    'main',
    '[{"hour":8,"minute":0,"enabled":true},{"hour":12,"minute":0,"enabled":true},{"hour":18,"minute":0,"enabled":true}]',
    'focus',
    604800,
    50,
    0,
    'auto',
    strftime('%s', 'now'),
    strftime('%s', 'now')
);
```

## Database Access Patterns

**High-Frequency Queries:**
- Fetch recent articles with keyword matches (main feed)
- Full-text search across articles
- Keyword match detection for new articles
- Source status checks for scheduling

**Optimization Strategies:**
- Prepared statements for all queries
- Connection pooling (3 connections)
- In-memory cache for keywords
- Batch inserts for articles
- Automatic VACUUM weekly
