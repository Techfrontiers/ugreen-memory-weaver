# Hackathon Disclosures, Privacy Model & Compliance Notes

## 1. Principal Functionality Built During the Hackathon

**Memory Weaver** (`memory-weaver`) is an original UGOS Pro Agent Skill built during the **UGREEN Hackathon 2026 on AI NAS: Bring Your Skills to UGOS** (`Understand Your Life` track). Its core functionality orchestrates three built-in NAS agents to transform raw personal photo, video, and optional journal archives into structured, emotionally resonant stories:

1. **Timeline & Contextual Discovery (`photo-agent`)**: Queries timeline summaries, people/pet recognition albums, multi-facet media search, EXIF/geo metadata, and cross-year "Memory Echo" lookbacks.
2. **Duplicate Hygiene & Media Inspection (`file-agent`)**: Detects duplicate assets cleanly via `scan-dedupe` to prevent cluttered albums, inspects stream technical metadata (`mediainfo`), and audits existing file tags (`tag-list`, `tag-list-file`) prior to tagging.
3. **Local Visual Understanding & Note Weaving (`photo-agent` + `knowledge-agent`)**: Runs local `vision-ocr` and `vision-vlm` inference strictly on curated shortlist frames while optionally reading user-referenced markdown/PDF/text notes (`process-materials`).
4. **Confirmation-Gated Persistence (`photo-agent`, `file-agent`, `knowledge-agent`)**: Pauses in `waiting_approval` via `ctx.ask` before creating albums (`create-album-anniversary`, `create-album`, `create-album-custom`), adding semantic file tags (`tag-add`, `tag-add-file`), or writing the Markdown Memory Chronicle (`save-text`).

## 2. Privacy & Safety Design Guarantees

- **Zero Cloud Egress**: All indexing, OCR, vision-language captioning, deduplication, and text generation execute on local NAS services through documented UGOS Pro agent delegations.
- **No Destructive Operations**: Duplicate candidates are excluded from curated story chapters and reported to the user; files are never deleted, moved, or overwritten destructively.
- **No Embedded Secrets or Code**: The Skill package contains pure Markdown guidance (`SKILL.md` and `references/*.md`) with zero executable scripts, binaries, external HTTP calls, or credentials.
- **Catalog Alignment**: Written against the official **Uliya Skill Development Standard (`v0.7.7`, Capability Catalog `v2.2.0`)** and validated with `scripts/check-skill.mjs` (`static-pass`).

## 3. Third-Party Components & Licenses

- **External Third-Party Libraries / Binaries / Models**: None bundled inside the Skill package.
- **Official Hackathon Framework Referenced**: UGREEN **Uliya Skill Development Kit (`v0.7.7`, Catalog `v2.2.0`)** specification and static checker used strictly as development/validation tooling per hackathon rules.

## 4. AI Tools Disclosure & Principal Uses

- **AI Coding & Authoring Assistants** (Antigravity / Gemini, Claude / GPT-family drafting workflows): Used during Skill specification design, English technical writing/editing, static schema compliance verification against `capability-catalog.json`, proposal tightening under the 500-word limit, and HTML/SVG architecture visual layout generation.
- **Runtime On-Device NAS AI**: Intended runtime execution relies solely on UGOS Pro's native built-in agents (`photo-agent`, `file-agent`, `knowledge-agent`) and local NAS Vision/VLM/OCR capabilities (`ug-photo vision-ocr`, `ug-photo vision-vlm`). All outputs undergo human author review and automated `check-skill.mjs` validation.
