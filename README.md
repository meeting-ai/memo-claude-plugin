# Meeting.ai plugin for Claude

![Meeting.ai in a meeting room, with the Meeting.ai notetaker on the table](assets/banner.webp)

Meeting.ai is the AI that works before, during, and after your meeting. It prepares decks and
research before, records and writes the notes during, and turns what was actually said into
minutes, documents, spreadsheets, follow-up decks, audio briefs, and Visual Notes after.

This plugin brings that into Claude. Once connected, you talk to Claude about your meetings
the way you would talk to a colleague who sat in every one of them: ask what happened, ask
what was decided, ask for it written up differently, ask for a picture of it.

## Install

Inside Claude Code, add the Meeting.ai marketplace and install the plugin:

```
/plugin marketplace add meeting-ai/memo-claude-plugin
/plugin install meeting-ai@meeting-ai
```

Or from your terminal:

```bash
claude plugin marketplace add meeting-ai/memo-claude-plugin
claude plugin install meeting-ai@meeting-ai
```

> _The plugin is also on its way to the Claude plugin directory. Once listed there, it will be
> installable with one click from Cowork or the directory page._

## Connect your account

The first time Claude reaches for a Meeting.ai tool, it asks you to sign in. Run `/mcp`, pick
`meeting-ai`, and choose **Authenticate**. Your browser opens the Meeting.ai sign-in page.
Sign in with the account you use for the Meeting.ai app and approve the connection.

That is the whole setup. No API key, nothing to paste. The plugin stores nothing on its own;
Meeting.ai's login handles it, and you can revoke the connection any time from Connected
Apps in the Meeting.ai web app. If you don't have an account yet, create one at
[meeting.ai](https://meeting.ai).

## Try it

Once connected, ask in plain words. Three to start with:

> Join my Google Meet and take notes: https://meet.google.com/abc-defg-hij

> Recap all my Acme meetings from September into one summary: decisions, open questions, and action items with owners.

> Turn my Q3 planning meeting into a one-page Visual Note.

## Everything else

Once you are connected, Claude also knows how to tag meetings and Drive files with the tags
you already use, rename files, look up and tidy contacts, read text aloud into an audio file,
generate an image from a prompt, and switch between your workspaces. You do not need to
learn any of that up front. Ask for what you want in plain words, and Claude uses the right
tool or tells you it is done in the web app instead.

Some actions, such as recording, exporting, Visual Notes, and media generation, use your
Meeting.ai subscription or coins. Claude mentions this before doing one. If your account has
no active subscription or coins, Claude tells you and stops.

## What is in this repository

| Path | Purpose |
|---|---|
| `.claude-plugin/plugin.json` | Plugin manifest, including the Meeting.ai MCP server at `https://mcp.meeting.ai/mcp` |
| `.claude-plugin/marketplace.json` | Lets Claude Code install the plugin straight from this repository |
| `skills/` | The guidance Claude follows for recording, editing notes, Visual Notes, setup, and everything else |

The MCP server itself is a hosted service operated by Meeting.ai. This repository contains no
server code and no credentials.

## Links

- Connection guide, for Claude and other assistants: https://meeting.ai/mcp
- Website: https://meeting.ai
- Privacy policy: https://meeting.ai/privacy
- Terms of service: https://meeting.ai/terms
- Support: support@meeting.ai

## License

Apache License 2.0. See [LICENSE](LICENSE). The Meeting.ai name, logo, and artwork are trademarks
of Meeting.ai and are not covered by the license; see [NOTICE](NOTICE).
