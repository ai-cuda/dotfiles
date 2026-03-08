---
name: meeting-notes-processor
description: Processes raw meeting transcripts (Gemini, Zoom, Teams, or any speaker-labeled text) into structured markdown summaries saved locally. Use this skill whenever the user pastes a block of meeting transcript text, says "process these notes", "summarize this meeting", "extract action items", "create meeting summary", or shares any text that looks like a conversation with speaker names. Trigger even if the user just pastes a transcript without explanation — the intent is clear. Also trigger for follow-ups like "what were the action items from that meeting?" if a transcript is in context.
---

# Meeting Notes Processor

You turn raw meeting transcripts into clean, structured markdown files saved to `~/meeting-notes/`.

## Step 1: Extract metadata

Before writing anything, pull out:

- **Meeting title** — infer from the topics discussed if not stated explicitly (e.g., "Q2 Planning Sync", "Product Roadmap Review"). If truly unclear, use "Untitled Meeting".
- **Date** — look for it in the transcript header or body. If absent, ask the user once before proceeding.
- **Participants** — collect every unique speaker name from the transcript.

## Step 2: Generate the markdown file

Use this exact structure:

```
# [Meeting Title]
**Date:** YYYY-MM-DD
**Participants:** Name1, Name2, Name3, ...

---

## Executive Summary
3–5 bullet points capturing the purpose, key outcomes, and overall direction decided. Write for someone who wasn't in the meeting and has 30 seconds to read this.

---

## Key Topics Discussed
- Topic 1 — one sentence on what was covered
- Topic 2 — one sentence on what was covered
(aim for 4–8 topics)

---

## Decisions Made
- [Decision]: brief explanation of what was decided and why (if stated)
(if no explicit decisions were made, write: *No formal decisions recorded.*)

---

## Action Items

### [Person Name]
- [ ] Action item description *(due: date if mentioned, otherwise omit)*

### [Another Person]
- [ ] Action item description

### Unassigned
- [ ] Action item that wasn't clearly attributed to anyone
(omit this section entirely if everything is assigned)

---

## Open Questions
- Question that came up but wasn't resolved
(if none, omit this section entirely)
```

## Step 3: Save the file

1. Create `~/meeting-notes/` if it doesn't exist.
2. Name the file: `YYYY-MM-DD-meeting-title-slugified.md`
   - Slugify the title: lowercase, spaces → hyphens, remove special characters
   - Example: "Q2 Planning Sync" → `2025-04-15-q2-planning-sync.md`
3. Write the file using the Write tool.
4. Tell the user: the full file path, and a quick count (e.g., "5 action items across 3 people").

## Guidelines for good output

**Executive summary**: Don't just list what was discussed — capture the *so what*. "The team decided to delay the v2 launch to Q3 to allow time for user testing" is better than "The launch timeline was discussed."

**Action items**: Be specific and actionable. "John to send updated spec to design team by Friday" not "John: spec". If a follow-up was implied but not explicitly assigned, include it as Unassigned with a note like *(implied from discussion)*.

**Speaker attribution**: Use the names exactly as they appear in the transcript. Don't normalize or guess full names unless the transcript makes it obvious.

**Decisions vs. action items**: A decision is something the group agreed on ("We're going with vendor A"). An action item is something a person needs to do ("Sarah to draft vendor contract"). Keep them separate.

**Tone**: The summary should be professional and neutral — avoid editorializing.

## Interacting with past notes

When the user wants to look back at previous meetings (e.g., "what did we decide about X?", "show me Sarah's open action items"), use the Read and Glob tools to search through `~/meeting-notes/`. All files follow the same structure so they're easy to scan.
