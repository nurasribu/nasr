# nasr

Personal blog and portfolio site for Nur Asri bin Bujang. Static HTML, no build tools, no frameworks.

## Structure

```
├── index.html          # Homepage (topics, reading, quotes)
├── blog.html           # Blog listing page
├── post.html           # Individual blog post viewer
├── blog/               # Blog posts in markdown
│   ├── intro.md        # Pinned at top of blog listing
│   ├── post1.md        # July 2026
│   ├── post2.md        # July 2026
│   └── ...
├── reading/            # Reading list entries
├── quotes/             # Quotes (quotes.md)
├── photos/             # Photo entries
├── images/             # Static images
└── style.css           # Single stylesheet
```

## How it works

All pages are static HTML. Blog posts are `.md` files fetched at runtime via JavaScript and rendered client-side. No server required — works on GitHub Pages or any static host.

`blog.html` reads from a hardcoded `blogFiles` array, fetches each `.md` file, parses the first 3 lines for title, date, and topics, and renders the list.

`post.html` reads `?id=` from the URL, fetches `blog/{id}.md`, and renders it.

## Blog post format

```markdown
# Title
Blog · Month Year
topics: topic1, topic2

Body text here...
```

- Line 1: title (rendered as `<h1>`)
- Line 2: meta line (date displayed in listing)
- Line 3 (optional): comma-separated topics (must match keys in `topicMeta` in blog.html)
- Line 4+: body content

## Important: keeping blog lists in sync

The blog post list appears in **two places** that must stay in sync:

1. `blog.html` — the `blogFiles` array (what renders the listing)
2. `index.html` — the prefetch cache `blogFiles` array (lines ~127-132)

`index.html` prefetches all blog posts into `sessionStorage` so `blog.html` loads instantly if navigated from the homepage. If `index.html`'s list is out of date, `blog.html` will use stale cached data and posts will appear missing.

**When adding or removing a post, update both arrays.**

## Post ordering

1. `intro.md` — pinned at top
2. `post13.md` — latest addition (July 2026)
3. Remaining posts — sorted newest to oldest by date

Current order (as of July 2026):

| Posts | Date |
|-------|------|
| intro | June 2026 (pinned) |
| post13 | July 2026 (latest) |
| post1, post2, post4, post5, post6, post9, post10 | July 2026 |
| post3 | June 2025 |
| post7 | July 2025 |
| post8, post12 | May 2025 |

## Topic filtering

Topics on `index.html` link to `blog.html?topic={key}`. The `topicMeta` object in `blog.html` maps topic keys to display names and descriptions. When adding a new topic, add it to both `topicMeta` in `blog.html` and the `<dl>` on `index.html`.

## Notes for LLM development

- **No build step.** Edits to `.html` or `.md` files are immediately visible. No `npm install`, no compilation.
- **Two sources of truth for blog posts.** `blog.html` and `index.html` both contain a `blogFiles` array. Always update both.
- **Client-side markdown parsing.** The `parseMd()` function in both `blog.html` and `post.html` parses the first 3 lines manually (not a markdown library). Changes to post format require updating both parsers.
- **Session storage cache.** `index.html` prefetches posts into `sessionStorage.blogCache`. This can cause stale data if the arrays diverge. Clearing browser storage or hard-refreshing fixes it.
- **No routing.** `post.html` uses `?id=` query params. The ID is the filename without `.md` (e.g., `post13`).
- **Static hosting.** This site is designed for GitHub Pages or similar. No server-side logic.
