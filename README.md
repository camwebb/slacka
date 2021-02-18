# Slacka: an Awk (Gawk) client for Slack

## Installation

### Dependencies

 * `gawk`
 * [`gawkextlib`](https://sourceforge.net/p/gawkextlib/)
 * `gawk-json`

### OAuth Tokens

You’ll need an App token from Slack. As of 2021-02-17, this is the
procedure:

 * Make sure you are logged into one of your Slack workspaces
 * Visit <https://api.slack.com/> and click on ‘Your Apps’ (top right)
 * ‘Create New App’ and give it a name (anything) and pick a
   Workspace/Team. **Note**: you will need a new app for each
   Workspace/Team.
 * ‘Add features and functionality’ -> ‘Permissions’ -> ‘Scopes’ ->
   ‘User Token Scopes’ -> ‘Add an OAuth Scope’ and add:
    * `channels:history`
    * `channels:read`
    * `groups:history` (for your private channels)
    * `groups:read`
    * `users:read` (see user names)
    * `chat:write` (for posting)
 * ‘Install to Workspace’. Note that the Admins for your Workspace may
   have restricted users ability to add Apps, in which case you’ll need to 
   ask ‘Request’ installation to Workspace.
 * The OAuth page will then ask you if you want to Allow your app to
   access/perform various things
 * You’ll be returned to the OAuth & Permissions page. Copy the OAuth
   token (which should begin with `xoxp-...`

### Config file

 * Create a directory `~/.config/slacka/`
 * Using the included [`config.json`](config.json) as a guide, create
   a minimal config file at `~/.config/slacka/config.json`. You’ll
   need at least one team (`id` and `token`) and one channel (make a
   dummy channel to start: `id` and `label`). Once you have this file
   in place, `slacka cfg` will open this file for editing (with
   `emacs`).
 * Scan available channels with `slacka chans`, and add them to the
   config file.  The `label` is your shortcut for that channel. Labels
   must be unique across all teams.

### Usage

 * First generate the `~/.config/slacka/users.json` file with `slacka
   users`. This caches the users’ real names for their user IDs (for
   all teams). If you later see that some (new) users in a team are
   appearing as `<UXXXXX>` re-run `slacka users` to regenerate the
   cache.
 * Plain `slacka` will give you usage and a list of available channels
 * To see the ten most recent threads in a channel, and their initial
   post: `slacka <channel>`
 * To read all posts in a thread: `slacka <channel> <thread_id>`,
   where `<thread_id>` is the numeric code in the heading for each
   thread (actually a time stamp).
 * To post a new message to the channel: `slacka <channel> new "your message"`
 * To post a message to a thread: `slacka <channel> <thread_id> "your
   message"` (e.g.: `slacka news 1610131196.000800 "I agree!"`)

