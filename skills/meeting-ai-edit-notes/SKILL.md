---
name: meeting-ai-edit-notes
description: Use when the user wants to edit, rewrite, tighten, or restructure the notes of a Meeting.ai meeting, rename a meeting, or combine several meetings into one summary, weekly digest, or project recap and save it back into Meeting.ai.
---

# Edit notes and combine meetings

> **Requires the Meeting.ai MCP server.** This skill calls its tools. If `workspace_list` is not
> available in this session, stop and follow the `meeting-ai-setup` skill. Do not guess or invent
> results. Tool names can carry a prefix, for example
> `mcp__plugin_meeting-ai_meeting-ai__workspace_list` in Claude Code.

Meeting.ai writes the first draft of every meeting's notes. Through Claude the user can
improve one meeting's notes, or pull several meetings together into one summary. Edits are
versioned like a web edit, so nothing is lost.

## Rewriting one meeting's notes

1. Call `meetings_notes` to get the sections. Each section has an `id`, a `title`, and its
   current `content`. Check the user's `role`: editing is **owner only**. If the user is not
   the owner, say so and offer to draft the text for them to paste instead.
2. Read the transcript before rewriting. Notes are a summary, and rewriting a summary from a
   summary loses detail. Use `meetings_transcript_search` when the user names a topic, or
   page through `meetings_transcript` until `next_cursor` is `null` for a full rewrite.
3. Draft the new text and show it to the user. Confirm before saving.
4. Call `meetings_update_notes` with `sections: [{ id, text }]` for each section you are
   replacing. Pass `regenerate_visual_note: false` unless the user wants the meeting's
   visual note redrawn too, because redrawing spends coins.
5. To rename the meeting, pass `title` in the same call. A title-only change never redraws.

An unknown section id edits nothing and returns the valid ids, so re-read and retry once.

## Combining several meetings into one summary

This is the workflow for "summarize this week's meetings", "recap the Acme project", or
"what did we decide across the three planning sessions".

1. Find the meetings with `meetings_search`. Use a keyword for a project or client, or a
   `start_date` and `end_date` for a period. Confirm the list with the user.
2. Read each meeting with `meetings_notes`. For decisions, numbers, or quotes, check the
   transcript with `meetings_transcript_search` so the combined summary reflects what was
   actually said.
3. Write the combined summary. A good shape is: what happened, decisions made, open
   questions, and action items with owners, each item naming the meeting it came from.
4. Reply with the recap in chat. That is the deliverable.
5. If the user wants a file instead, use `meetings_export` on the individual meetings.

Never write a combined recap into one of the source meetings. Each meeting's notes are the
record of that meeting, and overwriting a section with a recap of other meetings destroys
that record. The recap belongs in the reply, not inside a meeting.

## Sharing the result

`meetings_share` turns on a public link for one meeting. The link shows the notes and, by
default, the transcript. Tell the user this before you share. It is PIN-protected by default;
only pass `require_pin: false` when the user asks for an open link. Removing an existing PIN
also requires `remove_existing_pin: true` and the owner's explicit request. Sharing by email
is done in the web app. When the reply has a `pin`, give it to the user with the link.
