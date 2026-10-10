# Jason — AI Sales & Content Agent

> A desktop AI colleague built on the Claude Agent SDK. Writes daily SEO content, triages inboxes and drafts replies, logs leads to the CRM, and turns trade-fair directories into outreach drafts — running 67 logged working days in a real one-person export business.

## Results

| Metric | Number |
|---|---|
| Logged working days | 67 (Jun 30 – Oct 10, 2026) |
| Content files produced | 233 (64 daily batches, ~3 SEO articles/day, one with a LinkedIn version) |
| Published WordPress articles | 490 total on site (includes Jason's daily output) |
| Factories digitised | 656 from 5 scanned trade-fair directories (Sep 22 – Oct 8) |
| Outreach drafts written | 509 to Gmail drafts; 83 sent covering 54 companies |
| CRM records | 61 log/follow-up entries; customer numbering past ZB0390 |

## The Problem

A one-person export company has no content or sales assistant. SEO articles, LinkedIn/Facebook versions and cold outreach all came out of the founder's own hours — hours not spent finding customers. Inquiries across multiple inboxes (Gmail, domain mail) were easy to miss and slow to answer.

An earlier attempt kept business rules in documents — but the AI would "forget to read them", so formats, signatures and file paths kept breaking. And anything outward-facing (publishing, sending mail) needed a human in the loop.

## What I Built

**Six custom tools (in-process MCP server):** task logging, today's-log lookup, WordPress draft publishing, prospect registration, email reading, email draft writing.

**A permission gate:** read-only and local-write tools auto-approve; everything else (publishing, drafting, shell commands) pops a confirmation dialog.

**Code-level guards:** a `PreToolUse` hook hard-validates file paths and article content — e.g. articles are rejected if they contain invented contact details or miss required sections. Rules live in `workspace/knowledge/*.md`, merged into `CLAUDE.md` at startup (this also works around Windows CLI length limits).

**One-switch model provider:** started on Anthropic Claude, now on DeepSeek (`v4-pro` for main work, `v4-flash` for light tasks) via an Anthropic-compatible endpoint — flipped with a single environment variable. Images go through a separate vision model and reach the main model as text.

**Fact-grounded content:** the weekend intel job scrapes snapshots with pure Python; the article job may only cite numbers from `facts.json`, and every draft gets a number cross-check before it's saved.

## Tech Stack

Python 3.13 · Claude Agent SDK (custom `@tool`, MCP server, hooks, `can_use_tool`) · DeepSeek v4 · tkinter desktop chat · WordPress REST API · IMAP + Gmail OAuth2 · FTP · self-built CRM HTTP API · python-dotenv

Runs locally on Windows, launched from a `.bat` via `pythonw` (no console window).

## Project Layout

```
my-agent/
├── agent.py                 # core: system prompt, tools, guards, permission gate
├── chat.py                  # tkinter chat window (daily entry point, image paste/drag)
├── tools/vision.py          # image → text, then handed to the text model
├── daily_article.py         # SEO articles + LinkedIn posts + image prompts
├── daily_article_intel.py   # intel edition: only cited numbers allowed
├── weekly_intel.py          # weekend intel scrape → facts.json → next week's topics
├── sync_published_log.py    # pulls all published posts → topic dedupe index
├── .env.example             # credential template (secrets never committed)
└── workspace/
    ├── knowledge/*.md       # business rule base (auto-merged to CLAUDE.md)
    ├── logs/daily_work_log.md
    ├── daily_articles/<date>/
    └── projects/<client>/   # quotes, reply drafts, fair directories
```

## Engineering Decisions Worth Noting

1. **Rules as code, not docs.** The AI ignored rules kept in documents, so rules became startup-loaded context plus hard validation in code. Wrong file paths and invented contact details are now blocked before anything is written.
2. **Human-in-the-loop by default for outward actions.** Drafts go to the Gmail drafts folder and WordPress drafts — never sent or published without review.
3. **Provider independence.** The Anthropic-compatible interface means the whole agent survives a model-vendor switch with one env var.
4. **Append-only logging.** Every run writes to a daily log, so work survives restarts and "what did you do today?" always has a truthful answer.

## Status

In daily production use since June 2026 at Hangzhou Zhenbao Trading Co., Ltd.

Source code will be added after sanitisation.

## Contact

Open to custom AI agent builds for sales and content teams.

Maggie Yue · Hangzhou Zhenbao Trading Co., Ltd. · [sales@zhenbaotrading.com](mailto:sales@zhenbaotrading.com)
