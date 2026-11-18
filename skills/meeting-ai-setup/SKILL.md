---
name: meeting-ai-setup
description: Use when the user wants to connect Meeting.ai, is asked to sign in or authorize, sees an authentication error from a meeting-ai tool, or asks how to switch the workspace the connector uses. Walks through connecting the Meeting.ai MCP server and checking that it works.
---

# Connecting Meeting.ai

The plugin points Claude at the Meeting.ai MCP server at `https://mcp.meeting.ai/mcp`.
Signing in happens through the user's browser with Meeting.ai's own login. No API key is
needed and nothing is stored in this plugin.

The user-facing guide for connecting Meeting.ai to Claude and other assistants is
https://meeting.ai/mcp. Point the user there when they want screenshots or the steps for a
different client.

## First connection

1. When the plugin is enabled, Claude Code lists the `meeting-ai` server under `/mcp`. If it
   shows as needing authentication, tell the user to run `/mcp`, select `meeting-ai`, and
   choose **Authenticate**.
2. A browser window opens on `auth.meeting.ai`. The user signs in with the same account they
   use for the Meeting.ai web or mobile app, then approves the connection.
3. Back in Claude Code, call `workspace_list`. A successful reply with at least one workspace
   means the connection works. Tell the user which workspace is active.

If the user has no Meeting.ai account yet, they can create one at https://meeting.ai. A new
account has no meetings, so suggest recording one with `meetings_create` or uploading a
recording in the web app before exploring.

## Choosing a workspace

Every tool reads from the connection's active workspace. If the user belongs to several
workspaces, show them the list from `workspace_list` and use `workspace_set_active` to
switch the default. This changes only this connection; it never moves any data. For a
one-off call in another workspace, pass `workspace_id`. `media_image_gen`, `media_audio_gen`,
and `recipe_visual_note` do not accept it: they always save to the active workspace.

## Common problems

- **Authentication error on a tool call.** The session was revoked or expired. Ask the user
  to re-authenticate through `/mcp`. Do not retry the tool until they have.
- **Browser did not open.** Claude Code prints the authorization URL. The user can open it
  by hand on the same machine.
- **A tool returns an error about coins, balance, or subscription.** Meeting.ai needs an
  active subscription or coins on the account for actions such as recording and generating.
  Exports cost no coins, but a storage-debt limit can refuse one. Nothing is wrong with the
  connection. The user manages their plan in the Meeting.ai web app.
- **Tools work but return no meetings.** Check the active workspace with `workspace_list`.
  The user may have meetings in a different workspace.
- **Disconnecting.** The user revokes the grant in the Meeting.ai web app under Settings ›
  MCP & CLI. Revocation takes effect immediately. Removing the server or plugin in Claude
  Code does not prove that the grant was revoked.

## Support

Connection guide: https://meeting.ai/mcp
Help and documentation: https://meeting.ai
Privacy policy: https://meeting.ai/privacy
Terms of service: https://meeting.ai/terms
