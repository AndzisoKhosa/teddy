# teddy

A one-page birthday card for Tediso. Live at https://andzisokhosa.github.io/teddy/

Tap "Open your card": a song starts and loops, then two videos, one picture and the wishes spin onto the page one at a time, 3 seconds apart, until they form a collage. Confetti fires when the last piece lands.

## What is in this repo

Only `index.html`. There are no separate media files. The videos, the picture and the song are stored inside `index.html` as base64 data URLs, so the page works from this single file.

GitHub Pages serves the `main` branch from the root folder. Any commit to `main` redeploys the site in about a minute.

## How index.html is organised

| Part | What it holds |
| --- | --- |
| `<style id="app-style">` | All CSS. Colours are tokens on `:root`, with a dark theme. |
| `<script type="application/json" id="board-data">` | All content: name, wishes, media, song. |
| `<script id="app-script">` | All behaviour. Builds the page inside `<div id="app">`. |

Keep those three ids. The script reads its own style and script tags by id.

## Editing the content (board-data JSON)

```
{
  "rev": 1791228000000,          // millisecond timestamp, bump on every edit
  "name": "Tediso",              // shown in the heading and on the cover
  "wishes": [ { "id": "wish-1", "text": "...", "from": "" } ],
  "items":  [ { "id": "vid-a", "kind": "video" | "image", "mime": "video/mp4",
                "ar": 0.562, "data": "data:video/mp4;base64,..." } ],
  "song":   { "id": "song-x", "name": "...", "mime": "audio/mpeg", "data": "data:audio/mpeg;base64,..." }
}
```

Rules when editing it:

1. Always set `rev` to a newer timestamp than before. A browser that has a locally saved copy shows whichever copy has the higher `rev`.
2. Write every `<` inside the JSON as `\u003c`, and never let the text `</script>` appear in it.
3. `ar` is width divided by height. It reserves the right space before the media loads.
4. Videos play muted and looped, so strip their audio (`ffmpeg -an`). Use H.264 MP4 with `yuv420p`.
5. `items` order is the order pieces appear. Wishes always appear after all items.
6. Keep the file under GitHub's 25 MB web upload limit.

Current items: `vid-a` (about 34 s), `pic-a`, `vid-b` (about 12 s), plus one MP3 song.

## Editing the behaviour (app-script)

| Name | Meaning |
| --- | --- |
| `STEP_MS` | Delay between pieces, 3000 ms. |
| `reveal()` | Shows one piece. Adds the class `spin`. Swap to `fly` or `pop` for the other built-in transitions. |
| `stageWall()` | Hides everything, then schedules `reveal()` for each piece. |
| `layout()` | Places pieces in uneven columns, overlaps them, and shrinks the wall until it fits one screen. |
| `COL_WIDTHS`, `OVERLAP`, `NOTE_LIFT`, `TILTS` | Column sizes, how far pieces overlap, how far a wish tucks over a picture, tilt angles. |
| `confetti()` | Canvas burst after the last piece. |

Transitions are CSS keyframes in `app-style`: `spinin`, `flyin`, `pop`.

## Things that only work elsewhere

The page was first built as a Claude artifact. The Edit panel and "Save to link" need that host (`window.claude`) and stay hidden here, so on GitHub Pages the card is view only. This repo is a separate copy and does not sync with the artifact.

## Checking a change

Open the live link, tap "Open your card", and confirm the song plays, both videos move, and all pieces land without the page scrolling. Test in a visible browser tab, because Chrome holds video and audio in background tabs.
