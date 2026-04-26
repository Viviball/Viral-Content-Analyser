# Content Reference Agent

Turn any social media post into a structured Airtable content reference automatically.

Content Reference Agent is a Codex skill for creators, marketers, researchers, and content strategists who collect inspiration from the internet. Give an AI a post from X/Twitter, Instagram, TikTok, LinkedIn, YouTube, or an article URL, and the skill extracts the useful details, analyzes why the content works, and saves everything into the right Airtable table.

It is designed for building a reusable content intelligence library: not just a list of links, but a searchable database of hooks, emotions, audience insights, viral mechanisms, captions, visuals, metrics, and platform context.

## What It Does

- 🔗 Takes a social media post or article link
- 🧭 Detects whether it is a `Video`, `Carousel`, `Text`, or `Article`
- 🖼️ Finds cover images and thumbnails when available
- 📊 Captures visible metrics like views, likes, comments, and shares
- 🧠 Writes content-strategy analysis for hook, messaging, emotion, audience, and viral mechanism
- 🎬 Handles video-specific fields like visual style, pacing, audio, and duration
- 📥 Saves the result directly into Airtable without manual copy-paste

## Why It Exists

Great content disappears fast in feeds. This skill turns scattered inspiration into a structured library so you can study patterns, reuse winning angles, and build stronger creative systems from real examples.

Instead of asking an AI to simply summarize a link, this skill guides it to preserve the reference as reusable creative intelligence.

## How It Works

1. The user gives an AI a social media post or article URL.
2. The AI uses `SKILL.md` to classify the reference.
3. The AI extracts metadata, media, metrics, captions, and visible context.
4. The AI writes reusable analysis fields such as hook, emotion, audience, messaging, and viral mechanism.
5. The AI creates a new Airtable record in the correct table automatically.
6. The AI reports what was saved and notes any missing or inferred fields.

## Supported Content Types

| Type | Examples | Airtable Target |
| --- | --- | --- |
| `Video` | TikTok, Reels, YouTube Shorts, X videos, LinkedIn videos | `AIRTABLE_VIDEO_TABLE` |
| `Carousel` | Instagram carousels, LinkedIn document posts, swipe posts | `AIRTABLE_CAROUSEL_TABLE` |
| `Text` | X posts, LinkedIn text posts, Threads-style posts | `AIRTABLE_TEXT_TABLE` |
| `Article` | Blog posts, newsletters, Substack posts, long-form pages | `AIRTABLE_ARTICLE_TABLE` |

## Getting Started

Clone the repository:

```bash
git clone https://github.com/YOUR_USERNAME/content-reference-agent.git
cd content-reference-agent
```

Copy the environment example:

```bash
cp .env.example .env
```

Fill in your Airtable configuration:

```bash
AIRTABLE_API_TOKEN=
AIRTABLE_BASE_ID=
AIRTABLE_VIDEO_TABLE=Video
AIRTABLE_CAROUSEL_TABLE=Carousel
AIRTABLE_TEXT_TABLE=Text
AIRTABLE_ARTICLE_TABLE=Article
```

Do not commit `.env` or real API tokens.

## Airtable Setup

Create one Airtable base with separate tables for each content type:

- `Video`
- `Carousel`
- `Text`
- `Article`

You can use table names or table IDs. Table IDs are safer because they keep working even if you rename a table.

Recommended core fields:

```text
URL
Title
Cover
Date
Platform
Creator
Views
Likes
Comments
Shares
Hook
Caption
Messaging
Emotion
Audience
Viral
Description
Niche
Visual
Pacing
Audio
Length
```

Not every table needs every field. If Airtable returns `UNKNOWN_FIELD_NAME`, the skill tells the AI to remove the unsupported field and retry once.

## Airtable Token Scopes

Your Airtable personal access token should have access to the target base and these scopes:

```text
data.records:read
data.records:write
schema.bases:read
```

`schema.bases:read` is useful because the skill can inspect field names, select options, and duration formats before writing.

## Usage

Ask your AI to process a link:

```text
Analyze and save this:
https://x.com/example/status/123456789
```

Or:

```text
Add this Instagram Reel to my content library:
https://www.instagram.com/reel/...
```

Or:

```text
Save this article as inspiration:
https://example.com/blog/post
```

When Airtable credentials and table configuration are available, the skill is designed to save automatically. It should not ask for a separate confirmation before creating the Airtable record.

## Example Output

After saving, the AI should respond with a compact summary:

```text
Saved to Content Library.

Type: Video
Platform: Twitter
Creator: @creator
Hook: A product demo turns a vague AI trend into a concrete future-interface example.
Emotion: Curiosity, wonder, possibility
Viral: Why viral: the post gives people a visual artifact they can share to signal taste and early awareness.
Cover: found from video thumbnail
Airtable: saved to Video
```

## Duration Handling

Airtable duration fields are format-sensitive.

If a table uses an `h:mm` duration format, short videos can appear incorrectly as `0:00` or `0:01` when raw seconds are saved. The skill includes rules for detecting this and normalizing values so Airtable displays the intended duration.

For example:

```text
5 second clip  -> save 300  -> displays as 0:05 in an h:mm field
15 second clip -> save 900  -> displays as 0:15 in an h:mm field
30 second clip -> save 1800 -> displays as 0:30 in an h:mm field
```

Use this only for confirmed `h:mm` duration fields. For seconds-aware duration or number fields, save seconds directly.

## Platform Notes

Some platforms hide useful data from logged-out requests.

- Instagram and LinkedIn may require authenticated browser context.
- X/Twitter may expose useful data through page state or oEmbed.
- YouTube and articles often expose metadata through Open Graph, Twitter cards, or JSON-LD.
- If metrics or duration are not available, the skill should omit them instead of guessing.

The skill must not ask users for passwords, cookies, or browser session tokens.

## Repository Structure

| File | Purpose |
| --- | --- |
| `SKILL.md` | Main Codex skill instructions and workflow |
| `.env.example` | Environment variable template |
| `.gitignore` | Keeps secrets and local files out of Git |
| `CONTRIBUTING.md` | Contribution guidelines |
| `SECURITY.md` | Secret-handling and platform-access guidance |

## Development

Check the repository state:

```bash
git status --short
```

Check for whitespace issues:

```bash
git diff --check
```

Search for accidental secrets before pushing:

```bash
rg "pat[A-Za-z0-9]|AIRTABLE_API_TOKEN=.*\\S|cookie|auth_token" --glob '!README.md'
```

## Safety

- 🔐 Never commit Airtable tokens, cookies, passwords, or browser session data
- 🚧 Do not bypass login gates, privacy controls, or platform limits
- 🧾 Omit uncertain fields instead of inventing data
- 🧩 Use existing Airtable select options when fields are constrained
- 🧪 Test changes with a small number of records before bulk updates

## Suggested GitHub Description

```text
Codex skill that turns social media and article links into structured Airtable content-reference records with metadata, cover images, metrics, and viral content analysis.
```

## Suggested Topics

```text
codex-skill airtable content-strategy social-media metadata-scraping content-library ai-workflow
```
