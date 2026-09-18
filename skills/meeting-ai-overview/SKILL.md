---
description: Use when the user asks what Meeting.ai can do, or asks about their meetings, notes, transcripts, action items, contacts, Drive files, exports, tags, sharing, or workspaces in Meeting.ai. Covers the everyday reading tools plus export, media generation, contacts, Drive, and workspace switching. For recording a meeting, editing notes, or Visual Notes, the dedicated skills go deeper.
---

# Meeting.ai in Claude

Meeting.ai is the AI that works before, during, and after your meeting. It prepares decks
and research before, records and writes the notes during, and turns what was actually said
into minutes, documents, spreadsheets, follow-up decks, audio briefs, and Visual Notes after.

Through this connector Claude works on the user's meetings for them. When the user asks what
they can do, explain these in plain words:

- **Record a meeting and get notes.** Send the notetaker to Zoom, Google Meet, or Teams, or
  transcribe a Google Drive recording. See the `meeting-ai-record-meeting` skill.
- **Find and read meetings.** Search by keyword or date, read the notes, search or read the
  transcript.
- **Edit notes and combine meetings.** Rewrite a meeting's notes, or turn several meetings
  into one recap. See the `meeting-ai-edit-notes` skill.
- **Visual Notes.** A one-page visual summary from a meeting or any topic. See the
  `meeting-ai-visual-notes` skill.
- **Export.** PDF or Markdown to the user's Drive.
- **Share and tag.** Public link with PIN, personal tags.
- **Contacts and Drive.** Who the user met, and the files in their workspace.
- **Generate media.** Images and spoken audio from text.

Some actions use the user's coins or subscription. Mention that in a few words before doing
one, so the user is never surprised.

The connection itself is described at https://meeting.ai/mcp. Send the user there for setup
questions, or use the `meeting-ai-setup` skill.

## Reading meetings

1. `meetings_search` with keywords, or with `start_date` / `end_date` (UTC) and no query to
   list by date. It returns the user's `role` on each meeting.
2. `meetings_notes` for one meeting: facts plus every notes section with its `id`. A meeting
   still running is not an error; its notes are live and provisional.
3. `meetings_transcript_search` to find where a phrase was said. `meetings_transcript` to
   read it all, following `next_cursor` until it is `null`.

Notes are a summary. For exact wording, numbers, or names, read the transcript.

## Exporting

`meetings_export` saves a file to Drive and returns a login-gated link. Formats: `pdf` (the
full Visual Notes and Summary document), `summary_only`, `transcript_only`,
`summary_and_transcript` (Markdown). PDF is built in the background: the first call returns
`processing`; call again with the same arguments until `ready`. Once `ready`, **do not call
again** for the same meeting and format, because each repeat creates a duplicate file. The
meeting must be finished.

## Sharing and tags

- `meetings_share` turns on the public link, PIN-protected by default. Pass
  `require_pin: false` only on explicit request. Email sharing lives in the web app.
- `meetings_tag` and `drive_tag` apply tags that already exist. They never create tags. An
  unknown name returns the available tags. Tags are personal to the user.

## Contacts

`contacts_search` finds people by name, role, or company, with how many meetings they appear
in. `contacts_update` edits name, email, role, or company; a new name is confirmed across the
contact's meetings.

## Drive

`drive_search` lists or searches files with tags, size, owner, and an `etag`. `drive_rename`
renames a file the user owns; pass the `etag` as `if_match` so a concurrent change is refused
rather than overwritten. Uploading, deleting, and sharing files stay in the web app.

## Media

`media_image_gen` creates an image from a prompt; `media_audio_gen` reads text aloud into an
audio file. Both save to Drive, return a login-required link, spend the user's coins, and
create a new file every call. Confirm before repeating.

## Permissions

Editing notes, sharing, stopping a recording, and renaming Drive files are **owner only**.
Check `role` first and say so when the user is not the owner. Tagging works on any meeting
the user can see.

## Workspaces

Tools default to this connection's active workspace. `workspace_list` shows all of them;
`workspace_set_active` changes the default for this connection only. Pass `workspace_id` on
a single call for a one-off.

## Style

Use product words: workspace, meeting, notes, transcript, Drive, Visual Note. Give titles and
dates, not ids. On a fixable error, follow its instructions once; do not loop. When a tool
says the account has no coins or no active subscription, relay the message and stop.
