# TubePress Changelog

Release history of the TubePress CMS. Canonical page: [tubepress.io/changelog](https://tubepress.io/changelog) - download: [tubepress.io/download](https://tubepress.io/download).

## 1.0.66 - 2026-07-30

- Storage upload errors now say how long the file took to send and how long the storage server then took to answer.
- When an upload fails, the message separates the two: how many seconds the file spent going out and at what speed, and how many seconds were then spent waiting for the storage server to reply. A slow connection and a slow storage server produce very different numbers there, and they need different fixes. If you report a storage problem, that one line is enough for us to tell them apart.

## 1.0.65 - 2026-07-30

- More reliable uploads to S3 storage, and error messages that actually say what happened.
- Large files are now uploaded in 8 MB pieces instead of 128 MB, matching what the AWS command line tool and the official S3 libraries do. Each piece is confirmed within seconds rather than holding a single connection open for minutes, which is far more forgiving of firewalls, proxies and busy storage servers.
- A single upload that loses its connection is now retried up to three times before giving up. Previously only multi-part uploads retried, so one dropped connection could lose a preview or a rendition outright. Errors that will not change on a second attempt, such as a rejected key, still fail immediately instead of retrying pointlessly.
- When an upload does fail, the message now tells you how much of the file actually left your server, out of how much, how long it took and at what speed. That single line usually identifies the cause on its own - whether the file was sent completely and the storage server then went quiet, or whether the connection was cut part way through. If you contact support about a storage problem, copying that one line is now enough.

## 1.0.64 - 2026-07-30

- Faster and more reliable deletion everywhere, not just for videos.
- Galleries now delete in batches like videos do. Measured on albums of 40 images each: about 600 albums per second, and remote storage is cleaned in one request per thousand files instead of one request per image - deleting 400 galleries went from 16,400 separate storage requests to 17.
- Deleting a gallery used to leave its comments, reports, likes, view history and statistics behind for ever, because those rows were never removed. They are now cleaned up, which also stops those tables growing without bound.
- Every bulk delete screen - tags, categories, pornstars, channels, their gallery equivalents, comments, pages and users - now deletes in batches instead of row by row, and reports what was really deleted. Previously the confirmation always showed the number you had selected, even if some or all of them had failed, and a large selection could silently stop half way through when the page ran out of time. If that happens now it tells you how many were left so you can finish the job.

## 1.0.63 - 2026-07-30

- Bulk video deletion is much faster and more reliable.
- Deleting videos in bulk was processing them one at a time, with dozens of database queries per video and, on remote storage, fourteen separate delete requests per video. Videos are now deleted in batches: measured on a 5,000-video library, bulk deletion went from about 13 videos per second to several hundred, and remote storage is cleaned in a single request per batch instead of one per file.
- The batch size now tunes itself to your server. There is no setting to adjust: each batch is timed and the next one resized accordingly, so a small shared host takes smaller bites and a powerful server takes larger ones.
- Reliability fixes in the same area:
- A delete job could report "completed" when a batch had actually failed, leaving videos behind.
- With two administrators deleting at once, one progress bar could advance the other's job.
- A fast job could stop after its first batch.
- Long jobs stopped after ten minutes until an admin page was reopened; they now continue on their own.
- Deleted videos left rows behind for ever in the view log, the CTR statistics tables and the homepage pool. These are now cleaned up, which also keeps those tables from growing without bound.

## 1.0.62 - 2026-07-30

- Large file uploads to S3 storage.
- Files over 64 MB are now uploaded in parts instead of a single request. This removes the previous 5 GB per-file limit (a hard limit of the storage protocol, not a setting), and lets very large videos - up to several terabytes - be stored.
- It also makes large uploads far more reliable: each part is confirmed as it goes, and if one part fails it is retried on its own instead of restarting the whole file. Upload speed is unchanged.

## 1.0.61 - 2026-07-29

- Fixes large video uploads to S3 and other remote storage.
- Large uploads could fail with "Operation too slow" even when the transfer was perfectly healthy. A storage endpoint sitting behind a buffering gateway (nginx and similar) stays silent between the last byte it receives and its reply, for as long as it needs to store the object - several minutes for a large video. The CMS treated that silence as a dead connection and gave up after two minutes. The stall check now only applies while data is still being sent, so the storage server is free to take the time it needs.
- Small files were never affected, which is why the storage Test could pass while every video import failed.

## 1.0.60 - 2026-07-29

- Remote storage (S3) reliability and diagnostics.
- Upload failures now report the storage server's own error - SignatureDoesNotMatch, NoSuchBucket, AccessDenied, a connection error and so on - in the import log, the storage health check and Settings -> Storage -> Test. Previously these all showed a generic "Failed to upload ... to storage server" or a bare HTTP code, which made a misconfigured storage very hard to diagnose.
- Fixed: any file whose name, folder or storage prefix contained a space or a non-ASCII character was never uploaded to S3. Object paths are now percent-encoded in both the request URL and the SigV4 signature. This mainly affected album images and video assets, which keep their original filename.
- Fixed: the storage prefix was applied when writing objects but ignored when building playback URLs, so on any storage with a prefix the upload succeeded and every file returned 404 on playback.
- The Storage Test now also performs a 2 MB write through the real upload path and shows the request URL it used. A passing test can no longer hide a failing import, because the test and the import finally exercise the same code.
- Fixed: the host/port signing fix shipped in 1.0.59 was not applied to the backup plugin, so backups to a storage endpoint with an explicit port kept failing.
- Hardened: a missing or unreadable local file during an upload now fails cleanly instead of raising a fatal error.

## 1.0.59 - 2026-07-29

- S3 storage uploads fixed
- Imports no longer fail with "Failed to upload video.mp4 to storage server" when your S3-compatible endpoint URL includes a port (for example MinIO on :9000).
- Large video uploads to S3 are no longer cut off after 5 minutes and now run to completion as long as data keeps flowing.
- The storage Test button now validates these endpoints correctly, and failed S3 uploads are recorded in the PHP error log with the HTTP status for easier diagnosis.

## 1.0.58 - 2026-07-28

- Top up your credits with cryptocurrency
- The Top Up window now offers the payment methods your TubePress account actually has enabled, instead of stalling on an empty Payment step when card payment is unavailable.
- Choose from more than 50 coins and networks, with a search box and the exact amount to send — tax included — shown before you commit to anything.
- The payment step gives you a QR code, the deposit address and the amount with one-click copy, plus the memo or tag as a required field on the networks that need one.
- Your transfer is tracked live and the credits are added automatically once the network confirms, so you can close the window and come back later.
- Anything that goes wrong is now explained inside the window — coin unavailable, amount below that coin's minimum, service unreachable — instead of failing silently.

## 1.0.57 - 2026-07-22

- Import: clearer CPU heads-up before transcoding
- When importing from the catalogue, choosing "Stream smoothly" or "Shrink to save space" now warns you — inline in the option's summary — if no conversion server is set up, since encoding then runs on this server and uses most of its CPU.
- Links to Settings → Transcoding → Servers so you can offload encoding to a dedicated worker.

## 1.0.56 - 2026-07-22

- Import: clearer heads-up before transcoding
- When importing from the catalogue, choosing "Stream smoothly" or "Shrink to save space" now warns you if no conversion server is set up — encoding then runs on this server and uses most of its CPU while it works.
- The notice links to Settings → Transcoding → Servers so you can offload encoding to a dedicated worker.

## 1.0.55 - 2026-07-15

- Admin sidebar: support panel opens again and cleaner menu width
- The support button in the admin sidebar now opens your tickets again.
- The sidebar no longer shows an empty strip next to the menu, so its width matches the menu items.

## 1.0.54 - 2026-07-15

- Admin sidebar polish and internal cleanup
- The admin sidebar no longer becomes narrower the moment its menu grows long enough to scroll: the scrollbar space is now always reserved and the bar itself is a slim, dark-themed 6px.
- Internal source cleanup across the whole codebase — smaller download and update packages, no functional changes.

## 1.0.53 - 2026-07-15

- Much smoother pages on phones and low-end devices
- Video thumbnails no longer create a hidden video player for each card at page load. The hover preview player is now created the moment you first hover a card — previews look and behave exactly as before, while pages load lighter and use far less CPU, GPU and memory.
- Removed the per-card frosted-glass blur effects (duration badge, play icon) and scoped the blurred sticky header to desktop-class devices — long video grids now scroll smoothly on mobile.
- Bundles Simply theme 1.0.16, which carries these performance fixes.

## 1.0.52 - 2026-07-14

- Localized URLs now work in every language
- Fixed 404 errors on the category, tag and pornstar section pages in languages where the translated URL word is identical for singular and plural (Malay, Indonesian, Vietnamese, Czech, Hungarian, Italian).
- Fixed video pages, likes, comments and recommended-video loading returning 404 in Bulgarian, Indonesian, Italian, Malay, Russian, Serbian, Ukrainian and Vietnamese.

## 1.0.51 - 2026-07-13

- Per-language translation pricing and reliable multi-language imports
- Catalogue AI translations are now billed per target language: the import dialog shows the per-language price and the exact total for your enabled languages, for both titles and descriptions.
- You can now translate into up to 30 languages per import (video imports were previously capped at 8).
- Translations run in small batches of up to 5 languages per request, with automatic retries for any missing language — large language sets import reliably instead of failing as one giant request.
- Collected translations are saved as they arrive, so an interrupted import resumes without losing finished translations.
- The purchase summary now itemizes every AI option with live prices, including the description-translation line.

## 1.0.50 - 2026-07-11

- Installation now works on MySQL 8
- Fixed the setup wizard failing at the database step on MySQL 8 with a misleading "user privileges" error — three migrations used MariaDB-only SQL syntax that MySQL rejects.
- Existing sites on MySQL automatically repair the affected album features (comments, reports and taxonomy counters) when applying this update.

## 1.0.49 - 2026-07-11

- Help Center: album layout & sidebar guide
- The in-app Help Center now documents the new album listing options — default Grid/Masonry display, Pages/Infinite navigation and the show/hide sidebar filters — with a one-click link to Appearance > Layout. Search "masonry", "infinite" or "sidebar" to find it.

## 1.0.48 - 2026-07-11

- Album layout defaults (fresh installs)
- New Appearance > Layout > Albums options are pre-seeded on fresh installs: default album display (Grid/Masonry), default navigation (Pages/Infinite) and which sidebar filters show. Existing sites are unaffected and keep their current look.

## 1.0.47 - 2026-07-11

- Reliable bulk delete for videos
- Deleting videos — one by one, or in bulk from the Videos list — is now reliable. Before, removing a video that had any category, tag, performer or channel could silently do nothing.
- Bulk delete now runs straight from your browser with a clear progress bar, so it finishes dependably on every host (no background worker required).

## 1.0.46 - 2026-07-10

- Instant, parallel downloads + close/delete your tickets
- Imported videos now start downloading immediately and respect your chosen number of parallel downloads, even on brand-new sites with no visitors yet.
- You can now close a ticket (a reply reopens it) or permanently delete one, from a menu in the ticket header.
- Clicking the support chat icon when you have replies now always opens your most recently answered ticket.

## 1.0.45 - 2026-07-10

- Much faster catalogue browsing + smarter reply alerts
- The Import catalogue now loads and switches between Videos/Galleries and Straight/Gay/Hentai near-instantly: pages are cached and prefetched in the background, with a smooth loading skeleton.
- Unread support-reply alerts now stay visible until you actually open the reply — navigating between admin pages no longer clears them — and replies that were already waiting for you light up even on a fresh browser.

## 1.0.44 - 2026-07-10

- One-click support chat in the sidebar
- A blue chat button now sits right next to Help Center in the sidebar — one click opens your support tickets directly.
- When our team replies, that chat button pulses with an unread counter and clicking it opens the reply straight away; Help Center itself is back to being your documentation hub.

## 1.0.43 - 2026-07-10

- Support panel stays open + instant reply alerts
- Your open support ticket now stays on screen while you move between admin pages — only the X button closes it, and reply drafts are kept per ticket until you send them.
- When our support team answers you, the red Help Center button in the sidebar now pulses with an unread counter — one click opens the reply directly.
- Unread replies are also highlighted in the ticket list, and indicators refresh automatically while you work.

## 1.0.42 - 2026-07-10

- Polished default appearance for new installations
- New installations now start with a refined out-of-the-box look — colours, dark mode, layout, sorting, video cards, watch page, and the navigation menu (including Albums) — plus a default logo and favicon.
- Photo-album listings and cards now have sensible built-in defaults.
- The catalogue download queue now defaults to 3 parallel downloads.

## 1.0.41 - 2026-07-03

- Security and robustness hardening
- Hardened the built-in static-file fallback so it only ever serves files from your theme and template asset folders.
- The internal keep-alive ping now targets your configured Site URL instead of the incoming request Host header.

## 1.0.40 - 2026-07-01

- Reliability hardening: the CMS now transparently retries a database statement on a transient deadlock instead of showing an error page under heavy concurrent traffic, and a harmless update warning was silenced. Part of a full public-readiness QA pass (no security or logic issues found).

## 1.0.39 - 2026-07-01

- Licensing fix: the vendor's own domain (and its sub-domains) are recognised as first-party, so a self-hosted demo/showcase on the marketplace's own domain is never incorrectly warned that a paid plugin 'does not cover this domain'. No effect on normal customer installs.

## 1.0.38 - 2026-06-30

- Albums feature toggle (Settings > General) to cleanly enable/disable galleries site-wide (SEO-safe 410 + removed from menu/search/sitemap when off); profile now shows your favorite & liked Albums via Videos|Albums sub-tabs; new Albums section in the Help Center; Server Health now checks file/folder permissions (and these are sent with support tickets); album categories/tags/pornstars/channels added to the sitemap; new strings translated into all languages.

## 1.0.37 - 2026-06-30

- Albums import parity & admin fixes: Upload File and Feed Import now create Albums (galleries) as well as videos, with a sample feed in Feed Import; fixed the album add/edit error; album category & channel lists show representative cover thumbnails; Data Purge now covers Albums and album taxonomy; removed the 8-language limit; update restore points now show each backup's version.

## 1.0.36 - 2026-06-30

- Watch page: performer chips now show the model's image (best-video thumbnail, CTR-ordered) instead of an initial; dashboard widgets resolve thumbnails correctly (storage_id for video widgets, best-video fallback for Top Performers) — no more 'No img'.

## 1.0.35 - 2026-06-30

- Album CTR support: AlbumAsset exposes image filenames + album archives render a CTR-aware fallback thumbnail (best gallery cover). Pairs with CTR plugin 3.1.0 + Simply 1.0.11.

## 1.0.34 - 2026-06-30

- Albums become first-class in the admin: a Recent Albums + Top Albums dashboard widget; an independent ad surface (Gallery Page + Gallery Listings zones in /admin/ads); installed-theme thumbnails now render (screenshot.webp support); and every album-page string is translated into all 30 CMS languages. Comments & reports already cover albums.

## 1.0.33 - 2026-06-30

- Appearance admin: Layout gains an Albums tab (albums per page / per row / default sort incl. Most Images). Cards & Watch tabs renamed with nested Videos/Albums sub-tabs (transcode design): album card options (cover badge, title lines, hover, views) + single gallery-page display toggles (views/date/likes/favorites/pornstars/channels/categories/tags), lightbox download, and a related-galleries count. /albums + the gallery page now honor these settings.

## 1.0.32 - 2026-06-30

- Albums reach full parity with videos: report a gallery, like/dislike, and comments (guest + user, moderation, replies) — all in the same admin inboxes as videos. Gallery search on /albums. Mobile-optimized album browsing. Every album/gallery string is translatable in Admin > Languages. Builds on the catalogue gallery purchase + AI import wizard. Migrations 116-120.

## 1.0.31 - 2026-06-30

- Albums + Galleries release. Report a gallery exactly like videos (unified inbox). Catalogue galleries are purchasable with a 3-step import wizard — choose what to import (description/categories/tags/pornstars/channels/views) + optional AI rewrite/translate of title & description (background, #kimi). Albums added to the navigation menu (after Channels). Separate album taxonomy. Update mechanism now delivers bin/ + public/index.php and self-heals the DB schema on update. Migrations 116-119.

## 1.0.30 - 2026-06-30

- Albums now have their OWN separate taxonomy — dedicated Categories, Tags, Pornstars and Channels for albums, fully independent of your video taxonomy. Manage them under Admin > Albums (Categories / Tags / Pornstars / Channels), with front-end archive pages (/album-category, /album-tag, /album-pornstar, /album-channel) and configurable SEO patterns.
- Also bundles important reliability fixes: in-app updates now correctly run database migrations (a prior bug silently skipped them) and self-heal the schema on the next page load; new feature routes are delivered on update; the Studios-to-Channels rename is reconciled automatically; the ad-zone migration is idempotent. After updating, allow one page load for the database to finish applying.
- Pair with Simply theme 1.0.5 (album archive templates).

## 1.0.28 - 2026-06-30

- Admin: the sidebar "Albums" entry is now a collapsible group with quick access to the shared taxonomy (Categories, Tags, Pornstars, Channels), mirroring the Videos group. (The Albums content type and the Studios-to-Channels schema fix shipped in 1.0.27.) Pair with Simply theme 1.0.4 to get the Albums front-end and the Admin > SEO album panels.

## 1.0.27 - 2026-06-30

- Albums — a new image-gallery content type.
- Admin: create and manage albums (multi-image upload, cover selection, per-language translations, bulk actions), with a new Albums entry in the sidebar, a dashboard card + quick action, and an Albums count column in the Categories / Tags / Pornstars / Channels lists.
- Front-end (your active theme): an /albums listing and single-gallery pages with a responsive, count-adaptive photo grid, a built-in lightbox, favourites, copy-link, related galleries and previous/next navigation.
- Albums reuse your existing categories, tags, pornstars and channels, are fully translatable (Admin > Languages) and SEO-configurable (Admin > SEO): title/description patterns, a noindex toggle, the XML sitemap and ImageGallery structured data. Site search now returns matching albums too. Designed to scale to large libraries.
- Also includes an important schema fix: the Studios-to-Channels rename is now reconciled automatically on install and update, so fresh installs and upgrades correctly create the channels tables (resolves 'Table channels does not exist' 500 errors on the video editor and imports). The migration is idempotent and preserves all existing data.

## 1.0.26 - 2026-06-29

- Galleries dans l'Import : une bascule Vidéos/Galleries ouvre le catalogue d'images PornPics — source PornPics, catégories, recherche pornstars/chaînes/tags et filtre par nombre d'images, avec une visionneuse plein écran. Navigation seule (pas encore d'import). Technique : le template _import.php (287 Ko) a été découpé en partials plus petits (markup vs logique, catalogue vs uploads).

## 1.0.25 - 2026-06-29

- Command-line and automated installation
- TubePress can now be installed non-interactively from the command line with "php bin/install.php" — it provisions the database, admin account and settings in one step, so the CMS can be deployed by a script or an AI agent with no browser.
- The browser-based setup wizard is unchanged; both paths share the same install logic and produce an identical site.

## 1.0.24 - 2026-06-28

- Clear errors in the admin instead of a generic failure page
- When something fails while you're signed in to the admin, the panel now shows the exact error — what went wrong and where — with a reference you can copy, instead of a vague "something went wrong" page.
- Background and bulk actions (such as importing) report the underlying error too, so a failed import is never silent.
- Visitors to your public site still see a neutral page with no technical details exposed.

## 1.0.23 - 2026-06-28

- Performer, channel and category photos now show in the admin
- The Performers, Channels and Categories admin lists show each item's representative image again — it was blank on sites whose media lives on a remote CDN.
- When the CTR Ranking plugin is active, that image is the most engaging video's thumbnail; embed videos (no downloaded file) are supported too.

## 1.0.22 - 2026-06-28

- Smoother updates and a lighter support form
- Admin → Updates now makes clear that a single update installs the latest version directly and lists the combined release notes, so you never apply each version one by one.
- Contacting support is quicker: providing admin access is now optional and collapsed by default when you open a ticket.

## 1.0.21 - 2026-06-28

- Le prix des téléchargements vidéo et des opérations IA (rewrite, traduction) est désormais défini côté place de marché (Admin > Réglages > onglet Price) et non plus codé en dur dans le CMS. Le site importateur n'envoie que les opérations demandées : la place de marché les tarife (facturation autoritaire, le site ne peut plus se sous-facturer). L'UI d'import affiche les prix configurés en direct.

## 1.0.20 - 2026-06-28

- Correctif : la fausse alerte « ce domaine n'est pas couvert » déclenchée par un échec de vérification ponctuel (blip réseau/marketplace) est corrigée — il faut désormais 2 échecs consécutifs avant tout avertissement (les clones restent détectés et désactivés). Inclut l'UX de renouvellement des mises à jour expirées (CTA dans Admin > Mises à jour) et le nettoyage des commentaires décoratifs du code.

## 1.0.19 - 2026-06-28

- Clearer updates & licensing for paid add-ons
- Paid plugins and themes include one year of updates with every purchase, and your installed version always keeps working — even after that window ends.
- When your update window has ended, the Updates page now shows the new version with a one-tap "Renew to update" link instead of a confusing download error.
- Fewer false "licence problem" warnings (briefly-flaky network checks are now ignored).

## 1.0.17 - 2026-06-28

- Theme and admin assets now load on every web server out of the box
- The CMS now serves its own theme, admin and installer styling when the web server doesn't, so installs look correct on Apache, nginx or the PHP built-in server with no extra configuration — and existing sites get the fix on update.
- Local development now works over plain HTTP — run: php -S localhost:8000 -t public public/router.php and open http://localhost:8000 (no HTTPS required).
- Corrected the installer's theme/template symlink target and a fingerprint cookie that did not persist on plain-HTTP (localhost) installs.

## 1.0.16 - 2026-06-28

- Theme and admin assets now load on every web server out of the box
- The CMS now serves its own theme, admin and installer styling when the web server doesn't, so a fresh install looks correct on Apache, nginx or the PHP built-in server with no extra configuration.
- Local development now works over plain HTTP — run: php -S localhost:8000 -t public public/router.php and open http://localhost:8000 (no HTTPS required).
- Corrected the installer's theme/template symlink target and a fingerprint cookie that did not persist on plain-HTTP (localhost) installs.

## 1.0.15 - 2026-06-28

- Accurate, consistent disk usage across the admin
- The header storage gauge now measures the disk where TubePress is installed — its code, uploads and media — instead of the server's OS root partition, so it matches the figure shown on Settings → Storage and the dashboard.
- Local storage usage is now read live from the filesystem, so full-disk warnings and automatic storage rotation react immediately instead of waiting for the periodic health check.

## 1.0.14 - 2026-06-28

- More reliable updates with a paginated history
- The Updates page now verifies the version installed on your site before offering or applying anything, so an update you've already installed is never shown or applied again.
- Update History & Rollback is now paginated, keeping a long list of restore points easy to browse.
- Applying an update now starts with a single click — the extra confirmation prompt has been removed.

## 1.0.13 - 2026-06-28

- Cleaner Updates screen
- The Update History & Rollback panel now lists only your available backups, each with a one-click Rollback, instead of a long repetitive update log.
- Added spacing on the Updates page so the up-to-date notice and the history panel are no longer cramped together.

## 1.0.12 - 2026-06-28

- Fix update history not loading, and allow rolling back the CMS core
- The 'Update History & Rollback' panel in Admin → Updates now loads correctly instead of showing 'Failed to load history'.
- You can now roll back the CMS core to a previous backup directly from that panel, alongside plugins and themes.

## 1.0.11 - 2026-06-28

- Help Center: new guide for automatic updates
- A new "Automatic updates" article in the Help Center explains the Settings → General toggle: it auto-applies signed CMS core and free plugin updates, backs each one up and rolls back automatically if it fails, keeps themes and paid plugins manual, and is off by default.
- The guide is also surfaced as contextual help on the Updates page.

## 1.0.10 - 2026-06-27

- Installer now works on shared hosting and every PHP 8.2+ build
- Setup installs into a database your hosting panel pre-created even when the database user cannot create new databases (the usual cPanel/Plesk case), and shows a clear message if it genuinely cannot.
- The database host field now accepts a port or socket (e.g. db.host:3306), so managed and remote MySQL connect without extra steps.
- Creating the admin account no longer requires the Argon2 extension — it uses bcrypt automatically when Argon2 is missing, so installation succeeds on any PHP 8.2+ server.
- Setup detects HTTPS correctly behind Cloudflare and other reverse proxies, checks that PHP sessions are writable, and creates its writable folders automatically.

## 1.0.9 - 2026-06-27

- Le rapport de santé du serveur est joint automatiquement à chaque demande de support, pour un meilleur diagnostic.

## 1.0.8 - 2026-06-27

- Removed a placeholder theme update from the Updates screen
- The Updates page no longer shows a sample "Simply" theme update that was not a real update.

## 1.0.7 - 2026-06-27

- Licence enforcement for paid plugins and themes
- Paid plugins and themes are now valid on a single domain, including all of its sub-domains.
- A site copied to another domain is detected and its paid items are disabled, with a notice shown in the admin.

## 1.0.6 - 2026-06-27

- Faster update detection
- The CMS now checks for updates every 90 seconds, so a new version appears in Admin → Updates almost immediately.

## 1.0.5 - 2026-06-27

- Clearer Updates entry in the admin sidebar
- Redesigned the sidebar Updates item as an aligned card with an icon, a subtitle and a count badge.

## 1.0.4 - 2026-06-27

- Scheduled automatic updates
- When enabled, automatic updates now apply once a day at 1 AM in your site's timezone, instead of throughout the day.

## 1.0.3 - 2026-06-27

- One-click automatic-update toggle
- Settings now has a switch to enable or disable auto-applying CMS core and free plugin updates (off by default).
- Updates are signed, backed up and auto-rolled-back on failure; themes always update manually.

## 1.0.2 - 2026-06-27

- Opt-in automatic updates
- Enable automatic updates in Settings to auto-apply signed CMS core and free plugin/theme updates.
- Every update is backed up first and rolled back automatically if it fails.

## 1.0.1 - 2026-06-27

- Live support chat updates in real time
- Incoming replies now appear instantly, without refreshing the page.

## 1.0.0 - 2026-06-27

- First public release of the TubePress CMS.
- A free, self-hosted CMS for adult tube sites on modern PHP 8.2+ and MySQL/MariaDB — no ionCube, no Composer, no Node. Includes: bulk CSV/JSON import, the licensed video Catalogue with one-click import, automatic CTR ranking, AI title/description rewriting and translation into 30+ languages, built-in SEO (XML sitemaps, JSON-LD, hreflang, canonicals), a full advertising server, age-gate, comments with spam protection, FFmpeg transcoding, themes and a WordPress-style plugin system, scheduled backups, and a 5-minute web installer.

