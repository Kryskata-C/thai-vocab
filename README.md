# Thai · Vocab

A tiny, mobile-first web app for learning Thai vocabulary by **typing the answer** — no frameworks, no build step, works offline once loaded.

**Live:** https://kryskata-c.github.io/thai-vocab/

## Tabs

| Tab | What it does |
|-----|--------------|
| 📅 **Today** | Practice one day at a time. Opens on the newest day in the word bank (the highest `day` number); tap a day chip to revisit an earlier day. Shows that day's word list and a shortcut to its flashcards. |
| ⌨️ **Practice** | Typed recall in three directions: **EN → Thai** (type romanised or Thai script), **Listen** (hear the word, type its meaning), **Thai (rom) → EN** (see the romanised word, e.g. *yùu*, type the meaning). The same direction switch is on the Today tab. Lenient matching (`khao`, `kao`, `khâao` all count; near-misses count as "close"). Hints, "show answer", and an "I was right" override. Filter by category, **Due**, **Weak** or **New**. |
| 🃏 **Learn** | Flip-able flashcards (tap / swipe) by day, plus a searchable word list with 🔊 and mastery badges. |
| 🧩 **Build** | Sentence builder — tap Thai word tiles into the right order to translate an English sentence (with an optional distractor tile). Shows word-by-word gloss and speaks the sentence. |
| 🎵 **Tones** | Tone drill — hear/see a word and pick its tone (mid / low / falling / high / rising), plus a tone reference. |
| 📈 **Progress** | Streak, daily goal, accuracy, due count, 16-week activity heatmap, per-category mastery bars, weak-word list, settings (goal, autoplay, speech speed) and export / import / reset. |

## How the review scheduling works

Each word tracks right/wrong/streak. A correct answer pushes its next review out (0 → 1 → 3 → 7 → 14 → 30 → 60 days); a miss resets it to due-now. The Practice picker favours due words, then new words, then weak words, and avoids immediate repeats. A word is **solid** once it has 3+ correct answers and a streak of 2+.

## Adding words

The word bank is personal — it only contains words the owner has actually learned, and grows day by day.

Everything lives in `index.html`. Add an object to `WORDS`:

```js
{id:"unique_id", th:"ไทย", rom:"thai", en:"Thai (language)", alt:["thai language"], tone:"mid", day:9, cat:"Things"}
```

- `id` must be stable — progress is keyed by it.
- `alt` (optional) lists extra accepted English answers.
- Single-syllable words (no `+` in `tone`) are used in the Tone drill.

Sentences for **Build** go in `SENTENCES` as `{en:"...", ids:[...word ids in Thai order]}`.

## Run locally

Just open `index.html`, or `python3 -m http.server` and visit http://localhost:8000 (needed for the service worker / PWA install).

Audio uses the browser's built-in Thai text-to-speech voice (iOS ships one; on desktop you may need to install one).
