# reachableheights.com — an archive

A static, single-page archive of **Reachable Heights**, a praise and worship band
from Ludington, Michigan, active roughly 2016–2020. Built in 2026. The band is
not active and this is not their original site.

Members: **Jessica Hein** (lead vocals), **Andrew Lucero** (bass, guitar),
**Brandon Martinez** (guitar, bass, drums, keys, recording and production).

## The critical rule: this is an archive, and it says so

An archive banner sits at the top of every render, the `<title>` and `og:title`
both read "— an archive," and the `#story` section states plainly that the page
was built in 2026 and that the domain lapsed and was re-registered to hold it.
**Do not remove or soften any of that.** If someone lands here from a search
result, they must not think the band is playing.

## Provenance — where every piece came from

| On the page                          | Source                                                                                                                                              |
| ------------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| The five song posts + the 2018 post   | The WordPress RSS feed, captured by the Internet Archive on **2018-12-04** (`web.archive.org/web/20181204190030id_/http://www.reachableheights.com/feed/`) — quoted **verbatim** |
| Post dates                            | `pubDate` in that same feed                                                                                                                         |
| Audio                                 | **Azure Blob** — `martinezmediaclients.blob.core.windows.net/reachableheights/tracks/`, uploaded 2026-07-31 from the band's own Logic bounces. *Great Are You Lord* remains a `w.soundcloud.com` embed |
| Logo, wordmark, starfield, treeline   | `Documents/Clients/Reachable Heights/site/img/` — the original 2018 holding-page assets (layered PSD/AI sources also survive)                        |
| Worship EP / Christmas EP covers      | `Documents/Clients/Reachable Heights/Media/Album Art/`                                                                                               |
| The unreleased catalog (29 + 21)      | Filesystem survey of `/Volumes/Audio/Logic/Reachable Heights` (~54 GB of Logic sessions); years are folder **mtimes**, not release dates             |
| Band roles, formation, the quiet close | The owner's own vault notes (`Relationships/Reachable Heights/`), themselves sourced from message archives and direct confirmation                    |

Research writeup:
`brandonmartinez-secondbrain/02-Areas/Web Presence/Docs/2026-07-31 - Legacy Site & Band Archive Inventory.md`.

## Design

Rebuilt on the design language of the **May 2018 holding page**: the white
outline logo, the night-sky photograph cropped into a starfield behind the hero,
and the mirrored treeline horizon closing the page. Accent `#1cbac8` is the
accent color from the earlier WordPress "unicon" theme.

`logo-textonly-white.png` is a recolor of the original black wordmark — the
source file is black-on-transparent and disappears on a dark page.

## Content rules

- **Never invent or correct content.** Every quoted line is verbatim from 2016 /
  2018. Where a sentence reads oddly ("Checkout our SoundCloud account"), that is
  how it was written. Do not fix it.
- **Ten of the eleven recordings are self-hosted; the covers are not.** The
  owner's decision was revised 2026-07-31: the four 2016 originals plus six
  finished-but-never-posted mixes stream from Azure Blob (see "Audio & the
  player"). Everything still withheld is withheld on purpose — guide tracks,
  half-mixes, working drafts, and the 21 covers.
- **Do not host the covers.** The 21 covers are other writers' songs. They are
  listed by title only. The one cover on the page, *Great Are You Lord*, stays a
  SoundCloud embed — it is the band's own already-public upload, and no local
  mix of it survives.
- **Titles are fine to list; unmixed recordings are not.** The unreleased
  catalog exists on the page as a list of names deliberately. The nine audible
  titles in it are marked ▶ and link nowhere else.
- *Great Are You Lord* is the one player that dies if SoundCloud does. `#story`
  says exactly that; keep it honest.

## Audio & the player

Audio is **not** in this repo. It streams from a public Azure Blob container:

```
https://martinezmediaclients.blob.core.windows.net/reachableheights/tracks/<slug>.mp3
```

Ten files, 53 MB, uploaded 2026-07-31 from
`~/src/_archive/_staging/reachableheights-audio/` — that staging tree's
`README.md` carries the per-file provenance and the SoundCloud duration-match
table used to identify the four published mixes. The container is **Blob-level**
anonymous access, never Container-level (which would allow listing).

`src/player.js` (shared with blythe-band and lostinsanity) is a ~165-line
vanilla-JS sticky bar. The markup contract:

- A track is `.track[data-src][data-seconds]`, with `.track-btn`, `.track-title`.
- Its nearest `[data-playlist]` ancestor scopes auto-advance;
  `data-record` is the label shown in the bar. `closest()` matches the element
  itself, so a lone track can carry both.
- The bar is `#player` with `#pl-{play,prev,next,close,title,record,cur,dur,seek}`.

**Tailwind v4 trap — read this before touching a track title.** Utilities beat
`@layer components` regardless of selector specificity, so a `text-zinc-100`
utility on `<h3 class="track-title">` silently defeats
`.track.is-active .track-title { @apply text-beacon; }` with no error. Resting
styles for `.track-title` therefore live in `styles/tailwind.css`; do not add
font/size/color utilities to those `<h3>`s.

Verify every URL after an upload:

```sh
grep -o 'data-src="[^"]*"' src/index.html | cut -d'"' -f2 | \
  while read u; do printf '%s %s\n' "$(curl -s -o /dev/null -w '%{http_code}' -r 0-1 "$u")" "$u"; done
```

`206` is correct (ranged request). Anything else means the blob is missing or
the container's access level was reset.

## Deliberately excluded — do not add these

- **Kenny Graham.** He sat in on jams in 2017 but was not a member; the owner
  asked for him to be left off (2026-07-31).
- **"Stephanie,"** an early member named only by first name in a 2016 message.
  Not confirmed enough to publish.
- Any audio file, chord chart, lead sheet, or lyric PDF from the working folders.
- The Waterfront Worship share folder.
- Any email address, phone number, or street address.

## Build

House pattern, same as the sibling archives:

```sh
npm install
npm run dev     # vite on :4176
npm run build   # → _site/ (also copies CNAME)
```

Deployed to GitHub Pages by `.github/workflows/deploy.yml` on push to `main`.
`npm audit` must report **0 vulnerabilities**.

## Sibling archives

- [blythe-band.com](https://blythe-band.com) — high-school band, 2005–2007
- [lostinsanity.net](https://lostinsanity.net) — first band, 2004–2005
- [geekycomputerservices.com](https://geekycomputerservices.com) — teenage
  business, 2003–2005
