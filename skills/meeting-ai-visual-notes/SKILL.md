---
description: Use when the user wants a Visual Note, an infographic-style one-page summary image, whether from a Meeting.ai meeting, from several meetings, or from any text or topic they supply. Also use to redraw a meeting's own visual note after its notes changed.
---

# Visual Notes

A Visual Note is a one-page, house-styled image that summarizes content visually. Meeting.ai
draws one for every finished meeting. Through Claude the user can also create one from any
meeting, any set of meetings, or any topic at all.

## From a meeting

1. Find the meeting with `meetings_search` and read it with `meetings_notes`.
2. Build the `content` from the notes: title, date, key points, decisions, action items.
   Include enough substance for the picture to say something; a title alone makes a thin
   image. For a long meeting, use the summary and decisions rather than the whole transcript.
3. Call `recipe_visual_note` with that `content`. Pick `aspect_ratio` for where it will be
   used: `4:5` (default) or `1:1` for chat and social, `16:9` for slides, `9:16` for phone
   stories.
4. The image is saved to the user's Drive and returned inline with a login-required link.
   Show the image and tell the user it is in their Drive.

## From several meetings

Combine the key points of each meeting into one `content` block, grouped by meeting or by
theme, then call `recipe_visual_note` once. This is a good way to turn a week of meetings
into one shareable picture.

## From any topic

The recipe is not limited to meetings. If the user gives a topic, a document, or a plain
paragraph, pass that text as `content`. When the user only gives a topic name, write a short
structured outline first (three to six points) and use that as the content, so the result
has real information on it.

## Redrawing a meeting's own visual note

When the user edits a meeting's notes and wants the meeting's visual note to match, call
`meetings_update_notes` with `regenerate_visual_note: true`. This redraws from the meeting's
current notes and replaces the visual note on the meeting itself. `recipe_visual_note`, by
contrast, creates a new standalone image in Drive and does not touch the meeting.

## Cost and repeats

Every call spends the user's coins and creates a new image. Confirm before generating, and
do not regenerate to "try again" unless the user asks. If the account has no coins or no
active subscription, the tool returns a clear error. Relay the message and stop.
