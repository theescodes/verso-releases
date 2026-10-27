# Security policy

A browser handles everything you type and read. Bugs that weaken its isolation or let data out
are treated as security issues, not just bugs.

## Reporting

Please use GitHub's **private vulnerability reporting** on this repository (Security tab →
"Report a vulnerability"); the source is private, so this is the place. Don't open a public
issue for anything below.

You'll get an answer within a few days. Fixes ship as a new release and the advisory is published
once the fixed version is out.

## Supported versions

Only the latest release gets fixes.

## In scope

- Anything that lets a web page escape WebKit's sandbox, read another Space's cookies or storage,
  or reach the file system beyond what a download or a file upload the user chose allows.
- Any data (URLs, titles, cookies, form contents) leaving the Mac to an origin other than the site
  the user opened, or the connections listed in `PRIVACY.md`.
- A page intercepting a reserved shortcut (`⌘T`, `⌘W`, `⌘L`, `⌘Q`, …) or spoofing the browser
  chrome (address field, dialogs, permission prompts).
- Entitlement creep: a build requesting more than the entitlements listed in `PRIVACY.md`.
- A release whose DMG is not signed with Verso's Developer ID and notarized, or does not match
  its `SHA256SUMS`, or an update feed that could serve a build without Verso's update signature.
- Supply-chain problems in the dependencies or the release workflow.

## Out of scope

- Vulnerabilities in WebKit itself. Report those to Apple; Verso ships the system WebKit and
  picks up its fixes with macOS updates.
- Websites that behave badly within the web platform's rules.
