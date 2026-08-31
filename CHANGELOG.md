# TubePress Changelog

Release history of the TubePress CMS. Canonical page: [tubepress.io/changelog](https://tubepress.io/changelog) - download: [tubepress.io/download](https://tubepress.io/download).

## 1.1.25 - 2026-08-31

- The custom top-up amount can be paid again
- Typing your own amount in the top-up window and pressing Continue now opens the payment step. The button did nothing at all before, so only the four fixed credit packs could be bought.

## 1.1.24 - 2026-08-31

- Fixes the unstyled admin some sites saw after updating to 1.1.23
- If you updated to 1.1.23 and your admin panel lost all its styling, install this update — it restores it. Nothing else on your site was affected.
- Cause: 1.1.23 placed the new compiled stylesheet and scripts in a folder that the updater already running on your site does not install. The files were in the package but never reached your server, so the stylesheet returned 404.
- They now ship in a location every version of the updater has always installed, and which your web server serves directly. Verified end to end on a real install.
- All the 1.1.23 performance work is unchanged and still applies.

## 1.1.23 - 2026-08-31

- Admin panel: 4.5x faster to load, and no longer depends on any third-party CDN
- The admin stylesheet is now compiled and shipped with the release. It used to be generated inside your browser on every page load by the Tailwind Play CDN — a 407 KB compiler that re-ran on every DOM change. On the Import screen this cut DOMContentLoaded from 1250 ms to 277 ms and JavaScript CPU time from 251 ms to 14 ms, measured on the same machine.
- About 440 KB of admin JavaScript (Import, help panel, support bubble, marketplace) now loads from cacheable files instead of being inlined into every page, so your browser downloads and parses it once instead of on every navigation. The Import page's HTML dropped from 664 KB to 415 KB.
- Country flags are served from your own install — the admin no longer loads anything from a third-party CDN.
- Catalogue thumbnails are cached on your server. Opening the Import screen used to re-download every thumbnail through PHP on every visit.
- Admin background requests no longer hold the PHP session lock while they contact tubepress.io. One slow call used to block every other admin request behind it — most visibly, the admin froze while an import job was running.
- Catalogue pricing refreshes in the background instead of delaying the Import page.
- The video preview player is downloaded only when you open a preview.
- The updater now installs new admin asset folders, which previously only arrived with a fresh install.

## 1.1.22 - 2026-08-31

- Buy credits with any amount, and see the free credits you get
- The Top Up panel now shows what each pack really gives you: the credits you pay for, the credits offered on top, and the bonus percentage.
- You can now enter any amount instead of picking a pack. The free credits are applied automatically on the same scale as the packs, so an amount matching a pack's price gives you exactly what that pack gives.
- The credits are recalculated as you type, and the order summary tells you how many of them are free before you pay.

## 1.1.21 - 2026-08-29

- Missing ad zones restored on the Advertising page
- The Player, Homepage, Categories and Performers tabs in Admin → Advertising were empty on new installs because their ad zones were never created. They are now, so VAST pre-roll / mid-roll / post-roll, the pause ad and the player popunder can be set up again.
- The In-Player Ads zone now appears under the Player tab instead of Watch Page, and the Interstitial, In-Page Push, Before Comments, Between Related Videos and Top of Listings zones are back.
- Existing zones and ad spots are untouched by the update.

## 1.1.20 - 2026-08-28

- Long transcodes no longer restart from the beginning
- Large 2160p/4K jobs are now watched by their actual encoding progress instead of a fixed time limit, so a video that needs 3, 4 or 6 hours finishes instead of being reset to 0% and re-queued after about two hours.
- A job that has genuinely stopped responding is still recovered just as quickly, and remote conversion servers are covered by the same change.
- Intro/outro assembly and preview-timeline generation now report progress while they run, so long videos are no longer interrupted during those steps.

## 1.1.19 - 2026-08-23

- Help Center now covers the taxonomy indexing threshold
- The in-admin Help Center still described only the older whole-type noindex switches. Its "noindex thin pages", "Sitemap" and "Tags" articles now explain the per-page "Minimum items required to index a taxonomy page" setting introduced in 1.1.18 — what it does to the page and to the sitemap, that it reverts on its own once a page grows, and that the panel counts the effect live before you save. Searching the Help Center for "threshold", "minimum items" or "discovered not indexed" now finds it.

## 1.1.18 - 2026-08-23

- Hide taxonomy pages that have too little on them
- Admin → SEO → Indexing now carries a "Minimum items required to index a taxonomy page" setting. Under that number a category, tag, pornstar, channel or album-taxonomy page is still served normally to visitors and keeps every internal link, but it is marked noindex, follow and left out of your sitemap, so search engines spend their crawl budget on your videos and albums instead.
- A page becomes indexable again and returns to the sitemap on its own as soon as it reaches the number. Nothing is stored per page and nothing has to be switched back by hand.
- An option keeps any category, pornstar or channel carrying at least 50 characters of its own description indexable even under the number, because that text is content in its own right. It is read from your default language, so a page can never be indexable in one language and withheld in another. Tags have no description field and always follow the number.
- Before you save, the panel shows live from your own database how many pages each taxonomy type would hide.
- The setting ships switched off, so an install that updates and changes nothing renders and generates exactly what it did before.
- Also fixed: on an install whose theme does not provide the SEO page, the "Discourage search engines from indexing this site" box in Settings → Branding always displayed as unticked whatever the stored value was, so saving that page could silently make a hidden site visible to search engines again.

## 1.1.17 - 2026-08-23

- Multilingual SEO: non-canonical slug aliases now redirect
- In a non-default language, reaching an entity by its base (untranslated) slug returned 200 with a canonical pointing at the translated URL. That alias now returns 301 to the canonical translated URL, so the duplicate consolidates instead of competing.
- Applies to /video, /category, /tag, /pornstar, /channel, /album and the four album taxonomy routes; the query string (pagination, sort) is preserved.
- Matches the behaviour the /videos/... listing routes already had. The default language, pages with no translated slug, and /embed/ URLs are unchanged.

## 1.1.16 - 2026-08-23

- Multilingual SEO: consistent language-root URLs
- A language home page now emits the same slashless URL in its hreflang tags, og:url and language-switcher links as in its canonical (https://example.com/pl, not https://example.com/pl/). The self-referencing hreflang no longer contradicts the canonical.
- The sitemap advertises the same slashless language roots, so Search Console stops reporting them as alternates pointing to a different canonical.
- The default-language root keeps its bare slash (https://example.com/), and secondary video-type roots (/gay, /hentai) follow the same rule.

## 1.1.15 - 2026-08-21

- Public pages no longer fail with a database collation error
- On servers where the database connection collation differs from the character set default, the ad scheduling filter compared dates as text and the database refused the comparison (error 1267 Illegal mix of collations), which returned Something went wrong on every public page, even with no ad spots configured.
- Ad scheduling now compares real date and time values, so start and end windows behave exactly as before on every server.

## 1.1.14 - 2026-08-16

- Cost calculator now quotes your real credit rate
- The import cost calculator converted credits to dollars at a fixed rate and named a credit pack that is no longer offered. It now reads the live rate from your account, so its dollar estimate matches what you are charged and follows any price change automatically.

## 1.1.13 - 2026-08-15

- Dashboard Views Trend now keeps a real 30-day history
- Daily view totals are recorded as views are counted, so Today, Avg/day, Peak, 30d total and the 30-day chart no longer drop during the day.
- The view de-duplication log keeps its short retention, so the fix adds no meaningful database growth.
- The 30-day history builds forward from this update; days before it show no data.

## 1.1.12 - 2026-08-12

- Faster category, pornstar and channel thumbnails
- Listing pages no longer rank an entity's entire video library to pick its cover image, so the header menu, the home page and the category, pornstar and channel listings load far faster on large sites.
- The thumbnails chosen are unchanged - only the work needed to find them is.

## 1.1.11 - 2026-08-08

- GPU transcoding now completes on NVIDIA, AMD, Intel and Apple conversion workers
- The worker's GPU command dropped the output pixel format, so a source that was not already 8-bit 4:2:0 (10-bit HEVC, 4:2:2 footage) was handed to the GPU encoder in a format it cannot accept and the encode was refused.
- When a GPU encode was refused the worker retried on CPU, produced a valid file, then still reported the job as failed and discarded it. Those jobs now complete.
- The CPU retry now uses the quality settings configured for that format instead of a fixed fallback, so the fallback rendition matches the one you set up.
- The worker console and the job error message now carry FFmpeg's own reason when a GPU encode is refused.
- After updating, re-download transcode-worker.php from Settings > Transcoding > Servers and restart your worker: that script runs on your own conversion server, so a CMS update alone does not update it.

## 1.1.10 - 2026-08-08

- GPU encoding now works on transcode workers
- The transcode worker now correctly detects NVIDIA NVENC, AMD AMF, Intel QSV and Apple VideoToolbox instead of silently encoding everything on the CPU: the test clip it used to check the encoder was smaller than the minimum frame size NVENC accepts, so the check failed even on a perfectly working GPU.
- A working GPU is no longer rejected just because FFmpeg printed a harmless warning while that check ran.
- When an encoder genuinely is unavailable, the worker now prints FFmpeg's own reason for it instead of a bare "not functional".
- After updating, re-download transcode-worker.php from Settings → Transcoding → Servers and restart your worker so it picks up this fix.

## 1.1.9 - 2026-08-08

- Signed media links no longer break on S3 storage with a prefix
- When a storage uses an S3 prefix together with Secure URLs, video, album and thumbnail links were redirected to an address without that prefix and returned "not found"; signed links now point at the real file. Storages without a prefix, and local, FTP and WebDAV storages, are unchanged.

## 1.1.8 - 2026-08-08

- Credits come back when an AI translation fails
- AI Translate now returns the credits for anything a translation job was charged for but never delivered — a job that fails, that you stop, or that the AI service could not complete refunds what it did not produce, on its own.
- Translations you did receive are never refunded, each charge is refunded once and never more than it cost, and running Retry failed after a refund is charged again.
- The job summary now shows how much went back next to how much was used.
- Includes the 1.1.7 related-videos fix.

## 1.1.7 - 2026-08-08

- Related videos no longer scan the whole library
- The related-videos block on the watch page scored candidates by joining every published video and discarding the non-matching ones afterwards, so its cost grew with the size of the library and forced an on-disk temporary table on every view. Busy sites with large libraries could saturate MySQL from watch-page traffic alone.
- Scores now come from the category and tag indexes, and only the videos actually displayed are loaded. Measured on a 100,000-video library: 1,150 ms to 90 ms per watch page.
- The related-galleries block on album pages had the same defect and is fixed the same way.
- Ranking, ordering and the number of items shown are unchanged, and there is no database migration.

## 1.1.5 - 2026-08-07

- Catalogue imports no longer stall when the AI pass fails
- An import whose AI title or description step kept failing retried forever and never produced the video, leaving the job stuck between downloading and rewriting. The AI pass now stops after its retries, is marked Failed with the error kept on the job, and the video is imported with its original title.
- Fixed the built-in AI prompts used when the prompt service cannot be reached: they asked for plain text while the importer expected tagged output, so every attempt failed.
- Server Health: "Pseudo-cron (last run)" always reported Never, even on sites whose background tasks were running normally. It now reports the real last run.

## 1.1.4 - 2026-08-07

- Importing now keeps your original files by default
- The catalogue import panel opens on "Keep originals only": videos are imported as they are, with no conversion, no extra disk use and nothing queued for transcoding.
- "Stream smoothly" and "Shrink to save space" are unchanged and remain one click away.
- The "Recommended" badge has been removed, so the panel no longer pushes one outcome over the one it preselects.

## 1.1.3 - 2026-08-07

- Translated tags no longer appear twice on the same page
- When several tags translate to the same name in your language, that name is now shown once — on the video page, on album pages and in the Tags listing.
- Nothing is merged behind the scenes: every tag keeps its own id, its own localized address and its own videos, and each one is still reachable at its own URL.
- The tag kept on screen is the one with the most videos, so the link always points to the fullest page.

## 1.1.2 - 2026-08-06

- See real progress while a video uploads
- The New Video page now shows a live progress bar with the percentage, the uploaded size against the total, the current upload speed and the estimated time remaining.
- The upload reports the stage it has reached — Uploading, then Processing while the server saves the file, then Completed.
- An upload in progress can be cancelled, and the page warns you before you navigate away from one.
- A file larger than the server accepts is refused straight away, showing both sizes, instead of failing after a long upload.

## 1.1.1 - 2026-08-06

- Large catalogue videos now download without timing out
- Catalogue video downloads are no longer cut off after 10 minutes. A transfer is now only abandoned when it genuinely stalls, so large files can take as long as they need.
- An interrupted download resumes from the bytes already received instead of starting over, so a brief network problem no longer wastes the whole transfer.
- Videos previously marked as failed can be requeued with "Retry all failed" in the Download Queue after updating.

## 1.1.0 - 2026-08-03

- Sitemaps now follow every control on the SEO page.
- Max URLs per file (SEO -> Sitemap) works again. In 1.0.99 the field had no effect; it is now applied as an extra cap on top of Google's own limits, so it can only make files smaller.
- The Categories / Tags / Pornstars / Channels / Albums index pages added in 1.0.99 now respect the matching checkboxes. Unchecking a type removes both its sitemap file and its index page; the home page follows the Pages checkbox.
- A content type set to noindex (SEO -> Indexing) is no longer listed in the sitemap. Submitting a page you have asked Google not to index is reported as an error in Search Console. The Categories/Tags/Pornstars/Channels index pages stay listed, because the noindex option only applies to the individual pages - Albums is the exception, where the option covers the index page too.
- Turning on "Discourage search engines" now also empties the sitemaps, not just robots.txt.
- Themes (simply 1.0.25, hentai 1.0.12).
- SEO -> Robots showed an example robots.txt that was out of date: it still blocked /embed/ and paginated pages, which 1.0.99 deliberately stopped blocking. Copying it into the custom box would have silently re-blocked the embed player Google needs to crawl in order to index your videos. The example now always matches what your site actually serves. Themes are not updated by a core update - install these from Admin -> Themes to get the corrected example.

## 1.0.99 - 2026-08-03

- Sitemaps — rebuilt for search engines.
- Sitemap files can no longer exceed Google's limits (50,000 URLs / 50 MB uncompressed). On a site with many tags and several languages enabled, the tag sitemap could grow past 50 MB and be rejected by Google in full; the generator now measures what it writes and splits automatically.
- Languages that have no translated slug for an item no longer produce broken URLs in the sitemap (previously these were emitted with an empty slug and also poisoned the hreflang links).
- Each sitemap file now reports its own accurate last-modified date instead of one shared date for the whole content type.
- New sitemap-hubs.xml: the Categories, Tags, Pornstars, Channels and Albums index pages, the video listing pages (New, Top rated, Most viewed, Most favorited) and the home page — in every enabled language. None of these were listed before.
- Video entries are now correct on installs using remote storage or a CDN: thumbnails and video file URLs were malformed or missing, which made those entries invalid for Google Video.
- robots.txt no longer blocks /embed/ — Google must be able to crawl the embed player to index a video. The player page itself stays noindex.
- robots.txt now also protects the login, register, profile, search and account pages in every language (only the default language was covered), and no longer blocks paginated pages.
- Sitemap files are no longer sent gzip-encoded to clients that did not ask for it.
- On large libraries, sitemap generation is now spread over several background runs instead of doing everything in one request.
- Import → Catalogue — galleries.
- Galleries you have already imported are now moved to the END of the listing instead of filling the first pages, which is how the video catalogue already behaved. Nothing is hidden: the totals and page count stay exact and every gallery remains reachable.

## 1.0.98 - 2026-08-03

- No more empty pages in the catalogue browser
- Import → Catalogue no longer discards galleries and videos you have already imported after a page has loaded, so pages are never left empty or half full.
- Items already in your library stay visible, greyed out and marked IMPORTED, and cannot be selected by mistake.
- The result count and the number of pages now match exactly what the browser shows.

## 1.0.97 - 2026-08-03

- Large album sitemaps no longer exceed Google's 50 MB limit
- Album, category, tag, performer and channel sitemaps are now split into several files when they would grow past Google's size limit, and every part is listed in the sitemap index.
- The split now accounts for hreflang alternates, so multilingual sites no longer produce oversized sitemap files that Search Console rejects.
- Existing sitemap URLs keep working — an entity that still fits in a single file keeps its current filename.

## 1.0.96 - 2026-08-02

- TubePress 1.0.96
- Fix: admin panel showed a fatal "Call to undefined function shell_exec()" error on every page when the host's disable_functions restricted shell_exec (e.g. a PHP-FPM pool with disable_functions = exec,passthru,shell_exec,system). The admin's server-health widget now degrades gracefully instead of crashing, matching how it already handles a missing sys_getloadavg(). No functional change to background tasks: the heartbeat and task scheduler only ever require exec(), never shell_exec/passthru/system.

## 1.0.95 - 2026-08-02

- Transcoding Queue stays live while you watch it
- The queue counters, the active-jobs pill and the Queue tab badge now refresh on their own instead of staying frozen until you reload the page.
- Auto-refresh also starts when you open the Queue while it is empty, so newly queued videos appear without a manual refresh.
- Refresh failures are now reported in the browser console instead of being silently ignored.

## 1.0.94 - 2026-08-01

- Intro and outro clips no longer break the video timeline
- Videos with an intro or outro attached are now joined using the same frame rate, time base and audio settings as the video itself, so playback no longer stalls or buffers once the intro ends.
- The finished file is checked before it replaces the original: if its timeline or stream durations come out malformed, the original video is kept instead.

## 1.0.93 - 2026-08-01

- Gallery imports now start on their own on every host
- Fixes catalogue gallery purchases that stayed at pending forever, with no attempts and no error, on servers where a background worker process cannot be started. Galleries now use the same exec-less worker the video queue has, so the queue drains without any manual step, cron or heartbeat.
- The importer now checks that a worker really started instead of assuming it did, and falls back automatically when it did not.
- PHP command-line binaries installed by cPanel and CloudLinux are now detected, so servers using those layouts can start workers normally.

## 1.0.92 - 2026-08-01

- Slug field is saved, Load more works on every theme
- The Slug field on videos, categories, tags, pornstars, channels and albums is now saved exactly as you type it — and a page you simply re-save keeps its URL instead of silently changing it. Leave the field empty and it is still generated from the title, as before.
- Load more under the recommended videos now works on every theme, not only Simply.
- Pages no longer return a server error when a crawler adds a malformed parameter to the URL.
- AI translation jobs no longer stop with an internal error when they run in the background.

## 1.0.91 - 2026-08-01

- Galleries you already paid for are never charged again
- Fixes a defect where selecting a catalogue gallery whose import had not finished, or had failed, charged your credits a second time. The purchase check now recognises a gallery that is already paid for and waiting in the import queue, and retrying a failed gallery import costs nothing.
- Deleting an ad spot reported success even when the delete was rejected, for example after a session or security token had expired. The real result is now shown instead.

## 1.0.90 - 2026-08-01

- AI Translate now covers names, not only biographies and descriptions. Performers, album performers, channels and album channels gain a Name field: the name is written in the reader's own script (Arabic, Cyrillic, Chinese and so on) and returned unchanged for languages using the Latin alphabet. Translating a name also rebuilds that language's URL slug, and both are used on the site immediately. Text you have already translated is kept and is not billed again.

## 1.0.89 - 2026-08-01

- Catalogue downloads start again after updating to 1.0.88
- Fixes a regression in 1.0.88 where a purchased catalogue download could stay at pending with no attempts, no error and no worker running, on servers where the worker slot file could not be created. Downloads start normally again.
- If no download worker can be started at all, the queue now falls back to the exec-less workers instead of stalling.
- A worker slot whose recorded process id had been reused by an unrelated process no longer blocks the queue permanently.

## 1.0.88 - 2026-07-31

- Multilingual video sitemaps and stuck catalogue downloads
- Video sitemaps now include hreflang alternate links and one entry per language when "Include hreflang alternates" is enabled, matching the categories, tags, performers, channels, albums and pages sitemaps.
- A catalogue download could stay in the queue forever, with no attempts and no error recorded, on servers where the site timezone and the database timezone differ. Downloads now retry on schedule, and any download already stuck is released when you update.
- Several catalogue workers could start for the same queue slot when downloads were triggered from more than one place at once. Only one worker now starts per slot.

## 1.0.87 - 2026-07-31

- The catalogue no longer lists what this site has already imported, for videos as well as galleries, instead of merely sorting them to the end of the page you were looking at. Whether you own something is decided by this site's own library, so several sites on one account stay independent, moving to another domain changes nothing, and deleting a video or an album puts it back on sale straight away.

## 1.0.86 - 2026-07-31

- Galleries you have already imported are now listed last in the catalogue, the way already-imported videos are, so the page opens on what is still available.

## 1.0.85 - 2026-07-31

- Purchasing galleries now confirms the same way purchasing videos does: a bar above the catalogue saying the imports are being processed, with a button through to the download queue, instead of a spinner on the modal button.

## 1.0.84 - 2026-07-31

- The Parallel setting on the Downloads page now applies to gallery imports as well as video downloads; choosing 2 or 3 previously changed nothing for galleries. The queue banner and result count no longer say videos when what is queued is galleries.

## 1.0.83 - 2026-07-31

- Fixes a gallery import starting a background process on every refresh of the Downloads page. Reviving an unfinished queue is left to the scheduled task that already does it.

## 1.0.82 - 2026-07-31

- Gallery imports are now rows of the same queue as video downloads, with the cover, title, live AI badge and the same Cancel, Retry, Edit and Remove buttons, and they are covered by the existing filters, search and counters. AI enhancement chosen at import time now runs on each gallery as soon as its images are in, instead of waiting for the whole batch to finish; on a large import that made it look as though the AI options were being ignored.

## 1.0.81 - 2026-07-31

- Gallery imports now appear in Import > Downloads, next to video downloads. Each gallery is a queue entry with its own status, retried up to three times and left visible with its error if it cannot succeed. An import interrupted by a restart resumes instead of being lost, and a queue with work left restarts its worker by itself. The panel adds Retry failed and Clear finished. Re-importing a gallery you already bought is never charged twice.

## 1.0.80 - 2026-07-31

- Gallery imports now run in the background. Importing a large selection used to download every image inside the request itself, which held a PHP worker for minutes, left the button stuck on Importing... and, with several attempts running, could exhaust the pool and take the site offline. The request now charges and hands the work to a background worker, reporting real progress. The 25-gallery per-request cap, which silently dropped the rest of a bigger selection, is gone. Clicking beside a modal no longer closes it; use its close button.

## 1.0.79 - 2026-07-31

- Feed Import keeps its speed as your library grows
- Importing got slower the more you had already imported: attaching each video to its tags and categories re-counted those tags and categories across the whole library, once per tag, for every row. A feed with 17 tags per video triggered around 21 full recounts per import.
- Counts are now adjusted by the change instead of recalculated. On a 400,000-video library with a 17-tag feed this took one row from 164 ms to 4.5 ms, and the cost no longer grows with the size of your library.

## 1.0.78 - 2026-07-31

- Updates only lists what you actually have installed
- Admin → Updates offered marketplace themes and plugins that were never installed on the site, and automatic updates would then install them without being asked. Only extensions present on your site are listed now.
- Updating an extension can no longer create one: an update for something you do not have is refused. Installing a theme or plugin from the marketplace still works exactly as before.

## 1.0.77 - 2026-07-31

- Select page and Download all now work on the gallery catalogue
- The bar above the catalogue results hid both of its buttons whenever you were browsing galleries, so bulk selection was only ever available for videos. Both work for galleries now.
- Select page takes the whole page and leaves out anything you have already imported.
- Download all appears once you narrow the galleries by source, category, performer, channel, tag or image count, and tells you how many it will take. As with videos it stays hidden on the unfiltered catalogue, so a single click can never buy everything.

## 1.0.76 - 2026-07-31

- AI translation no longer charges for work it discards, and the front end is far faster at scale
- A translation batch that could not finish inside one worker pass was charged in full, sent only in part, and then counted as done — the job moved on and never came back to those videos, which is how a run could report Completed with nothing failed while most translations were missing. A batch now keeps its place and resumes until it is genuinely finished.
- Resuming a batch can no longer be charged twice. The charge is now tied to the exact items it covers, and each job records what it has already paid for, so retrying, resuming or recovering from an interruption costs nothing extra.
- The credits shown against a job now match what was actually taken, and the translated counter counts videos rather than passes. A job reports Completed only when every item really is translated; anything that failed is counted as failed.
- Large libraries: the home page now issues 12 queries instead of 60, and pages that were dominated by counting videos no longer are. Measured on a one-million-video database with no caching: 498 ms down to 11 ms. Category and tag pages get the same treatment. Nothing is cached and no total is approximated on a normal-sized site.

## 1.0.75 - 2026-07-31

- Galleries you have already imported are marked in the catalogue
- A gallery you imported stayed in the catalogue looking like one you had never bought. It now appears greyed out with an IMPORTED badge, cannot be selected, and is skipped by Select all — the same treatment the video catalogue already gave imported videos.
- Your credits were never at risk: the import screen already dropped galleries whose album exists before charging, the import re-checked, and the database enforced it. The protection was there; the indication was not.
- Re-buying a gallery after deleting its album locally still works and is still charged, as intended.

## 1.0.74 - 2026-07-31

- Robustness pass over the 1.0.73 translation work
- The one-off repair that removes English written over your translations now claims its run atomically and works in batches. On a 29-language site it used to issue one statement per string per language — 1601 of them inside a single page view, with nothing stopping a second visitor starting the same work. The same repair is now 58 statements and finishes in well under a tenth of a second.
- A footer configuration in an unexpected shape can no longer take the front page down. Values are checked before use, so a partially written or hand-edited footer setting renders what it can instead of raising an error.
- Catalogue import: a sidebar section you collapse stays collapsed. It was springing back open every time a filter was cleared or a chip removed.
- Mobile menu: the language list can no longer run past the bottom of the drawer on a short screen — that area now scrolls.

## 1.0.73 - 2026-07-31

- Editing your translations no longer replaces them with English
- Admin → Languages → Edit translations showed the English text, not your translation, for every string that comes from your theme. Saving the page then stored that English over the translation for good — on a 29-language site that was 56 strings per language, including the whole Albums interface and headings such as TRENDING, WATCH IT AGAIN, More and Powered by. The editor now shows what your site shows, and saving an untouched form changes nothing.
- Installing this release repairs the entries that were already overwritten. Translations you deliberately customised are kept, including a string you chose to leave in English.
- Core updates now install the translation files. They have always been inside the update package but were never applied, so a translation fix or a newly translated string could only ever reach a brand-new install.
- Footer badges and column headings follow the site language again (18+ Only, Free Content, HD Videos, Navigation, Pages, About), and the per-language text saved for a footer logo description is now used. Your own wording is untouched.
- Catalogue import: every sidebar heading now shows how many values are behind it, and the galleries view opens on Categories instead of a column of empty headings.

## 1.0.72 - 2026-07-31

- Translate a single row or a single language, and site visibility moves next to the other indexing controls
- Every listing row — categories, tags, pornstars, channels (videos and albums), plus videos, albums and pages — now has a small language button beside Edit. It opens the same AI Translate window scoped to that one item, with the same language choice, field choice and price shown before anything is charged.
- On the page editor, each language tab has its own button: "Translate to FR" translates that language alone, and the default tab opens the full picker for that page.
- "Discourage search engines from indexing this site" now sits at the top of SEO → Indexing, above the per-page-type list, instead of on the Advanced tab. While it is on, the per-type list is dimmed and explains that the site-wide switch is what applies.

## 1.0.71 - 2026-07-31

- Search engine visibility stays reachable however old your theme is
- "Discourage search engines from indexing this site" moved to SEO → Advanced in 1.0.70, but themes are updated on your own schedule, so an install running an older theme could end up with the setting nowhere at all. Settings now shows it again automatically whenever your theme does not yet provide it, and hides it the moment the theme does.
- Saving Settings can no longer switch that setting back on or off behind your back.

## 1.0.70 - 2026-07-31

- AI Translate on every content section, per-language branding, and 404 pages that can no longer be indexed
- The "AI Translate" button is now on all eleven content sections — Categories, Tags, Pornstars and Channels (for videos and for albums), plus Videos, Albums and Pages. Pick your target languages one by one or all at once, choose which fields to translate, see the exact cost before you start, and run it in the background with pause, resume, stop and retry.
- You are now charged as the run goes, only for what actually gets translated: anything already translated is skipped free of charge, and stopping a run stops the billing. A run started with a short balance tells you how far it will get and pauses cleanly when it runs out.
- Pornstar and channel NAMES are never translated — only their biography or description. Proper names are left exactly as you wrote them.
- Your site name, site description and homepage title / meta description can now differ per language. Set the default in Settings, then translate them in Languages → Edit translations → Branding; anything you leave empty falls back to the default. The translated value is used in the page title, meta description, Open Graph, Twitter card and WebSite structured data.
- Pages that do not exist now return a real 404 together with "noindex, follow" and a matching X-Robots-Tag, so a missing URL can never be indexed. Sections you switch off answer 410 Gone with the same protection.
- "Discourage search engines from indexing this site" has moved to SEO → Advanced, next to the other indexing controls, and saving Settings no longer switches it back off.

## 1.0.69 - 2026-07-31

- AI translation for album categories and album tags
- Album Categories and Album Tags now offer the same AI translation as videos: "Translate All" in the page header, "Translate" on a selected batch, and the same price of 0.5 credits per item covering every language you have enabled.
- "Skip already translated" only spends credits on entities that are still missing a language, and "Auto-translate new ... as they are added" keeps newly created album categories and tags translated on their own.
- Album categories reuse the same shared translation dictionary as video categories, so a term you already translated on the video side costs you nothing on the album side.
- Album pornstars and album channels are deliberately not translated: those are proper names and are left exactly as you entered them.

## 1.0.68 - 2026-07-31

- Ads no longer stretch the page on phones
- Ad-network banners (728x90 leaderboards, hero billboards, in-feed cards, sidebar and sticky spots) are now scaled down to fit the screen, so the whole ad stays visible on mobile.
- An ad wider than the screen can no longer widen the page itself: ad slots keep your site's width on every device, and desktop banners still display at their full size.

## 1.0.67 - 2026-07-31

- New ad spots can be saved again in every ad zone
- Creating or editing an ad spot in any zone other than Popup/Popunder failed with a 500 error and saved nothing; it now saves normally.
- Popunder spots are unchanged: their URL/code mode, delay, frequency cap and time window keep working as before.

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

