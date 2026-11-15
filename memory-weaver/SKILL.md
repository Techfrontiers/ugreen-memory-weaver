---
name: memory-weaver
description: Local-first personal memory curator and story weaver for UGREEN AI NAS (UGOS Pro). Use when the user asks to organize photos or videos into themed memory albums, rediscover forgotten highlights from past years, apply semantic memory tags, combine personal notes with visual moments, or generate a warm Markdown memory chronicle.
---

# Goal

Turn scattered family photos, travel shots, pet moments, and optional personal notes stored on the user's UGREEN AI NAS into curated memory albums, semantic file tags, "Memory Echo" rediscoveries, and a private Markdown memory chronicle — running completely on local NAS intelligence with explicit user confirmation before every persistent write.

Read the supporting specification files alongside this skill:
- Clustering and lookback rules: [album-grouping-rules.md](references/album-grouping-rules.md)
- Local vision prompts, narrative captions, and chronicle layout: [story-caption-templates.md](references/story-caption-templates.md)
- Hackathon disclosures, privacy model, and compliance notes: [disclosures-and-compliance.md](references/disclosures-and-compliance.md)

# Required input

- **Scope or theme**: A natural-language request specifying a target folder path (optional if searching globally across indexed photos), date window, person, pet, location, event name, or rediscovery request.
- **Output preference** (optional, defaults applied cleanly): Whether to create photo albums, apply file manager tags, weave optional journal/itinerary notes, and save a Markdown Memory Chronicle in a user-chosen destination directory.

If no timeframe, location, person, or directory scope can be inferred at all, return `waiting_user` and ask the user for at least one discovery anchor before running searches.

# Generation-time capability evidence

- `ug-photo` → `photo-agent`
- `ug-filemgr` → `file-agent`
- `ug-knowledge` → `knowledge-agent`

# Workflow

Execute the following stages in order. Each downstream delegation must consume the real result returned by its upstream step; never invent media paths, album identifiers, counts, dates, locations, or captions.

## Stage 1 — Visual Discovery, Timeline Context & Optional Note Ingestion

Query the local photo index first to locate matching scenes, timeline distributions, recognized people/pet albums, and anniversary lookback ("Memory Echo") candidates following [album-grouping-rules.md](references/album-grouping-rules.md):

delegate --target photo-agent --instruction 'Discover candidate memories for the user request using ug-photo timeline-summary, ug-photo search-picture, ug-photo figure-albums, ug-photo object-albums, and ug-photo albums. Filter strictly by the user-provided directory path, date range, person, pet, or location if given; also check historical matching windows across prior years for Memory Echo rediscovery when requested. For top candidate items, inspect ug-photo meta to collect verified timestamps, file paths, and location metadata. Return structured JSON with candidate clusters, item paths, album matches, and timeline totals, or an explicit empty result if zero matches are found.'

If the user also referenced personal journal notes, trip itineraries, or text documents in a folder, extract relevant context in parallel or immediately after:

delegate --target knowledge-agent --instruction 'Read and summarize only the user-referenced personal note or itinerary files using ug-knowledge process-materials with the explicit file path, extension filter, and user query so event names and dates can enrich upstream photo clusters. Return real extracted facts and source paths, or report zero matches if no note files exist.'

**Zero-match guard**: If `photo-agent` discovery returns zero matches or an empty result (0 photos/videos found for the requested scope), halt downstream stages cleanly, report that no matching media items were found without creating empty albums or files, ask the user whether to broaden the date range, location, or folder scope, and finish with `completed` (or `waiting_user` if awaiting adjusted scope).

## Stage 2 — Deduplication, Media Metadata & Tag Hygiene

Pass the verified upstream item paths from Stage 1 into `file-agent` to eliminate exact duplicates from candidate clusters, enrich video/photo stream metadata, and inspect existing tags before proposing any new tag names:

delegate --target file-agent --instruction 'Inspect the verified upstream candidate paths from Stage 1. Run ug-filemgr scan-dedupe on the scoped folders to identify duplicate copies so only the highest-resolution primary copy per duplicate group enters story clusters. Inspect video and key asset streams via ug-filemgr mediainfo, and list existing tags via ug-filemgr tag-list and ug-filemgr tag-list-file. Return deduplicated primary paths, duplicate savings summary, media specs, and existing tag names.'

## Stage 3 — Local Vision Captioning & Text Extraction

Select a balanced representative sample (up to 12 highlight frames across chapters, plus key signs/tickets/menus when relevant) from the deduplicated upstream paths — never scanning bulk archives redundantly — and analyze them locally on the NAS using prompts from [story-caption-templates.md](references/story-caption-templates.md):

delegate --target photo-agent --instruction 'Run local vision understanding strictly on the curated representative shortlist of upstream primary file paths from Stage 2. Call ug-photo vision-ocr on photos likely containing signage, tickets, dates, or menus, and call ug-photo vision-vlm with concise scene, mood, subject, and storytelling prompts bounded by modest max-tokens. Return verified per-file visual observations, OCR snippets, and short memory captions anchored only to observed details.'

## Stage 4 — Preview & Approval-Gated Album + Tag Persistence

Synthesize the real upstream outputs from Stages 1–3 according to [album-grouping-rules.md](references/album-grouping-rules.md) and [story-caption-templates.md](references/story-caption-templates.md), then pause in `waiting_approval` via `ctx.ask` to present the complete preview to the user before touching persistent storage:
- Proposed album name(s), album type (`anniversary` vs `custom`/`standard`), item counts, and date spans;
- Highlight captions and optional "Memory Echo" rediscoveries;
- Proposed semantic tags (reusing existing tags whenever matched) and target primary file paths;
- Duplicate assets excluded (kept safe in place — never deleted automatically);
- Target output path for the optional Markdown Memory Chronicle.

Wait for explicit user confirmation (`ctx.ask`). If the user rejects or cancels, stop immediately with `cancelled`. Only for the approved items:

delegate --target photo-agent --instruction 'Create only the user-approved photo albums from the confirmed upstream Stage 2 primary item lists. Use ug-photo create-album-anniversary for milestone, family-anniversary, or dated trip chapters with matching start-time, end-time, commemorate metadata, location, and album-name; use ug-photo create-album or ug-photo create-album-custom for thematic memory collections. Return created album names, identifiers, and actual item counts without inventing unconfirmed albums.'

If the user also approved file tagging:

delegate --target file-agent --instruction 'Apply only the user-approved semantic memory tags to confirmed upstream primary file paths. First check ug-filemgr tag-get-by-name for each proposed tag name; create missing tags with ug-filemgr tag-add only when absent, then attach each tag-id via ug-filemgr tag-add-file to the confirmed paths. Return tagged file counts and tag identifiers.'

## Stage 5 — Private Markdown Memory Chronicle Export

If the user confirmed chronicle export in Stage 4, pass all real upstream results (created albums, verified file paths, OCR/VLM observations, note highlights, duplicate exclusions, and tag counts) to write a clean Markdown chronicle into the user-confirmed destination directory:

delegate --target knowledge-agent --instruction 'Assemble and save the final Markdown Memory Chronicle built strictly from verified upstream outputs of Stages 1 through 4 and matching the Markdown layout template. Call ug-knowledge save-text with the user-confirmed destination path, descriptive filename-hint ending in .md, and final chronicle content containing chapter summaries, story captions, rediscovery highlights, album summary table, and source path index. Return the saved chronicle path and confirmation.'

# Completion and failure

Return structured `delegation_result` status handoffs aligned with [disclosures-and-compliance.md](references/disclosures-and-compliance.md):
- `completed`: Zero-match query handled gracefully without side effects, OR user-approved albums, tags, and/or Markdown chronicle were persisted and summarized exclusively from verified upstream outputs.
- `waiting_user`: Initial prompt lacked any timeframe, person, location, theme, or directory anchor, or user asked to adjust discovery filters after an empty result.
- `waiting_approval`: Paused before Stage 4 / Stage 5 persistent writes (`create-album*`, `tag-add*`, `save-text`) so the user can review and approve exact album names, tags, and destination paths via `ctx.ask`.
- `cancelled`: User declined persistent album creation, tagging, or chronicle export during approval preview; zero filesystem or album changes performed.
- `failed`: An upstream agent reported an unrecoverable error (for example, offline photo service reported by `ug-photo health`, invalid/missing directory path, or write denial); report the exact failing stage and upstream reason without fabricating partial success.
