# Slacka: lightweight CLI client for Slack (in Gawk)

[**SCREENSHOTS**](doc/screenshots.md) 

### Usage

```
  Maintenance: slacka                  init | auth | cfg | users
  New IM:      slacka                  newim <teamID> <userID>
  List chans:  slacka [ <channel> ]    chans
  Check new:   slacka                  new
  Read:        slacka   <channel>   [ <thread_id> ] 
  Write:       slacka   <channel>   [ <thread_id> ] "your message"
  React:       slacka   <channel>     <thread_id>   :<emoji>:
  Search:      slacka   <channel>                   "/search term/"
    My chans: ...
```

**Setup**:

 1. First, run `slacka init` to create the config directory and dummy config
    file (`~/.config/slacka/config.json`)
 2. Run `slacka auth` to get team ID and auth token (see OAuth Tokens, below)
 3. Edit `config.json` with `slacka cfg`, adding the team and token
 4. Run `slacka users` to read user names for the team. This caches
    the users’ real names for their user IDs (for all teams) in
    `users.json`. If you later see that some (new) users in a team are
    appearing as `<UXXXXX>` re-run `slacka users` to regenerate the
    cache.
 5. Run `slacka chans` and edit `config.json` with `slacka cfg`, to
    add some channels. Note: DMs are treated just as other channels
    (`DXXX` and `GXXX`).
 6. Run `slacka new` to create `latest.json`

**Use**:

 * Plain `slacka` will give you help/usage and a list of available
   channels.
 * To see the ten most recent threads in a channel/DM, and the initial
   post: `slacka <channel>`
 * To read all replies in a thread: `slacka <channel> <thread_id>`,
   where `<thread_id>` is the numeric code in the status line for each
   thread (actually a time stamp).
 * To post a new message to the channel (and to the DM): `slacka
   <channel> "your message"`
 * To post a message to a thread: `slacka <channel> <thread_id> "your
   message"` (e.g.: `slacka news 1610131196.000800 "I agree!"`)
 * To check for new posts in the channel/DM, or new replies to the most
   recent post: `slacka new`.
 * To search in a channel: `slacka <channel> "/query terms/"`
 * To react: `slacka <channel> <thread_id> :<emoji>:`, where `<emoji>`
   is the standard emoji [`short_name`](emojis.txt)

Frequent updates to `config.json` are needed in general use, adding
DMs, channels, new teams, etc.

## Installation

### Dependencies

 * `gawk`
 * `gawk-json` (Build from source:
   [gawkextlib, gawk-json](https://sourceforge.net/p/gawkextlib/),
   [rapidJson](https://rapidjson.org/)) or: Arch AUR: `gawk-json`)
 * `curl`

### OAuth Tokens

You’ll need an App OAuth token from Slack. As far as I understand, the
only way to get a token is via a registered App
(<https://api.slack.com/apps/>). Once installed in a workspace, this
App will appear in the Workspace ‘Apps’ list seen by team members and
can be deinstalled by other users, or admins. (Note: in the following,
‘App’ refers to a Slack API App, that could do anything, and may be
called anything, not to this specific `slacka` Gawk app.)

The _easy way_ to get a token is to use the built-in `slacka auth`
function. This uses the distributed (multi-workspace) App registered
by the `slacka` author.  Note that this App is not in the Slack App
Directory, and not “approved” by Slack, and so some Workspaces may
require the workspace admins to allow use of Slacka (depending on
admin settings). If all is working well, you will redirected to a
webapp that will give you a token.

The _other way_ to get tokens is to register your own App.  If for
some reason the author’s Slacka app is deleted, then the built-in
`auth` function will no longer work and you would need your own Slack
App to get tokens.  Your App may either be installed in a single
Workspace, requiring several Apps if you have several Slack workspace,
or you may “Distribute” a single App, with a few extra settings.

As of 2021-02-17, this is the procedure for creating your own App in a
single workspace:

 * Make sure you are logged into one of your Slack workspaces
 * Visit <https://api.slack.com/> and click on ‘Your Apps’ (top right)
 * ‘Create New App’ and give it a name (anything) and pick a
   Workspace/Team. **Note**: you will need a new app for each
   Workspace/Team.
 * ‘Add features and functionality’ -> ‘Permissions’ -> ‘Scopes’ ->
   ‘User Token Scopes’ -> ‘Add an OAuth Scope’ and add:
    * `{channels & groups}:history` (groups are prival channels)
    * `{channels & groups}:read`
    * `users:read` (see user names)
    * `chat:write` (for posting)
    * `search:read` (search)
    * `reactions:write` (reactions)
    * `{im & mpim}:{read & history & write}` (for DMs)
 * ‘Install to Workspace’. Note that the Admins for your Workspace may
   have restricted users ability to add Apps, in which case you’ll
   need to ask ‘Request’ installation to Workspace. Also, free Slack
   plans only allow a maximum of 10 Apps.
 * The OAuth page will then ask you if you want to Allow your app to
   access/perform various things
 * You’ll be returned to the OAuth & Permissions page. Copy the OAuth
   token (which should begin with `xoxp-...`

The extra steps to Distribute your App (so you can use it in multiple
workspaces) are:

 * Add dummy redirect URL (e.g., <https://example.com/path>)
 * Confirm hardcode review
 * Distribute

### Config file

Use `slacka init` or create manually:

 * Create a directory `~/.config/slacka/`
 * Using the included [`config.json`](config.json) as a guide, create
   a minimal config file at `~/.config/slacka/config.json`. You’ll
   need at least one team (`id` and `token`) and one channel (`id` and
   `label`). To get the `id`s, look at the URL of the Slack web-app:
   <https://app.slack.com/client/TXXXXX/CXXXXXX>; `TXXXXX` is the team
   `id` and `CXXXXXX` is the channel `id`. 
 * Once you have this file in place, `slacka cfg` will open this file
   for editing (with `emacs`).
 * Scan available channels with `slacka chans`, and add them to the
   config file.  The `label` is your shortcut for that channel. Labels
   must be unique across all teams.

### slacka-notify

The included `slacka-notify` can be run in a cron job to run `slacka
new` on a regular basis. It will trigger a desktop notification, via
`notify-send`. Read file header for more info.

## Privacy policy

This app runs as an independent program on your computer and gives no
access to other users. The `auth` method uses a registered App’s
`client_secret` to authorize the app for your workspaces via a webapp
and provide you with an OAuth token; the webapp does not store this
OAuth token.  Other methods for obtaining an OAuth token exist (see
above).
