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

## Uninstall

Dragging Verso to the Trash removes the app but not what it keeps. Like every sandboxed app,
Verso keeps its tabs, history, bookmarks, settings, extensions and every Space's cookies and site
data in its container, which macOS leaves in place. To remove all of it:

1. If Verso is the default browser, choose another one in System Settings → Desktop & Dock →
   Default web browser.
2. Quit Verso.
3. In Terminal, run:

   ```sh
   tccutil reset All codes.thees.verso
   rm -rf /Applications/Verso.app \
     ~/Library/Containers/codes.thees.verso \
     ~/Library/Application\ Scripts/codes.thees.verso
   ```

   The first command takes back the camera, microphone and Full Disk Access permissions you gave
   Verso, and must run while the app is still in Applications; the second removes the app and
   its folders.
4. macOS protects the container folder itself: unless Terminal has Full Disk Access, the second
   command empties it and reports "Operation not permitted" for the folder and the one file left
   in it, macOS's own record of the container. Run `open ~/Library/Containers` and drag the
   folder `codes.thees.verso` to the Trash; Finder may name it Verso.

Without Terminal: turn Verso off in System Settings → Privacy & Security under Camera, Microphone
and Full Disk Access, drag Verso from Applications to the Trash, then choose Go → Go to Folder…
in Finder, enter `~/Library/Containers` and drag the folder named `codes.thees.verso` (or Verso)
to the Trash as well.

Files you downloaded stay where they are, and crash reports macOS wrote for Verso stay in its
crash reporter until macOS deletes them.

The website has the same steps in seven languages:
[versobrowser.com/uninstall](https://www.versobrowser.com/uninstall).

## Speed

Measured from outside with the same yardstick for every browser, on an M4 Pro with 24 GB running
macOS 27.2, on 2026-10-28: relaunch is the median time from the launch request until the first
window is on screen (Verso restoring a session with the ten tabs, Safari opening its start page,
Arc restoring the maintainer's sidebar); memory is the footprint of all the browser's processes,
idle for 30 s after the same ten pages (Wikipedia, MDN, GitHub, apple.com, Hacker News, The Verge,
BBC News, Reddit, YouTube, Amazon) were opened one by one; CPU is their share over the next 10 s.
The row is measured again for each release.

| Verso 0.2.0 against            | Verso  | Safari 27.2 | Arc 1.170 (Chromium 156) |
| ------------------------------ | ------ | ----------- | ------------------------ |
| Relaunch to the first window   | 0.37 s | 0.63 s      | 2.1 s                    |
| Memory with the ten pages open | 1.7 GB | 2.3 GB      | 3.0 GB                   |
| Idle CPU with the ten pages    | 3 %    | 4 %         | 9 %                      |
| Download                       | 7 MB   | with macOS  | 458 MB                   |

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
