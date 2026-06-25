# Mi Home Tools Article Refresh Audit

Date: 2026-06-25

## Scope

Reviewed the existing Mi Home Tools article and project-page entry against the
current `placeless/mihome-tools` `main` branch.

The refresh covers documentation only. It does not change the post filename,
front matter, permalink, layouts, includes, deployment configuration, or
historical media.

## Current Behavior

- `_posts/2026-05-02-mihome-tools.md` describes the original packet-capture and
  Mi Home cloud protocol investigation.
- The article says the macOS repository provides feed and feed-stat scripts,
  but does not describe the current installation or command interface.
- The article presents the iPhone client as an idea that still needs to be
  implemented in JavaScript.
- `projects.md` describes the project only as a more convenient manual-control
  method.

## Upstream Changes Reviewed

The current project is version `0.3.5` and now provides:

- isolated installation through `uv tool`;
- the `mihome-feed`, `mihome-feed-stats`, and `mihome-login` commands;
- a protected `feeder.env` configuration file;
- Xiaomi session creation and refresh through `mihome-login`;
- local portion limits and redacted debug output;
- a direct Scriptable iPhone client with an installer, Keychain-backed setup,
  feed confirmation, Shortcuts input/output, statistics, and a Home Screen
  widget.
- the author's local macOS workflow, which uses Alfred keywords to run the
  `Feed` and `Feed Stats` shortcuts before invoking the CLI commands.

Sources reviewed:

- `https://github.com/placeless/mihome-tools/blob/main/README.md`
- `https://github.com/placeless/mihome-tools/blob/main/scriptable/README.md`
- `https://github.com/placeless/mihome-tools/blob/main/pyproject.toml`

## Risks Or Unknowns

- Mi Home cloud APIs are private and may change without notice.
- The implementation is tailored to a specific feeder action shape and to
  event key `4.2`, with the portion count in MIoT property `piid: 4`.
- Xiaomi session values and device identifiers are sensitive and must not
  appear in article screenshots or examples.
- Scriptable cannot perform Xiaomi account login; an expired session must be
  refreshed elsewhere and imported again.
- Four WebP screenshots were supplied and reviewed. They show the Alfred
  keyword entry, installed Scriptable scripts, the feed-portion picker, and the
  Home Screen statistics widget. No account credentials, device identifiers,
  or API values are visible.

## Implemented Changes

1. Rewrite the article as a practical guide while preserving its title, date,
   filename, and permalink.
2. Retain a concise explanation of packet capture and the RC4/SHA-256 request
   protocol.
3. Document CLI installation, configuration, login refresh, feed actions,
   statistics, automation behavior, and safety considerations.
4. Document the complete Scriptable installation and usage path.
5. Document the author's Alfred Workflow to macOS Shortcuts to CLI invocation
   path.
6. Update the Mi Home Tools entry on `projects.md` to mention both the CLI and
   direct iPhone client.
7. Add the four approved screenshots with descriptive alternative text.

## Verification Commands

```sh
mise exec -- bundle exec jekyll build
rg -n "mihome-login|mihome-feed|mihome-feed-stats|MiHomeInstaller|MiHomeWidget" \
  _posts/2026-05-02-mihome-tools.md projects.md
rg -n "自己.*重写|丢给 AI 仿写" _posts/2026-05-02-mihome-tools.md
```
