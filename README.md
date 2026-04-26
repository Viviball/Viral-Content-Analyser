# Viral Content Analyser

Turn any social media post into a structured Airtable content reference automatically.

Viral Content Analyser is a Codex skill for creators, marketers, researchers, and content strategists who collect inspiration from the internet. Give an AI a post from X/Twitter, Instagram, TikTok, LinkedIn, YouTube, or an article URL, and the skill extracts the useful details, analyzes why the content works, and saves everything into the right Airtable table.

It is designed for building a reusable content intelligence library: not just a list of links, but a searchable database of hooks, emotions, audience insights, viral mechanisms, captions, visuals, metrics, and platform context.

## What It Does

- 🔗 **Turn any link into a structured record** — paste a post, video, carousel, or article and get a complete Airtable entry automatically
- 🧠 **Understand why content works** — every record includes hook, emotion, target audience, and viral mechanism so you can study and reuse winning patterns
- 📊 **Capture performance data without the effort** — views, likes, comments, and cover images pulled from the page automatically
- 🔥 **Build your content library without busywork** — routes to the right table, fills the right fields, and saves without asking

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

## Analysis Mechanism

Viral Content Analyser does not only summarize a post. It breaks the content into reusable strategic components, then maps those components to Airtable fields.

The analysis follows this mechanism:

1. **Evidence Capture**
   The AI first collects observable facts: URL, platform, creator, date, caption, media type, cover image, visible metrics, video duration, and any accessible page metadata. It separates verified data from inferred analysis.

2. **Content-Type Classification**
   The AI decides whether the reference is mainly a `Video`, `Carousel`, `Text`, or `Article`. This determines which Airtable table receives the record and which fields matter most.

3. **Hook Diagnosis**
   The AI identifies what makes someone stop scrolling: the first line, first frame, visual surprise, title promise, contradiction, proof, transformation, or curiosity gap.

4. **Message Extraction**
   The AI reduces the post to its core message: what the creator wants the viewer to believe, feel, try, buy, copy, or remember.

5. **Audience Psychology**
   The AI identifies who the post is for and why that audience would care. It looks for identity signals, desires, pain points, aspirations, fears, status cues, and jobs-to-be-done.

6. **Emotion Mapping**
   The AI names the emotional driver behind engagement, such as curiosity, relief, aspiration, urgency, disbelief, envy, belonging, validation, delight, or fear of missing out.

7. **Retention and Format Mechanics**
   For videos and carousels, the AI looks at pacing, sequence, reveals, proof points, before/after structure, demonstrations, visual rhythm, and payoff. For text and articles, it looks at framing, argument structure, specificity, and shareability.

8. **Visual and Audio Reading**
   The AI describes the visual system: composition, first frame, demo style, overlays, editing pattern, color, screenshots, proof, or interface shown. If audio is available, it notes whether voiceover, music, silence, or sound design carries the idea.

9. **Virality Diagnosis**
   The AI explains why the post could spread: novelty, usefulness, identity signaling, controversy, social proof, trend fit, emotional resonance, clarity, copyability, or a strong payoff.

10. **Airtable Mapping**
    The AI turns the analysis into structured fields such as `Hook`, `Messaging`, `Emotion`, `Audience`, `Viral`, `Description`, `Visual`, `Pacing`, `Audio`, `Caption`, `Length`, and metrics.

The result is a content intelligence record: a reusable breakdown of why the post works, not just a saved link.

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
git clone https://github.com/Viviball/Viral-Content-Analyser.git
cd Viral-Content-Analyser
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

- **Instagram** — public posts are handled by Instaloader, which fetches views, video duration, exact likes, comments, caption, and cover image without any login. Private or login-gated posts fall back to authenticated browser context.
- **LinkedIn** — requires an authenticated browser session. Document posts route to `Carousel`; text-only posts route to `Text`.
- **X/Twitter** — may expose useful data through page state or oEmbed.
- **YouTube and articles** — metadata is usually available through Open Graph, Twitter cards, or JSON-LD without authentication.
- If metrics or duration are not available, the skill omits them instead of guessing.

The skill must not ask users for passwords, cookies, or browser session tokens.

## Repository Structure

| File | Purpose |
| --- | --- |
| `SKILL.md` | Main skill instructions: intake workflow, field schema, Instaloader extraction, Airtable routing, and viral analysis rules |
| `.env.example` | Airtable environment variable template — copy to `.env` and fill in your base ID, token, and table names or IDs |
| `.gitignore` | Keeps secrets, `.env`, and local state files out of Git |
| `CONTRIBUTING.md` | Contribution guidelines — how to write focused pull requests and what to include in the description |
| `SECURITY.md` | Secret-handling and platform-access guidance — what must never be committed and how to handle login-gated content |

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

- 🔒 Never commit Airtable tokens, cookies, passwords, or browser session data
- 🚧 Do not bypass login gates, privacy controls, or platform limits
- 🧾 Omit uncertain fields instead of inventing data
- 🧩 Use existing Airtable select options when fields are constrained
- 🧪 Test changes with a small number of records before bulk updates
