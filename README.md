# LinkedIn Agent

> A Claude Code global slash command that turns your coding sessions into LinkedIn posts.

## Problem

You do interesting work every day with Claude Code, but by the time you sit down to write about it, the momentum is gone. The details blur. The post never gets written.

## Solution

Type `/linkedin` in any Claude Code session. It analyzes your git diff, recent commits, and conversation history to generate a LinkedIn post draft — right when the context is still fresh.

## Setup

Copy `linkedin.md` to your Claude Code global commands directory:

```bash
# macOS / Linux
cp linkedin.md ~/.claude/commands/linkedin.md

# Windows
copy linkedin.md %USERPROFILE%\.claude\commands\linkedin.md
```

That's it. No packages, no build step, no API keys.

## Usage

```bash
# Auto-detect content and tone
/linkedin

# Highlight a specific angle
/linkedin built a full-stack app with AI agents in one session
```

## How it works

1. Gathers context from `git diff`, `git log`, project files, and conversation history
2. Picks the best tone for your content (see below)
3. Generates a 150-300 word English post optimized for LinkedIn
4. You review, tweak, and post

## Tone Selection

The skill automatically picks the best tone based on what you built:

| Tone | Best for | Style | Hashtags |
|------|----------|-------|----------|
| **Provocative Hook** | Paradigm shifts, new approaches | Bold claim → old vs new → insight → CTA | 7-8, broad |
| **Practical Tips** | Workflow improvements, tool setups | Numbered list → tips → invite follow-up | 5, specific |
| **Discovery Share** | Hidden features, debugging journeys | Conversational → process → honest limitations | 0-3, minimal |

You can override the tone: `/linkedin --tone=tips`

## Content Guidelines

- Written in English, first-person (I did / I found / I built)
- 150-300 words (optimal for LinkedIn algorithm)
- Focus on learning and process, not self-promotion
- Sensitive info (API keys, .env values) is automatically filtered out
- First 2 lines optimized for "...see more" clicks
