# Privacy

What leaves your Mac when you use Verso, and why. There is no analytics, no crash reporting, no
account and no remote code. This file lists every connection the app makes on its own; the pages
you open make their own requests, as in any browser, inside WebKit's sandboxed processes.

## Connections the app makes

| What                       | When                                                                                                                                                                                                                                                                                                                                | Where to                                                                                                                                                | What is sent                                                                                                                                                                                                                                                                                                                                                                                                      |
| -------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Page loads                 | You open a page.                                                                                                                                                                                                                                                                                                                    | The site.                                                                                                                                               | The request any browser makes; cookies of that Space's profile only.                                                                                                                                                                                                                                                                                                                                              |
| A proxy of your own | You gave a Space a proxy in its settings (the Proxy row, shown while Settings → Developer is on), and only while that switch is on: off, the Space's pages go direct again, and the proxy waits. Nothing else in Verso takes it. | The proxy you named, host and port, by HTTP CONNECT or SOCKSv5. | Every request the pages of that Space make, as the proxy protocol carries it: the host and port of each destination, and for a plain HTTP page the page itself. A proxy that cannot be reached fails the load; Verso never falls back to a direct connection. Favicons, the update check and a store install belong to no Space and keep the routes their rows describe. |
| Favicons                   | A page names an icon, or none, and `/favicon.ico` is tried. An SVG icon is passed over for a raster one or `/favicon.ico`.                                                                                                                                                                                                          | The host the page named. A host on this Mac only for a page on a local host; never another machine of your local network, so macOS never asks you for Verso's Local Network access and Verso declares none. | One request for the icon, without cookies, in a session that keeps nothing on disk, with the User-Agent the pages send (this Mac's Safari, nothing about Verso), `Accept: image/*` and the languages macOS adds (`Accept-Language`). At most 256 KB after decoding and 15 seconds, or the transfer is cancelled; what is kept is a PNG of at most 64 pixels made from it. |
| Searches                   | You press Enter on text that is not an address.                                                                                                                                                                                                                                                                                     | The search engine you chose.                                                                                                                            | The text. Nothing is sent while you type: there are no search suggestions.                                                                                                                                                                                                                                                                                                                                        |
| Update check               | You choose Verso → Check for Updates… or Settings → Updates → Check Now, or once a day after you said yes to automatic checks: Verso asks on its second launch, and Settings → Updates changes it (Off, Notify, Install). A build without the update key, as every build before the first release, has no updater and never checks. | GitHub: `github.com`, which forwards to its host for release files.                                                                                     | One request for the release feed, with Verso's and Sparkle's names and versions in the User-Agent. No system profile, and no cookie from an earlier check: the app's cookie store, which nothing else in Verso uses, is emptied before each one. An update is downloaded from the same place: when you choose to install it, or, with Install, as soon as a daily check finds it, to be applied when Verso quits. |
| Fraudulent website warning | You open a page: WebKit checks its address first, as Safari does. On, as in Safari; Verso has no switch for it yet.                                                                                                                                                                                                                 | Google Safe Browsing and Apple, through macOS's Safe Browsing service (Tencent Safe Browsing too when the Mac's region is China mainland or Hong Kong). | When the check needs it, information calculated from the address, never the address itself; Google (or Tencent) may log your IP address. This is Apple's description of the same check in Safari.                                                                                                                                                                                                                 |
| Translation language packs | You choose Translate on a page in another language whose pair of languages is not on this Mac yet, and say yes to macOS's own prompt; a pair that is there translates with no connection at all. | Apple, through macOS's Translation framework: the system downloads the languages, not Verso, as it does for the Translate app. | Which two languages were asked for. Nothing from the page, which is translated on this Mac afterwards. |
| Extension installs from the Chrome Web Store | You choose Install on an extension's page in the Chrome Web Store (`chromewebstore.google.com`), and only then: Verso checks no extension for updates, and to update one you install it again from its page. | Google: the update endpoint Chrome itself asks for an extension's package (`clients2.google.com`), which forwards to Google's file host. | One request for the package, in a session of the app's own that sends no cookie, whichever Space you are in: the extension's id from the page's address and a Chrome version number, which the endpoint requires and Verso does not have, plus Verso's name and version in the User-Agent, as the system sends it. The package is installed only if it is the extension the page names and carries the store's signature. On the store's own pages (`chromewebstore.google.com`) Verso presents itself as Chrome in the User-Agent header, since the store shows its install button to Chrome alone: the string names a Chrome version and a Mac, nothing of yours, and goes only to the store's pages you open. |
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

- Spaces (a proxy you gave one included), open tabs, their back/forward state, bookmarks,
  history, the icons of the sites you visit and the downloads list live in one SQLite file in the
  app's sandbox container under Application Support.
- A tab's back/forward state is WebKit's, and it holds what a form sent for a page that came
  from one, a password included. So a tab whose history holds such a page is saved with its
  address alone: what the form sent stays in memory while Verso runs and is never written to the
  file, and after a relaunch the tab opens on that address without its back/forward list.
- History → Clear History removes every page and visit, in every Space, with what search by
  meaning kept for them and the icons that no open tab or bookmark shows, and overwrites them in
  the file: its search index, the space they freed and its write-ahead log hold none of it
  afterwards. Open tabs, bookmarks, website data and downloads stay. Deleting one page in the
  History window takes it out of the list and the search at once; the search index only marks
  its words deleted, and SQLite drops them from the file when it next merges that part of the
  index, or with the next Clear History.
- Two more files can sit beside that SQLite file. When an update changes how the file is laid
  out, Verso first keeps a copy of it as it was, `verso.sqlite.before-<change>`, so a change that
  goes wrong loses nothing: it holds everything the file held then, history included, and is
  deleted after five launches of the new version, or as soon as you clear the history, delete a
  page in the History window or turn off search by meaning. A file SQLite finds damaged is put
  aside whole as `verso.sqlite.corrupt-<time>`, for a recovery; Verso tells you where it is and
  never deletes it.
- Settings (the search engine, the appearance, the downloads folder and how downloads are
  saved, whether content blocking is on) live in the same SQLite file, and so do the sites you
  turned content blocking off for (View → Turn Off Content Blocking for This Site), one host
  name each. One turned off in a private Space is kept in memory until Verso quits, never
  saved. A downloads folder you choose is kept as a bookmark
  macOS issues for that folder, so Verso can write there after a relaunch; it grants access to
  that folder and nothing else.
- Downloads go to your Downloads folder, or the folder you chose in Settings, under the name the
  site suggests (numbered when it is taken), unless you ask to choose the place for each one.
  Until a file is whole its bytes wait in a temporary folder on the same disk, which Verso empties
  once the download cannot continue: you cancel, remove or clear it, it fails with no way to
  resume, its Space is deleted or Verso quits, and after a crash at the next launch. A private
  Space's download is never saved, so after a crash its bytes stay in that temporary folder. A
  file you chose to replace stays until the new one takes its place.
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
  kept, with its profile's entry: its name, colour and icon, a proxy you gave it, the requests you
  block in it (the Develop palette's Requests), and that it is private; not the names of its tab
  groups. On macOS 27 its pages also send Global Privacy Control (`Sec-GPC: 1`), asking each site
  not to sell or share what it learns; the other Spaces do not. "Clear Website Data" on a Space
  removes everything its sites stored; deleting a Space removes its
  profile's store and forgets its recently closed tabs, so none of them can reopen in another
  Space.
- Web extensions you install are kept as Verso's own copy of the folder, ZIP or CRX file you
  chose, or of the package the Chrome Web Store served, under Application Support in the
  container, one folder per extension; the list of them, with what you granted each, whether it
  may see private Spaces and, for one from the store, the store's id for it, lives in the same
  SQLite file as the rest. What an extension stores for itself (its `storage.local`) is kept by
  WebKit in the container under one fixed identifier per installed extension, and removing the
  extension removes that storage with its files and its row. The website data of an
  extension's own pages and worker (their cookies, local storage and IndexedDB) is kept in
  memory and gone when Verso quits. An extension sees a private
  Space's pages, tabs and addresses only if you ticked "Allow in private Spaces" for it. A page
  an extension opens in a private window (`windows.create` with `incognito`) opens in a private
  Space, never with an ordinary Space's cookies or in its history.
- Content blocking uses EasyList and EasyPrivacy, which ship inside the app, converted when
  Verso is built; a new snapshot comes with a Verso update. Verso compiles them once per version
  into WebKit's rule-list store in its own container, and WebKit applies them inside its
  processes: no list is downloaded, and content blocking checks no page address against anything
  outside the Mac.
- A site that wants your camera, your microphone or your location, or a link that wants to open
  another app, is shown to you first, with the name of the site that asks. A no never reaches the
  system or the other app, and the page cannot ask again until it navigates.
- A download that a page starts on its own, with no click of yours (a script, a hidden frame from
  another site), saves nothing until you allow that site in the same kind of question; the answer
  holds until the page navigates. The downloads you start yourself are not asked about.
- Your location goes to a site only on macOS 27, after you said yes to that site in Verso and to
  Verso in macOS's own Location Services prompt: macOS works it out, as it does for any app, and
  WebKit hands it to the page you allowed, nowhere else. Verso keeps none of it. On macOS 26
  WebKit offers no public way to ask you, so every site's request is turned down.
- Making Verso the default browser is a setting of this Mac: after you confirm in the system's own
  alert, macOS writes it through Launch Services. Nothing is sent anywhere.
- Whether Verso checks for updates on its own and whether it downloads them too (Settings'
  Off, Notify or Install), when it last checked and a version you chose to skip live in the app's
  preferences, where the updater keeps them. A downloaded update waits in the updater's own cache
  until Verso quits or you choose Restart to Update.
- File → Import from Browser… reads Arc's sidebar file, history and favicon cache, or Safari's
  bookmarks, history and favicon cache, where those browsers keep them, only when you start an
  import; for Safari, macOS first asks you for Full Disk Access in System Settings, and the
  summary afterwards offers to take it back. The icons the other browser had cached for the
  imported pages come along, so the imported tabs, tiles and bookmarks show them without a page
  being loaded or a site being asked; such an icon is kept until the page's own replaces it. An Arc folder kept elsewhere is read from the folder you choose in the system's
  open panel. The histories are read from copies that are deleted afterwards; nothing in either
  browser's folder changes, and nothing is sent anywhere. What comes over is kept as above, with
  the browser's ids for the imported items, so a second import adds nothing twice. To offer the
  import on the first launch, Verso asks Launch Services which of the browsers are installed; it
  does not look into their folders until you start the import.
- File → Export Spaces and Bookmarks… writes, to a file where you choose in the system's save
  panel: your Spaces' names, colours, icons and proxies, their pinned tabs, bookmarks and open
  tabs (titles and addresses), your settings (but the downloads folder), the sites you turned
  content blocking off for, and the names and store ids of your extensions; your history only
  when you tick Include History. Private Spaces, the sites' data (cookies, sign-ins) and what
  extensions stored are never written. Its other format, Bookmarks for Other Browsers, writes
  the bookmarks and pinned tabs alone, as the `bookmarks.html` every browser reads. File →
  Import Spaces and Bookmarks… reads such a file, or a `bookmarks.html` from any browser, that
  you choose and that is no larger than 100 MB: the settings in a Verso file replace yours, the
  rest is added and kept as above, with the file's ids, so a second import adds nothing twice.
  Before a file sends your searches elsewhere, turns content blocking off (everywhere or for
  sites), turns the Developer switch on, or brings a Space with a proxy, Verso asks, naming each
  change; Cancel keeps yours for these and imports the rest. A search engine comes over only as
  Settings would save one (https, named after its host), tabs and bookmarks only with web or
  file addresses, and history only with web addresses. Extensions are not installed by an
  import; its summary can open their Chrome Web Store pages, where installing is your step.
  Neither command sends anything anywhere; the file is yours to keep or move to another Mac.
- A file another app hands to Verso (Open With, a drop on the Dock icon, File → Open File…) is
  kept in the same SQLite file as a read-only bookmark macOS issues for that file, so its tab
  shows it again after a relaunch; it grants access to that file and nothing else, and is
  forgotten once no saved tab or bookmark shows the file. A file opened in a private Space is
  not kept.
- The helpers under Settings → Apple Intelligence (a tab's tidied title, open tabs grouped by
  subject, a Space suggested for a new tab, a page's summary, an answer to a question about the
  page, the facts of a group's pages side by side, an action read from words you typed into the
  command bar, an explanation of text you selected, a Space's name from its tabs) run through
  Apple's on-device model, the one that powers Apple Intelligence, on this Mac: a page's title and
  host, its text for a summary, a question or a comparison you asked for, the words you typed into
  the command bar with the open tabs' titles and hosts, or the text you selected for an explanation,
  go to that model and nowhere else, and nothing of it is kept beyond the tidied title and the
  group's name saved with its tab. While Verso runs, it also remembers the model's tidied titles
  by title and host, so a reload asks nothing twice: at most 300, never a private Space's, and
  forgotten with Clear History, when you turn tidied titles or the pane's main switch off, and at
  the quit. The helpers work only while the Mac's own Apple Intelligence switch is on, and each
  has a switch of its own, but for Tidy Tabs, the comparison and the command bar's sentences,
  which run only when you ask. "Search history by meaning", in the same pane, lets the
  command bar find a page by what its title meant: it uses the sentence model macOS keeps for the
  languages your Mac is set up with (the one behind the system's own text features), not Apple
  Intelligence, so it works whether that is on or off; Verso downloads no model and sends
  nothing. The title of each of your 5,000 most recently visited pages is turned into a row of a
  few hundred numbers, kept in the same SQLite file beside the page and deleted with it, or with
  the switch. Writing Tools in a page's text fields are the Mac's, as in Safari, under the same
  system setting: what they do with text you choose there follows your choices in System
  Settings, not Verso's.
- Translate, on a page in another language (the capsule under the toolbar), hands the page's text to
  Apple's Translation framework on this Mac, which translates it with the language packs macOS
  keeps; nothing of it leaves the Mac, and nothing is kept beyond the sites you said not to ask on,
  saved with your settings. A pair of languages that is not on the Mac yet is offered for download
  by macOS's own prompt: that download is the system's, from Apple, and the table above lists it.
- Crash logs stay in the system's crash reporter; the app never uploads them.
- Everything Verso keeps lives in its sandbox container, `~/Library/Containers/codes.thees.verso`,
  except the files you download. macOS leaves the container in place when the app goes to the
  Trash; the README of the releases repository, under "Uninstall", says how to remove both.
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
| `files.user-selected.read-write`                           | File uploads, "Ask where to save", File → Save Page As…, a downloads folder chosen in Settings and an Arc folder kept somewhere other than Arc's own, through the system panels only; the chosen downloads folder is kept as a security-scoped bookmark, the Arc folder is not kept. |
| `cs.allow-jit`                                             | WebKit's JavaScript engine compiles JavaScript to machine code.                                                                                                       |
| `device.camera`, `device.microphone`, `device.audio-input` | Only when a site asks and you allow it, with the system prompt. `device.audio-input` is the microphone's key under the hardened runtime of the signed builds.         |
| `personal-information.location`                            | Only when a site asks for your location and you allow it, in Verso and then in the system prompt (macOS 27).                                                          |
| `print`                                                    | Printing a page.                                                                                                                                                      |
| `temporary-exception.mach-lookup.global-name`              | Updates: the updater talks to the installer it starts outside the sandbox, by the two names Sparkle documents for sandboxed apps (`<bundle id>-spks`, `-spki`).       |
| `temporary-exception.files.home-relative-path.read-only`   | File → Import from Browser… reads `~/Library/Application Support/Arc/` and `~/Library/Safari/`, read-only, only when you start an import. Safari's folder also needs Full Disk Access, which you give in System Settings and can take back right after; Verso asks for it and says so. |

A new entitlement is added in the same pull request as its row here. The UI-test runner, which never
ships, holds one more: `network.server`, to serve the test pages on 127.0.0.1 to the app it drives.
The passkey entitlement for websites (`com.apple.developer.web-browser.public-key-credential`) is
prepared but off until Apple grants it (`App/Passkeys.xcconfig`); its row joins the table when it is
turned on.
