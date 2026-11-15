# Local Vision Prompts, Story Captions & Chronicle Layout

This reference standardizes how `memory-weaver` prompts `photo-agent` visual analysis (`ug-photo vision-ocr` and `ug-photo vision-vlm`) and formats narrative captions plus the exported Markdown Memory Chronicle (`ug-knowledge save-text`).

## 1. Local Vision & OCR Guardrails

1. **Selective Invocation**: Run `ug-photo vision-vlm` and `ug-photo vision-ocr` only on the curated shortlist of deduplicated highlight images from Stage 2 (recommended <= 12 images per run).
2. **Compact Generation Bounds**: Keep `max-tokens` conservative (e.g., `120`–`180`) and temperature low (`0.2`–`0.4`) so `vision-vlm` returns concise, grounded scene observations on local NAS hardware without hallucinated lore.
3. **Ground-Truth Discipline**:
   - Captions may weave together **only** observed VLM visual cues (subject activity, lighting, mood, attire, setting), OCR signage/tickets, EXIF date/place (`ug-photo meta`), figure/pet labels, and optional user journal excerpts (`ug-knowledge process-materials`).
   - Never fabricate unverified identities, weather conditions not visible in the frame, or historical trivia absent from upstream results.

### Recommended `vision-vlm` Prompt Patterns

- **Narrative Scene Prompt**:
  `"Describe this personal photo in 2 concise sentences: highlight the main people/pets or activity, setting, lighting/mood, and notable foreground details without inventing names or locations."`
- **Travel / Signage Context Prompt** (paired with `vision-ocr`):
  `"Summarize what moment or place activity is shown in this frame and note any readable landmark features or food/object details concisely."`

## 2. Story Caption Tone & Structure

Keep each highlight caption warm, vivid, and concise (25–55 words):
- **Headline**: Short evocative title (4–7 words) plus exact observed date (`YYYY-MM-DD`) and location (if present).
- **Body**: 1–2 grounded sentences connecting what is seen in the moment with who was there or why it stood out.
- **Provenance Tag**: Cite the verified filename/path so the user can jump straight to the source asset on the NAS.

Example caption structure (using illustrative sample values only):

> **Golden Hour on the Harbor Pier — 2024-11-08 (Kobe Harbor)**
> Soft late-afternoon sunlight catches the waterfront promenade beside two steaming paper cups and a smiling golden retriever looking toward the ferries. Read from harbor signage via local OCR and matched to the Autumn Harbor trip chapter.
> *Source item*: `Autumn-Trip-Highlight-04.jpg`

## 3. Markdown Memory Chronicle Template (`ug-knowledge save-text`)

When Stage 5 runs with user approval, generate a clean UTF-8 Markdown file (`memory-chronicle-<theme-slug>.md`) matching this layout:

```markdown
# Memory Chronicle: <Curated Theme Title>

- **Generated**: <ISO Date>
- **Scope**: <User-requested scope / date window / directory summary>
- **Curated Chapters**: <N albums / chapters> | **Primary Items**: <M items> | **Duplicates Skipped**: <K items>
- **Privacy Guarantee**: Processed locally on UGREEN AI NAS via UGOS Pro agents (`photo-agent`, `file-agent`, `knowledge-agent`).

---

## Executive Story Summary

<1 concise paragraph (60–100 words) weaving the arc of the moments, key people/pets, locations, and any personal journal notes discovered.>

## Memory Echo (Then & Now)

<Present only when matching earlier-year memories were discovered; otherwise omit cleanly.>
- **<Earlier Year Date>**: <Verified historical photo highlight + album/file reference> paired with **<Recent Date>**.

## Narrative Chapters

### Chapter 1: <Chapter Name> (<Date Span> · <Verified Location>)
- **Created / Target Album**: `<Album Name>` (`<Album ID if created>`)
- **Semantic Tags Applied**: `<Tag List if approved>`
- **Highlight Moments**:
  1. **<Moment Title>** (`<Filename>`) — <Grounded 1–2 sentence caption>.
  2. **<Moment Title>** (`<Filename>`) — <Grounded 1–2 sentence caption>.

## Duplicate & Asset Hygiene Notes

- <Number of exact duplicate copies excluded from albums (retained untouched on NAS)>
- <Optional video resolution/codec summary from mediainfo>

## Source Index

| Chapter | Filename / Path | Capture Timestamp | Primary Tag / Album |
| --- | --- | --- | --- |
| 1 | `...` | `...` | `...` |
```
