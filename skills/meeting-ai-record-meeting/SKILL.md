---
description: Use when the user wants Meeting.ai to record, join, or take notes for an online meeting (Zoom, Google Meet, Microsoft Teams), transcribe a recording, check whether a recording is done, or stop a recording. Also use when the user asks how to get a meeting into Meeting.ai.
---

# Create notes from an online meeting

Meeting.ai records the meeting and writes the notes for you. There are two ways to get a
meeting in through Claude, and a few more through the Meeting.ai apps. Explain the options
in plain words, then do the one the user picks.

## Option 1: Send the notetaker to a live call

Works for Zoom, Google Meet, and Microsoft Teams.

1. Ask for the meeting invite link if the user has not given one.
2. Call `meetings_create` with `meeting_url`. Add `language` (for example `en` or `id`) only
   when the user says which language the meeting is in; otherwise leave it to auto-detect.
3. Tell the user: the notetaker is joining, it appears in the call as a participant named
   Meeting.ai, and the host may need to admit it from the waiting room.
4. The tool returns a `meeting_id` right away. Notes are written while the meeting runs and
   finished a few minutes after it ends.

## Option 2: Transcribe a recording you already have

Works for an audio or video file shared on Google Drive.

1. The file must be shared so that anyone with the link can view it. If the user gets an
   access error, that is the fix.
2. Call `meetings_create` with the Google Drive link as `meeting_url`.
3. Transcription runs in the background. A one-hour recording usually takes a few minutes.

YouTube links are not supported. Local files cannot be uploaded through Claude; point the user
to the Meeting.ai web app or mobile app for that.

## Following progress

Call `meetings_notes` with the `meeting_id`. While the meeting is still running, `status`
says so and the sections carry the live notes written so far, marked as provisional. When
`status` shows the meeting is finished, the sections are final. Do not poll in a tight loop.
Check when the user asks, or suggest they come back after the meeting ends.

## Stopping a recording early

`meetings_stop` makes the notetaker leave a live call and finalises the meeting from what was
captured so far. Only the meeting owner can do this, and it only works while the notetaker
is in the call. Recordings from uploaded files are not stopped this way.

## Other ways to record, outside Claude

Mention these when the user asks what else is possible:

- **Calendar sync** in the web app. Connect Google or Microsoft calendar and the notetaker
  joins scheduled meetings automatically.
- **Mobile app** for in-person meetings. Record from the phone and get the same notes.
- **Web upload** for local audio and video files.

## Billing

Recording and transcription use the workspace's Meeting.ai subscription or coins. If the
account has no active subscription or no coins left, `meetings_create` returns a clear
error. Relay the message to the user and stop; do not retry.
