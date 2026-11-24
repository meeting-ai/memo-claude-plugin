---
name: meeting-ai-overview
description: Use when the user asks what Meeting.ai can do, or asks about their meetings, notes, transcripts, action items, contacts, Drive files, exports, tags, sharing, or workspaces in Meeting.ai. Covers the everyday reading tools plus export, media generation, contacts, Drive, and workspace switching. For recording a meeting, editing notes, or Visual Notes, the dedicated skills go deeper.
---

# Meeting.ai in Claude

> **Requires the Meeting.ai MCP server.** This skill calls its tools. If `workspace_list` is not
> available in this session, stop and follow the `meeting-ai-setup` skill. Do not guess or invent
> results. Tool names can carry a prefix, for example
> `mcp__plugin_meeting-ai_meeting-ai__workspace_list` in Claude Code.

Meeting.ai records meetings and writes the notes. Through these tools Claude finds, reads,
edits, exports, and shares those notes, and creates Visual Notes and images. Decks,
research, and spreadsheets are made in the Meeting.ai app, not through these tools.

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
- **Generate media.** Images from text.

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

- `meetings_share` turns on the public link, PIN-protected by default. The link shows the
  notes and, by default, the transcript. Tell the user this before you share. When the reply
  has a `pin`, give the user the PIN with the link. Pass `require_pin: false` only on
  explicit request. Removing an existing PIN also requires `remove_existing_pin: true` and
  the owner's explicit request. Email sharing lives in the web app.
- `meetings_tag` and `drive_tag` apply tags that already exist. They never create tags. An
  unknown name returns the available tags. Tags are personal to the user.

## Contacts

`contacts_search` finds people by name, role, or company, with how many meetings they appear
in. `contacts_update` edits name, email, role, or company; a new name is confirmed across the
contact's meetings.

## Drive

`drive_search` lists or searches files with tags, size, owner, and an `etag`. Give the user
the file name, not the returned link: the file page is not live yet. `drive_rename` renames
a file the user owns; pass the `etag` as `if_match` so a concurrent change is refused
rather than overwritten. Uploading, deleting, and sharing files stay in the web app.

## Media

`media_image_gen` creates an image from a prompt. It saves to Drive, returns a login-required
link, spends the user's coins, and creates a new file every call. Confirm before repeating.

A long generation can return the tool error `JOB_PENDING` with `data.job_id`. This is not a
failure. The generation continues and can be charged. The file goes to the Drive of the
workspace that was active when the generation started. **Do not call the media tool again**:
that starts and charges a new generation. Do one of these:

- Tell the user that Meeting.ai saves the file to Drive when it is ready.
- Call `media_job_status` with the `job_id`. It costs nothing. When the file is saved, it
  returns `drive_link` and `file_name`, and it can also return the image. If it returns
  `JOB_PENDING` again, the file is not ready. Wait before you call it again. Do not call it in
  a fast loop.

`recipe_visual_note` can return `JOB_PENDING` too. Use the same steps.

Errors from `media_job_status`:

- `NOT_FOUND`: this account has no job with this `job_id`, or the result expired.
- `GENERATION_DECLINED`: no usable file was made. A job that failed is not charged, but a job that
  finished without usable media is charged. Give the user the message from the server, which says
  whether the call was charged. Do not tell the user that nothing was charged unless the message
  says so. Generate again only if the user asks.
- `DELIVERY_FAILED`: see the tool errors in Style.

## Permissions

Editing notes, sharing, stopping a recording, and renaming Drive files are **owner only**.
Check `role` first and say so when the user is not the owner. Tagging works on any meeting
the user can see.

For a meeting that someone else owns, the owner's download settings decide whether its notes
or transcript can be exported. A refusal there is not a bug. Tell the user.

## Workspaces

Tools default to this connection's active workspace. `workspace_list` shows all of them;
`workspace_set_active` changes the default for this connection only. Pass `workspace_id` on
a single call for a one-off.

`media_image_gen` and `recipe_visual_note` do not accept `workspace_id`.
They spend the user's coins and always save to the active workspace. Before you call one,
check the active workspace with `workspace_list`. If the content comes from a different
workspace, ask the user before you change the active workspace.

## Style

Use product words: workspace, meeting, notes, transcript, Drive, Visual Note. Give titles and
dates, not ids. On a fixable error, follow its instructions once; do not loop. When a tool
says the account has no coins or no active subscription, relay the message and stop.

For `JOB_PENDING`, follow the steps in Media. Do not repeat the call.
For `DELIVERY_FAILED`, the media was generated and charged, but it was not saved to Drive.
Tell the user the reason from the message and stop.
