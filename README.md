# Thai · Vocab

A tiny, mobile-first web app for learning Thai vocabulary by **typing the answer** — no frameworks, no build step, works offline once loaded.

**Live:** https://kryskata-c.github.io/thai-vocab/

## Tabs

| Tab | What it does |
|-----|--------------|
| 📅 **Today** | **📚 Daily review** — one pass through the words due from earlier days (weakest direction first, capped in Settings) plus every word of the newest day in both directions; ends with a score and can be rebuilt. Or pick a **day chip** to drill just that day (opens on the newest). Shows the day's word list (with your notes) and a shortcut to its flashcards. |
| ⌨️ **Practice** | Typed recall: **EN→Thai** (type romanised or Thai script), **Listen** (hear it, type the meaning), **Rom→EN** (see *yùu*, type the meaning), **Speak** (hear it, say it aloud, reveal; 🎤 does a rough speech-recognition check where the browser supports Thai). Lenient matching (`khao`, `kao`, `khâao` all count; near-misses count as "close"). Hints, "show answer", "I was right" override, a **tone follow-up** after any miss, and a **📝 note** per word for your own hooks. Filter by category, **Due**, **Weak** or **New** — all per direction. |
| 🃏 **Learn** | Flip-able flashcards (tap / swipe) by day, plus a searchable word list with 🔊, mastery badges and notes. |
| 🧩 **Build** | **Tiles** — tap Thai word tiles into order. **Type it** — write the whole sentence in romanised Thai (matched word by word, spaces/hyphens ignored; each word is marked ok / close / bad). **Patterns** — fill-the-blank drills on frames like *[X] yùu thîi-nǎi?*, *khǒ [X] khráp*, *[X] mǎi?*, with a reference list of every pattern. |
| 🎵 **Tones** | Tone drill — hear/see a word and pick its tone. Words you miss (in drills or tone follow-ups) come up more often; **Missed** drills only those. Plus a tone reference. |
| 📈 **Progress** | Streak, goal, accuracy, due count, **all-time answers** (every drill, any direction) and total word translations, 16-week heatmap, **per-day mastery** (tap a day to drill it), per-direction and per-category bars, weak words, settings (goal, autoplay, distractor tile, **hard mode** = tone marks required, review size, speech speed), and Export / **Copy link** / Import / Reset. |

## How the review scheduling works

Each word tracks right/wrong/streak **per direction** — `th` (EN → Thai: you produce the word; EN→Thai and Speak drills) and `en` (Thai → EN: you recognise it; Rom→EN and Listen drills). A correct answer pushes that direction's next review out (0 → 1 → 3 → 7 → 14 → 30 → 60 days); a miss resets it to due-now. The picker favours due words, then new words, then weak words, and avoids immediate repeats. A direction is solid at 3+ correct with a streak of 2+; a word is **solid** only when both directions are ("½ solid" otherwise). Older progress (v2, single-direction) is migrated into `th` automatically.

Progress lives in `localStorage` on the device. **Copy link** (Progress → Your data) packs the whole state into a URL — open it on another device and tap Import.

## Adding words

The word bank is personal — it only contains words the owner has actually learned, and grows day by day.

Everything lives in `index.html`. Add an object to `WORDS`:

```js
{id:"unique_id", th:"ไทย", rom:"thai", en:"Thai (language)", alt:["thai language"], tone:"mid", day:9, cat:"Things"}
```

- `id` must be stable — progress is keyed by it.
- `alt` (optional) lists extra accepted English answers.
- Single-syllable words (no `+` in `tone`) are used in the Tone drill.

Sentences for **Build** go in `SENTENCES` as `{en:"...", ids:[...word ids in Thai order]}`. Patterns go in `PATTERNS` as `{id, name, gloss, ids:[... with "_" for the slot], fills:[{id, en:"full English sentence"}]}` — only bank words.

## Run locally

Just open `index.html`, or `python3 -m http.server` and visit http://localhost:8000 (needed for the service worker / PWA install).

Audio uses the browser's built-in Thai text-to-speech voice (iOS ships one; on desktop you may need to install one).
