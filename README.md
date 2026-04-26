# Content Reference Agent

Codex skill for turning social posts, videos, carousels, text posts, and articles into structured Airtable content-reference records.

## Repository Description

AI-assisted content reference intake skill that extracts platform metadata, classifies content type, captures cover imagery, writes reusable viral analysis, and creates the right Airtable record.

Suggested GitHub topics:

```text
codex-skill airtable content-strategy social-media metadata-scraping content-library
```

## What It Does

- Lets an AI receive a social media post link and automate the Airtable entry field by field.
- Routes each URL as `Video`, `Carousel`, `Text`, or `Article`.
- Extracts creator, platform, date, caption, cover image, engagement, and duration when available.
- Writes strategy-focused fields such as hook, messaging, emotion, audience, visual system, pacing, audio, and viral mechanism.
- Handles LinkedIn and Instagram carefully through authenticated browser context when public metadata is incomplete.
- Includes Airtable duration handling for short-form videos where duration fields may display minutes instead of seconds.

## Files

| File | Purpose |
| --- | --- |
| `SKILL.md` | The Codex skill instructions and workflow. |
| `.env.example` | Local environment variable template. |
| `.gitignore` | Keeps tokens, scratch files, and local editor files out of Git. |

## Required Configuration

The skill expects these values to be available during use:

```bash
AIRTABLE_API_TOKEN=
AIRTABLE_BASE_ID=
AIRTABLE_VIDEO_TABLE=
AIRTABLE_CAROUSEL_TABLE=
AIRTABLE_TEXT_TABLE=
AIRTABLE_ARTICLE_TABLE=
```

Do not commit real tokens. Copy `.env.example` to `.env` for local use if your runner loads environment files.

## Airtable Schema

The skill writes to separate Airtable tables for:

- Video
- Carousel
- Text
- Article

Core fields include:

```text
URL, Hook, Emotion, Viral, Description, Platform, Creator, Date, Cover,
Type, Messaging, Target Audience, Engagement, Notes, Audience,
Comments, Visual, Audio, Pacing, Caption, Length
```

Unsupported fields are removed and retried once if Airtable returns `UNKNOWN_FIELD_NAME`.

## Duration Notes

Airtable duration fields are format-sensitive. If a table uses an `h:mm` duration format, short-form video seconds can display incorrectly as `0:00` or `0:01`. The skill includes explicit handling for this case and should inspect field metadata when possible before writing `Length`.

## Safety

- Never commit API tokens, cookies, session storage, or browser credentials.
- Do not bypass login gates, privacy settings, or platform limits.
- If platform metadata is unavailable, omit uncertain fields rather than guessing.
- Use authenticated browser context only through the user's normal logged-in session.

## Development

This repository is currently documentation-first: the main artifact is the `SKILL.md` instruction file. Future helper scripts can live under `scripts/`, with tests added under `tests/`.

Before committing:

```bash
git status --short
git diff --check
```
