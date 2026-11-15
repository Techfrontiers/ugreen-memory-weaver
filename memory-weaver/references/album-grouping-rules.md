# Album Grouping & Rediscovery Rules

This reference defines how `memory-weaver` groups verified upstream media items returned by `photo-agent` and `file-agent` into coherent narrative chapters without inventing missing dates, places, or relationships.

## 1. Scope Normalization & Priority Signals

Rank clustering boundaries using real metadata observed from `ug-photo timeline-summary`, `ug-photo search-picture`, `ug-photo meta`, `ug-photo figure-albums`, and `ug-photo object-albums`:

1. **Temporal Continuity (Primary Split)**
   - **Short Burst / Single-Day Event**: Items captured within a continuous 24-hour window with consistent location or shared subject group form one chapter.
   - **Multi-Day Trip / Holiday Arc**: Consecutive days (with gaps of <= 2 quiet days) sharing a non-home city/region or consistent travel theme group into one trip collection, optionally subdivided by day or sub-location when > 40 items appear.
   - **Year-in-Review / Season Arc**: Broad 3-to-12-month queries group sequentially by calendar quarter or major life chapter (e.g., *Spring Garden Mornings*, *Autumn Mountain Trip*).
2. **Spatial Context (Secondary Split)**
   - Prefer EXIF/index `location` strings returned by `ug-photo meta` / `search-picture`.
   - Never guess a city or venue name when location metadata is absent unless clearly visible in verified `ug-photo vision-ocr` signage or explicitly named in user-provided notes via `ug-knowledge process-materials`.
3. **Human & Pet Anchors (Thematic Binding)**
   - Bind recurring subjects using real identifiers from `ug-photo figure-albums` (people) and `ug-photo object-albums` / `search-picture` (`pet-*`, `person-*`).
   - Do not assign personal names to unlabeled people unless explicitly supplied by the user or existing figure album metadata.

## 2. Deduplication Precedence (No Destructive Cleanup)

When `ug-filemgr scan-dedupe` detects identical or redundant copies inside the scoped directory:
- Retain the single **primary copy** in the story cluster using this priority order:
  1. Highest pixel resolution / bitrate reported by `ug-filemgr mediainfo` or `ug-photo meta`.
  2. File already living inside the user's organized destination hierarchy rather than an inbox or temp subfolder.
  3. Earliest or cleanest filename without copy suffixes.
- Record skipped duplicate paths in a non-destructive **Duplicate Notice** section of the approval preview and Markdown chronicle so the user may review them independently.

## 3. "Memory Echo" Lookback Strategy

When the user asks for nostalgia, anniversary echoes, "on this day", or broad life curation:
- Inspect the same calendar week or month across earlier years using `ug-photo search-picture` date ranges (`start-time`, `end-time`).
- Pair a historical highlight (e.g., 2, 3, or 5 years earlier) with recent photos sharing the same person, pet, or location tag to surface meaningful continuity ("Then & Now").
- Keep Memory Echo candidates clearly labeled by actual capture year and original path.

## 4. Album Type Selection Matrix

Choose the exact `ug-photo` album creation action during approved Stage 4 persistence:

| Story Pattern | Preferred `ug-photo` Action | Key Metadata Fields Populated |
| --- | --- | --- |
| Birthday, wedding, milestone, or dated family trip | `create-album-anniversary` | `album-name`, `commemorate-*`, `start-time`, `end-time`, `location`, `person-*` |
| General thematic memory (e.g., pets, cooking, weekend walks) | `create-album` or `create-album-custom` | `album-name`, verified `add-item` / `query` / `source-ref` |

## 5. Edge Cases & Size Thresholds

- **Too Few Matches (`1–2` items)**: Offer a single "Pocket Memory" caption card and optional tag; confirm with the user before creating a formal album for fewer than 3 items.
- **Large Archive (`> 150` items matched)**: Summarize full timeline distribution via `ug-photo timeline-summary`, cap `ug-photo vision-vlm` inspection to at most 12 representative highlight frames (first, peak burst, scenic/portrait variety, and finale), and avoid flooding NAS vision compute.
- **Zero Matches (`0` items)**: Terminate gracefully with zero album or file writes and present concrete filter relaxation options (wider dates, parent folder, broader keywords).
