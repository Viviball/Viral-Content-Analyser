---
name: content-reference-agent
description: Automates Airtable content-reference records from social media post links and article URLs. Use this skill whenever the user gives an AI a social media post, video, carousel, text post, article, newsletter, blog post, or any URL and asks to save, log, analyze, or add it as content inspiration. Detects whether the reference is Video, Carousel, Text, or Article; extracts metadata and cover imagery; writes viral analysis around hook, messaging, emotions, and target audience; then creates the correct Airtable record using the configured content-reference schema.
compatibility: "Requires browser/web fetch access, Airtable API access, and Claude-in-Chrome or an authenticated browser for LinkedIn/Instagram."
---

# Content Reference Agent

Content inspiration intake workflow. For every shared URL, collect the source, analyze why it works, then create an Airtable record in the table that matches the content type.

Do not just summarize the link. The goal is to let any AI turn a social media or article link into structured Airtable data automatically: hook, emotional driver, viral mechanism, audience, platform context, creator, cover image, metrics, duration, and source URL.

## Operating Model

When the user gives any AI a social media post link or article URL, the AI should use this skill to automate the Airtable entry end to end. Do not ask the user to confirm before saving when Airtable credentials and table configuration are available; create the record automatically, then report what was saved.

1. Read and normalize the URL.
2. Detect the content type: `Video`, `Carousel`, `Text`, or `Article`.
3. Extract visible metadata, media signals, cover imagery, metrics, caption, creator, date, and duration when available.
4. Generate the strategy fields for Airtable: `Hook`, `Messaging`, `Emotion`, `Audience`, `Viral`, `Description`, `Visual`, `Pacing`, and `Audio` where relevant.
5. Create the Airtable record in the matching table automatically.
6. Confirm what was saved and briefly note any missing or inferred fields.

## Required Session Config

Ask once per session for any missing values, then keep them in memory for the rest of the session. Never print, log, or repeat private tokens.

- `AIRTABLE_API_TOKEN`
- `AIRTABLE_BASE_ID`
- `AIRTABLE_VIDEO_TABLE` - table ID or exact table name for Video references
- `AIRTABLE_CAROUSEL_TABLE` - table ID or exact table name for Carousel references
- `AIRTABLE_TEXT_TABLE` - table ID or exact table name for Text references
- `AIRTABLE_ARTICLE_TABLE` - table ID or exact table name for Article references

If the user has not provided table IDs yet, ask for only the missing table names or IDs before pushing to Airtable. You may still analyze the content and save a draft payload while waiting. Once the required Airtable configuration exists, do not ask for save confirmation.

## Content Type Routing

Detect one primary content type and use it to choose the Airtable table.

| Content Type | Route To | Detection Signals |
| --- | --- | --- |
| `Video` | `AIRTABLE_VIDEO_TABLE` | YouTube, TikTok, Reels, Shorts, video player, motion thumbnail, duration, transcript/captions, video-first post |
| `Carousel` | `AIRTABLE_CAROUSEL_TABLE` | Multi-image post, slide count, LinkedIn document post, Instagram carousel, swipe language, multiple panels |
| `Text` | `AIRTABLE_TEXT_TABLE` | LinkedIn/X/Threads-style text post, primarily copy-led, no article page, no meaningful visual sequence |
| `Article` | `AIRTABLE_ARTICLE_TABLE` | Blog, newsletter, publication, long-form web page, article schema, byline, read time, headline/body content |

When a post has mixed media, route by the main consumption mode. Examples: a LinkedIn document post is `Carousel`; a YouTube link with a long description is still `Video`; a blog post with embedded video is `Article` unless the video is clearly the reference.

## Field Schema

Populate the current Airtable fields below. Use exact field names.

- `URL` - original source URL.
- `Hook` - the opening line, visual opener, title promise, or first-frame idea. If no explicit hook exists, infer the strongest hook in plain language.
- `Emotion` - concise emotional driver(s), such as curiosity, relief, aspiration, urgency, disbelief, trust, envy, belonging, fear of missing out, validation, or delight.
- `Viral` - why this could spread. Focus on shareability, tension, novelty, identity signal, clarity, payoff, trend fit, social proof, controversy, usefulness, or timing.
- `Description` - short neutral description of what the content is and what happens in it.
- `Platform` - Instagram, LinkedIn, TikTok, YouTube, X/Twitter, Threads, Substack, Blog, Newsletter, Website, or Unknown.
- `Creator` - creator, channel, author, publication, or account name.
- `Date` - published date if visible; otherwise today's date in `YYYY-MM-DD`.
- `Cover` - Airtable attachment array using the best image URL: `[{"url":"https://..."}]`. Leave blank only if no usable image can be found.
- `Type` - one of `Video`, `Carousel`, `Text`, `Article` if this field exists in the target table.
- `Messaging` - compact analysis of the core message, angle, promise, and proof.
- `Target Audience` - who it is for, including job-to-be-done or identity when clear.
- `Engagement` - visible metrics such as likes, comments, reposts, views, or saves.
- `Notes` - optional extra context, caveats, or missing-data notes.
- `Audience` / `audience` - table-specific audience analysis. Use the exact case from the Airtable table.
- `Comments` / `comments` - visible comment count. Do not invent; leave blank when not visible.
- `Visual` / `visual` - concrete visual system: first frame, shot type, composition, pacing, color, overlays, editing pattern, proof shown on screen.
- `Audio` / `audio` - voiceover/music/original audio, sound design, spoken hook, pacing, silence, and whether audio carries the idea.
- `Pacing` / `pacing` - visible pacing or editing rhythm if the table has this field.
- `Caption` / `caption` - exact visible caption or a concise reconstruction from metadata. Preserve hashtags when relevant.
- `Length` / `length` - video duration only when confidently known. For Airtable duration or numeric fields, send a plain number, never a formatted string such as `0:42` or `1:23`. Before writing, inspect or remember the Airtable field format. If the field is a normal seconds-aware duration/numeric field, send seconds. If the Airtable duration field uses `durationFormat: "h:mm"`, raw short-form seconds will display as `0:00` or `0:01`; multiply short-form seconds by `60` so Airtable displays them as `0:05`, `0:15`, `0:30`, etc. If duration is not visible or confidently available from metadata, omit it rather than guessing.

If Airtable returns `UNKNOWN_FIELD_NAME`, remove only the unsupported field from the payload and retry once. Keep the fields above as the canonical schema.

When automatically updating existing Airtable records, use AI to refresh the analysis fields according to the current content and available metadata. For Airtable select and multi-select fields, including `Visual` / `visual`, `Pacing` / `pacing`, and `Audio` / `audio` when they are select-type fields, only use options that already exist in the table. Each selected option must be no more than two words. If a better value is not available as an existing option, omit that field and add the proposed value to `Notes`. For free-text versions of `Visual`, `Pacing`, and `Audio`, write normal concise analysis.

## Intake Workflow

### 1. Normalize URLs

For each link:

1. Keep the original URL in `URL`.
2. Remove obvious tracking parameters only when they do not affect access (`utm_*`, `fbclid`, `gclid`, etc.).
3. Preserve platform-specific IDs, share tokens, and post parameters.
4. Process multiple links one at a time and save each Airtable record automatically when required configuration is available.

### 2. Fetch Page Metadata

Use web fetch or browser tools to visit the URL. Extract:

- Title/headline
- Caption or body excerpt
- Creator/author/channel/account/publication
- Published date
- Platform
- Engagement metrics
- Media signals: video, carousel, text post, article
- Page description
- Cover image candidate

### 3. Extract Cover From Meta Tags

Prefer Open Graph and social meta tags before screenshots.

Look for these in order:

1. `<meta property="og:image:secure_url" content="...">`
2. `<meta property="og:image" content="...">`
3. `<meta name="twitter:image" content="...">`
4. `<meta property="twitter:image" content="...">`
5. `<meta name="thumbnail" content="...">`
6. JSON-LD `image`, `thumbnailUrl`, or `primaryImageOfPage`
7. Platform thumbnail visible in the authenticated browser
8. Browser screenshot only if no URL image is available and the tool supports uploading/hosting it

Resolve relative image URLs against the page URL. Use a direct, publicly reachable HTTPS image URL for Airtable `Cover` whenever possible:

```json
{
  "Cover": [
    {
      "url": "https://example.com/cover.jpg"
    }
  ]
}
```

Do not invent a cover URL. If none is available, leave `Cover` blank and mention that in `Notes`.

### 4. Use Claude-in-Chrome For LinkedIn And Instagram

LinkedIn and Instagram often hide data from unauthenticated fetches. For these:

1. Open/read the post through Claude-in-Chrome or the available authenticated browser session.
2. Use the user's logged-in page view to capture visible text, creator, date, media type, engagement, slide count, and cover/thumbnail.
3. Do not ask the user for cookies, session tokens, passwords, or browser storage.
4. Do not bypass privacy gates. If the authenticated browser cannot access the post, ask the user for a screenshot, pasted caption, or creator/date details.

For LinkedIn document posts, inspect whether it is a slide/document carousel and route to `Carousel`. For LinkedIn text-only posts, route to `Text`. For Instagram reels, route to `Video`; Instagram multi-image posts route to `Carousel`.

For Instagram metrics and video length, try these sources before leaving `Length` blank:

1. Page metadata, especially `<meta name="description">`, for visible likes, comments, creator, caption, and date.
2. Embedded page JSON or GraphQL/Relay payloads for `video_duration`, `video_versions`, `play_count`, `view_count`, and media resources.
3. Authenticated browser page state when public metadata is incomplete.
4. Instaloader when available: extract the shortcode from the Reel URL, then use `Post.from_shortcode(context, shortcode)` and read `post.video_duration`, `post.video_url`, `post.video_play_count`, `post.video_view_count`, `post.likes`, `post.comments`, `post.caption`, `post.date_utc`, and `post.owner_username`.
5. Instagrapi only when the user provides an approved authenticated Instagram account/session. Use `media_pk_from_url()` then `media_info()` to retrieve fields such as `comment_count`, `like_count`, `view_count`, `play_count`, `video_duration`, `video_url`, and carousel `resources`.
6. If a legitimate direct video URL or downloaded video file is available, use media metadata tools such as `ffprobe` to read duration and write the duration in seconds to Airtable `Length`.

Length handling rules:

- Airtable duration fields are format-sensitive. Fetch or infer the `Length` field metadata before writing when possible. Some Video tables use `Length` as `type: "duration"` with `options.durationFormat: "h:mm"`, which hides seconds in the UI.
- For a seconds-aware duration/numeric field, convert visible `M:SS` or `H:MM:SS` manually before saving: `0:42` -> `42`, `1:23` -> `83`, `1:02:03` -> `3723`.
- For an `h:mm` Video `Length` field, convert confirmed short-form clip seconds into the table's visible minute display by multiplying by `60`: `5` -> `300` so Airtable displays `0:05`; `15` -> `900` so it displays `0:15`; `30` -> `1800` so it displays `0:30`. Do this only for an explicitly confirmed minutes-only duration field.
- Treat Instagram `video_duration` and Instaloader `post.video_duration` as seconds. Preserve decimal seconds until the final payload, then round to the nearest whole second for Airtable duration or numeric fields. Do not divide by 60.
- If a scraped value is `0`, `1`, less than `2`, or displays in Airtable as `0:00` or `0:01`, assume duration extraction failed unless the player visibly confirms the clip is actually under two seconds. Clear or omit `Length` and retry from a better source.
- Do not coerce a time string with a numeric parser. Values like `0:35` parsed as `0` or `1:08` parsed as `1` are invalid and must not be saved.
- If multiple duration candidates disagree by more than two seconds, prefer direct media metadata from `ffprobe`, then Instaloader or platform API metadata, then visible browser/player duration. If still uncertain, omit `Length` and note that duration was unavailable.

Do not bypass login, privacy gates, or platform limits. If duration is unavailable from metadata or legitimate video-file probing, omit `Length` rather than guessing. Do not populate share count fields; this skill no longer tracks `Shares`.

### 5. Analyze Why It Went Viral

Use an adapted Viral Clarity model for video references. Its useful principle is: do not summarize; diagnose the hook, retention mechanics, audience psychology, and reusable pattern. Viral Clarity is strongest for short-form videos with transcripts because it maps hooks, retention beats, and rewrites. For this skill, extend it beyond transcript analysis so it also works from thumbnails, captions, OG metadata, visible frames, and authenticated browser context.

For video posts, always capture:

- Hook type: curiosity, contrast, authority, challenge, story, transformation, visual spectacle, proof, identity, or tutorial promise.
- First-frame job: what stops the scroll before the viewer understands the full context.
- Curiosity gap: what question the viewer needs answered.
- Retention beats: the sequence that keeps the viewer watching, such as setup, proof, reveal, transformation, checklist, or payoff.
- Reusable template: the repeatable pattern a creator can copy.
- Audience psychology: who feels personally addressed and why.

Write the analysis for reuse by a content strategist. Be specific and avoid generic praise.

Capture:

- `Hook`: what stops the scroll or creates the initial promise.
- `Messaging`: use concise tags when Airtable field is a multi-select; use a sentence only when the field is text.
- `Emotion`: the feeling that makes someone continue, save, comment, or share.
- `Target Audience`: who feels personally addressed.
- `Audience` / `audience`: more practical audience analysis for the current table: creator type, viewer desire, and why the reference matters to them.
- `Visual` / `visual`: what the viewer sees and how the visual system creates attention or credibility.
- `Audio` / `audio`: what the viewer hears and whether the audio creates momentum, explanation, mood, or trend fit.
- `Caption` / `caption`: the visible text/caption and how it frames the post.
- `Length` / `length`: confirmed video duration normalized for the target Airtable field format. Use seconds for seconds-aware duration/numeric fields. For an `h:mm` duration field, multiply confirmed short-form seconds by `60` before saving so Airtable does not display `0:00` or `0:01`. Omit if duration is unknown or if extraction produced an implausible `0`, `1`, `0:00`, or `0:01` value.
- `Viral`: the spread mechanism - why someone would forward it, save it, argue with it, copy it, or remember it. Use a "Why viral" framing and do not prefix the field with "Viral Pattern".

Good viral analysis names the mechanism:

- "Contrarian hook reframes a common AI fear into an advantage for solo creators."
- "Before/after payoff makes the transformation instantly legible and saveable."
- "Identity-led message validates overwhelmed founders and gives them language to share."
- "Specific numbers create credibility; the simple checklist makes it easy to reuse."
- "First-frame proof shows the output before process, then the caption turns it into a copyable workflow."

Avoid vague lines such as "great content", "very engaging", or "good visuals".

## Push To Airtable

Choose the target table from `Content Type Routing`, then create the record.

Saving is automatic. If the user asks to analyze, save, log, add, or process a link and Airtable configuration is available, create the Airtable record after extraction and analysis without asking for a separate confirmation.

Use table IDs when possible. If using table names, URL-encode the table name.

```bash
curl -sS -X POST "https://api.airtable.com/v0/$AIRTABLE_BASE_ID/$TARGET_TABLE" \
  -H "Authorization: Bearer $AIRTABLE_API_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "fields": {
      "URL": "<url>",
      "Hook": "<hook>",
      "Emotion": "<emotion>",
      "Viral": "<why it spread or could spread>",
      "Description": "<description>",
      "Platform": "<platform>",
      "Creator": "<creator>",
      "Date": "<YYYY-MM-DD>",
      "Cover": [{"url": "<cover image url>"}],
      "Type": "<Video|Carousel|Text|Article>",
      "Messaging": "<messaging analysis>",
      "Target Audience": "<audience>",
      "Engagement": "<visible metrics>",
      "Notes": "<optional notes>"
    }
  }'
```

Omit blank optional fields rather than sending empty strings unless Airtable requires the field. For `Cover`, omit the field entirely when no valid image URL is available.

If the Airtable API returns an error:

- Show the exact Airtable error message, excluding tokens.
- If the error is an unknown field, remove that field and retry once.
- If the error is an invalid option, preserve the user's data in `Notes` and ask whether to add/update Airtable select options.
- If credentials or table config are missing, ask only for the missing value.

## Save Summary

After a successful Airtable save, respond compactly:

```text
Saved to Content Library.

Type: Video
Platform: Instagram
Creator: @creator
Hook: ...
Emotion: ...
Viral: ...
Cover: found from og:image
Airtable: saved to Video
```

Mention missing or inferred fields briefly. Do not include private tokens or raw API responses unless there was an error.

## Edge Cases

- Private/login-gated content: use Claude-in-Chrome/authenticated browser first. If inaccessible, ask for pasted text or screenshots.
- No OG image: try Twitter tags, JSON-LD, visible thumbnail, then leave `Cover` blank.
- Article with no published date: use today's date and add `Published date not visible` to `Notes`.
- Multiple links: process sequentially; do not combine them into one record.
- Ambiguous format: route by the main reason the user saved it, then explain the inference in `Notes`.
- Duplicate in Airtable: if duplicate checking is available in the table, update the existing record only if the user asked for deduplication; otherwise create a new record.
