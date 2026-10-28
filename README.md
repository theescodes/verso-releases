# Verso

A macOS browser on WebKit: vertical tabs in a sidebar, Spaces with their own cookie jars, a
command bar, keyboard first. No AI features, no telemetry.

This repository holds the signed and notarized builds, the update feed Verso checks, and the
place to report bugs and security issues. The source code is private.

## Download

The latest DMG is under [Releases](https://github.com/theescodes/verso-releases/releases/latest),
with `SHA256SUMS`. Open it, drag Verso into the Applications folder it shows, then open Verso
from Applications and eject the disk image. Verso needs macOS 26 or later on Apple silicon and
updates itself through Sparkle, which needs it to run from Applications rather than from the
disk image.

## Reporting

- **A site that breaks:** a page that works in Safari and not in Verso, through the site
  breakage form.
- **A bug:** through the bug form, with what Help → Copy Diagnostics puts on the clipboard
  ([PRIVACY.md](PRIVACY.md) lists every field: nothing personal, and nothing is sent until you
  paste it).
- **An idea:** through the idea form.
- **A security issue** (data leaving the Mac, a page breaking out of its sandbox): privately, as
  [SECURITY.md](SECURITY.md) says.

[Open an issue](https://github.com/theescodes/verso-releases/issues/new/choose).

## Privacy, licences and imprint

[PRIVACY.md](PRIVACY.md) lists every connection Verso makes on its own and every entitlement it
holds. Verso is free to install and use; the builds are proprietary ([LICENSE](LICENSE)), and the
material by others they contain is in [THIRD_PARTY_NOTICES.md](THIRD_PARTY_NOTICES.md).

The website is https://www.versobrowser.com. Its [imprint](https://www.versobrowser.com/imprint)
says who is behind Verso and this repository; its
[privacy notice](https://www.versobrowser.com/privacy) covers the website, PRIVACY.md the app.
