# Privacy

What leaves your Mac when you use Verso, and why. There is no analytics, no crash reporting, no
account and no remote code. This file lists every connection the app makes on its own; the pages
you open make their own requests, as in any browser, inside WebKit's sandboxed processes.

## Connections the app makes

| What                       | When                                                                                                                                                                                                                                                                                                                                | Where to                                                                                                                                                | What is sent                                                                                                                                                                                                                                                                                                                                                                                                      |
| -------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Page loads                 | You open a page.                                                                                                                                                                                                                                                                                                                    | The site.                                                                                                                                               | The request any browser makes; cookies of that Space's profile only.                                                                                                                                                                                                                                                                                                                                              |
| Favicons                   | A page names an icon, or none, and `/favicon.ico` is tried.                                                                                                                                                                                                                                                                         | The host the page named.                                                                                                                                | One request for the icon, without cookies, in a session that keeps nothing on disk.                                                                                                                                                                                                                                                                                                                               |
| Searches                   | You press Enter on text that is not an address.                                                                                                                                                                                                                                                                                     | The search engine you chose.                                                                                                                            | The text. Nothing is sent while you type: there are no search suggestions.                                                                                                                                                                                                                                                                                                                                        |
| Update check               | You choose Verso → Check for Updates… or Settings → Updates → Check Now, or once a day after you said yes to automatic checks: Verso asks on its second launch, and Settings → Updates changes it (Off, Notify, Install). A build without the update key, as every build before the first release, has no updater and never checks. | GitHub: `github.com`, which forwards to its host for release files.                                                                                     | One request for the release feed, with Verso's and Sparkle's names and versions in the User-Agent. No system profile, and no cookie from an earlier check: the app's cookie store, which nothing else in Verso uses, is emptied before each one. An update is downloaded from the same place: when you choose to install it, or, with Install, as soon as a daily check finds it, to be applied when Verso quits. |
| Fraudulent website warning | You open a page: WebKit checks its address first, as Safari does. On, as in Safari; Verso has no switch for it yet.                                                                                                                                                                                                                 | Google Safe Browsing and Apple, through macOS's Safe Browsing service (Tencent Safe Browsing too when the Mac's region is China mainland or Hong Kong). | When the check needs it, information calculated from the address, never the address itself; Google (or Tencent) may log your IP address. This is Apple's description of the same check in Safari.                                                                                                                                                                                                                 |
| Extension installs from the Chrome Web Store | You choose Install on an extension's page in the Chrome Web Store (`chromewebstore.google.com`), and only then: Verso checks no extension for updates, and to update one you install it again from its page. | Google: the update endpoint Chrome itself asks for an extension's package (`clients2.google.com`), which forwards to Google's file host. | One request for the package, in a session of the app's own that sends no cookie, whichever Space you are in: the extension's id from the page's address and a Chrome version number, which the endpoint requires and Verso does not have, plus Verso's name and version in the User-Agent, as the system sends it. The package is installed only if it is the extension the page names and carries the store's signature. |
| Filter lists               | Never.                                                                                                                                                                                                                                                                                                                              | Nowhere.                                                                                                                                                | Nothing. EasyList and EasyPrivacy ship inside the app and are updated with it.                                                                                                                                                                                                                                                                                                                                    |

A web extension you install (File → Install Extension…, or Install on its Chrome Web Store page)
runs inside WebKit with what you granted it on the install sheet, and whatever it fetches is its own
doing, like a page's requests. Verso fetches an extension only when you choose Install on its store
page, checks none for updates and sends nothing about them.

A lint rule (`network_only_where_privacy_lists` in `.swiftlint.yml`) flags the usual ways to open
a connection (URLSession, the Network framework, CFNetwork, sockets, image views that load a URL,
and Sparkle, the updater) in any file but the ones behind this table. It reads text, not types,
so review covers what it cannot see, such as the connections a dependency makes.

An update is installed only when its EdDSA signature matches the key built into Verso, and the
feed that offers it has to carry a valid signature of the same key; the DMG itself is signed with
Verso's Developer ID and notarized by Apple. `docs/release.md` describes how both are made.

## What stays on the Mac

- Open tabs, their back/forward state, bookmarks, history, the icons of the sites you visit and
  the downloads list live in one SQLite file in the app's sandbox container under Application
  Support.
- Settings (the search engine, the appearance, the downloads folder and how downloads are
  saved, whether content blocking is on) live in the same SQLite file, and so do the sites you
  turned content blocking off for (View → Turn Off Content Blocking for This Site), one host
  name each. One turned off in a private Space is kept in memory until Verso quits, never
  saved. A downloads folder you choose is kept as a bookmark
  macOS issues for that folder, so Verso can write there after a relaunch; it grants access to
  that folder and nothing else.
- Downloads go to your Downloads folder, or the folder you chose in Settings, under the name the
  site suggests (numbered when it is taken), unless you ask to choose the place for each one.
  The downloads list keeps each file's name, address, size and place on disk for a month (a
  day, a week or until you clear it, as set in Settings); a private Space's downloads are
  listed only while the Space
  exists and never saved, and deleting the Space stops any still running. Every downloaded file
  carries macOS's quarantine flag as a web download, so the first open goes through
  Gatekeeper's check. For a file from a normal Space, macOS records the file's address and the
  page's in its own quarantine-events database, as it does for every browser; for a private
  Space's file Verso gives macOS no addresses.
- Each Space has a profile; each profile is its own cookie jar and storage, isolated by WebKit.
  A private Space keeps its cookies, site data and cache in memory only, records no history, and
  never saves its tabs, its bookmarks or their icons: all of it is gone when Verso quits or the
  Space is deleted, and after a relaunch the Space comes back empty. Only the Space itself is
  kept, with its profile's entry: its name, colour and icon, and that it is private. "Clear
  Website Data" on a Space removes everything its sites stored; deleting a Space removes its
  profile's store and forgets its recently closed tabs, so none of them can reopen in another
  Space.
- Web extensions you install are kept as Verso's own copy of the folder, ZIP or CRX file you
  chose, or of the package the Chrome Web Store served, under Application Support in the
  container, one folder per extension; the list of them, with what you granted each, whether it
  may see private Spaces and, for one from the store, the store's id for it, lives in the same
  SQLite file as the rest. What an extension stores for itself (its `storage.local`) is kept by
  WebKit in the container under one fixed identifier per installed extension, and removing the
  extension removes that storage with its files and its row. An extension sees a private
  Space's pages, tabs and addresses only if you ticked "Allow in private Spaces" for it.
- Content blocking uses EasyList and EasyPrivacy, which ship inside the app, converted when
  Verso is built; a new snapshot comes with a Verso update. Verso compiles them once per version
  into WebKit's rule-list store in its own container, and WebKit applies them inside its
  processes: no list is downloaded, and content blocking checks no page address against anything
  outside the Mac.
- A site that wants your camera or microphone, or a link that wants to open another app, is
  shown to you first, with the name of the site that asks. A no never reaches the system or the
  other app, and the page cannot ask again until it navigates.
- Making Verso the default browser is a setting of this Mac: after you confirm in the system's own
  alert, macOS writes it through Launch Services. Nothing is sent anywhere.
- Whether Verso checks for updates on its own and whether it downloads them too (Settings'
  Off, Notify or Install), when it last checked and a version you chose to skip live in the app's
  preferences, where the updater keeps them. A downloaded update waits in the updater's own cache
  until Verso quits or you choose Restart to Update.
- Crash logs stay in the system's crash reporter; the app never uploads them.
- Help → Copy Diagnostics puts a short report on the clipboard when you choose it, for a bug
  report: Verso's version and build, the macOS version and build, the Mac's model identifier
  (such as `Mac16,8`, the same on every Mac of that model), whether Verso is the default browser,
  the search engine's name (or "custom" for one you added), how many Spaces there are and how
  many of them are private, and how many tabs are live, warm, cold, suspended or crashed. No
  addresses, titles, Space or profile names, history, or anything that names you or your Mac.
  Nothing is sent: the report goes wherever you paste it.

## Entitlements

Verso runs in the macOS App Sandbox. Every entitlement is declared in `project.yml`, and each is
here with the reason for it:

| Entitlement                                                | Why                                                                                                                                                                   |
| ---------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `app-sandbox`                                              | The whole app is sandboxed; web content runs in WebKit's own sandboxed processes on top.                                                                              |
| `network.client`                                           | Loading web pages.                                                                                                                                                    |
| `files.downloads.read-write`                               | Saving downloads to `~/Downloads`.                                                                                                                                    |
| `files.user-selected.read-write`                           | File uploads, "Ask where to save" and a downloads folder chosen in Settings, through the system panels only; the chosen folder is kept as a security-scoped bookmark. |
| `cs.allow-jit`                                             | WebKit's JavaScript engine compiles JavaScript to machine code.                                                                                                       |
| `device.camera`, `device.microphone`, `device.audio-input` | Only when a site asks and you allow it, with the system prompt. `device.audio-input` is the microphone's key under the hardened runtime of the signed builds.         |
| `print`                                                    | Printing a page.                                                                                                                                                      |
| `temporary-exception.mach-lookup.global-name`              | Updates: the updater talks to the installer it starts outside the sandbox, by the two names Sparkle documents for sandboxed apps (`<bundle id>-spks`, `-spki`).       |

A new entitlement is added in the same pull request as its row here. The UI-test runner, which never
ships, holds one more: `network.server`, to serve the test pages on 127.0.0.1 to the app it drives.
The passkey entitlement for websites (`com.apple.developer.web-browser.public-key-credential`) is
prepared but off until Apple grants it (`App/Passkeys.xcconfig`); its row joins the table when it is
turned on.
