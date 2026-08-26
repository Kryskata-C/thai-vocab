# Thai · Vocab

A tiny, mobile-first web app for learning Thai vocabulary by **typing the answer** — no frameworks, no build step, works offline once loaded.

**Live:** https://kryskata-c.github.io/thai-vocab/

## Tabs

| Tab | What it does |
|-----|--------------|
| 📅 **Today** | **📚 Daily review** — one pass through the words due from earlier days (weakest direction first, capped in Settings) plus every word of the newest day in both directions; ends with a score and can be rebuilt. Or pick a **day chip** to drill just that day (opens on the newest). Shows the day's word list (with your notes) and a shortcut to its flashcards. |
| ⌨️ **Practice** | Typed recall: **EN→Thai** (type romanised or Thai script), **Listen** (hear it, type the meaning), **Rom→EN** (see *yùu*, type the meaning), **Speak** (hear it, say it aloud, reveal; 🎤 does a rough speech-recognition check where the browser supports Thai). Lenient matching (`khao`, `kao`, `khâao` all count; near-misses count as "close"). Hints, "show answer", "I was right" override, a **tone follow-up** after any miss, and a **📝 note** per word for your own hooks. Filter by category, **Due**, **Weak** or **New** — all per direction. |
| 🃏 **Learn** | Flip-able flashcards (tap / swipe) by day, plus a searchable word list with 🔊, mastery badges and notes. |
| 🧩 **Build** | **Direction switcher** — **EN → Thai** (the original: see English, produce Thai) or **Thai → EN** (see the Thai script + romanisation, give the English; *Tiles* become English word tiles, *Type it* takes a free English translation matched leniently — articles, word order, contractions, verb forms and synonyms from the bank's `alt` lists are forgiven, but a missing *not* or a swapped question word is wrong). Your last choice is remembered. Sentences are **generated on the fly** from the tagged word bank (thousands of distinct, grammatical, translated sentences — see below), picked to favour unseen / missed sentences and due, weak or newest-day words. A **🎯 Focus** chip row lets you pick a day (e.g. Day 6) so the generator strongly favours sentences using that day's words (remembered in settings; "Any day" turns it off). A **🔥 Harder** toggle chip makes the generated sentences longer and fuller — more objects, destinations, companions (*gàp …*), *wan-níi*, *khráp*, and the picker prefers the longest candidates (also saved in settings). **Tiles** — tap Thai word tiles into order. **Type it** — write the whole sentence in romanised Thai (matched word by word, spaces/hyphens ignored; each word is marked ok / close / bad). Every answer ends with a one-line grammar tip for the frame you just built. **Patterns** — fill-the-blank drills on frames like *[X] yùu thîi-nǎi?*, *khǒ [X] khráp*, *[X] mǎi?*, with a reference list of every pattern. |
| 🎓 **Test** | A **readiness check for a day** — "can I move on?" Pick a day (or **All days** for an occasional overall measurement of 10/15/20 sampled words): **every word of that day, typed in both directions** (EN→Thai and Thai→EN), plus 2–4 generated sentences that use that day's words. **No hints, no show-answer, no audio, one shot per question**, and every answer is **timed**. Matching is stricter than Practice — missing tone marks are still fine, but a near-miss that is (or is closer to) a *different* bank word is wrong, typed tone marks that spell another word are wrong, and sentences need every word exact. It ends with a **decisive verdict**: ✅ **Ready** (every word right in both directions, at most ~1 in 5 words slow, sentences at least half right — *go learn the next day*), 🟡 **Almost** (≥75 % of word answers right and at most ~2 in 5 words shaky — practise those, retest), or 🔴 **Not ready** (drill the day again first). "Shaky" = wrong **or 🐢 slow**: an answer that took longer than 1.6× your own median for the test (and >3.5 s) means the word isn't automatic yet even if you got it right. The results show the verdict with the exact words holding you back, accuracy per direction, a **per-word table with the time for each direction**, every wrong answer with the correct one, a comparison with your previous test of the same day, and a button to send just the shaky words into Practice (the **🎓 Shaky from test** filter). History is kept per day so you can see retests improving. Answers also feed the normal per-word scheduling. |
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

Patterns go in `PATTERNS` as `{id, name, gloss, ids:[... with "_" for the slot], fills:[{id, en:"full English sentence"}]}` — only bank words.

### Grammar tags (Build sentences)

Build sentences are not stored — the **sentence generator** (`FRAMES` in `index.html`) builds them from the word bank: ~16 frames (statement / negation / yes-no question, *yàak* + verb, *dâi* can, adjective and *yùu* location predicates, every wh-word, *khǒ* requests, *mii* there-is, *rʉ̌ʉ* or, *gàp* with/and, imperatives, polite phrases), each filled with words that fit, then translated by a small English clause builder (do-support, -s / -ing / participles, contractions). Each "Next" builds a pool of candidates and picks the best for your progress. Progress stays keyed by the word ids, so the same sentence always maps to the same record.

Words tell the generator how they behave through an optional `g` tag. **Without one, a word is guessed from its `cat`** (Verbs → verb with no object, Things/Places/People → noun, Describe → adjective, Time → time word) and joins the simpler frames only (*phǒm yàak [verb]*, *[noun] yùu thîi-nǎi*, *nîi [adj]* …). Add a tag to unlock the rest:

| pos | fields | example |
|-----|--------|---------|
| `pron` | `s` subject / `o` object form (`"he\|she"` = pick one), `be` (am/is/are), `n` 1/2/3 for verb agreement | `{pos:"pron",s:"he\|she",o:"him\|her",be:"is",n:3}` |
| `v` | `en` base verb, `t` form used before an object (`"look at"`), `obj` classes it takes (`food drink thing place person dem loc`), `asp` `any\|prog\|simple` (progressive allowed?), `dest` `"away"\|"toward"` (go / come), `at` (allows *thîi place*), `with` (allows *gàp person*), `maak` (`1` = "a lot" without object, `"any"`), `it` (English needs "it" when no object), `art:"a"` (indefinite objects: *have a car*), `comp:0` (can't follow *yàak*/*chôrp*), `perf:0` (no *láew/yang* 'already/yet' frame), `nowh` (no why/when questions), `needObj:1` (never appears without its object — *bàwk* always takes a person) | `{pos:"v",en:"eat",obj:["food","dem"],asp:"any",at:1,with:1,maak:1}` |
| `n` | `cls` classes, `a` indefinite English (`"rice"`, `"a car"`), `the`, and for places `at` / `to`, `holds:["person"]` (only people can be located there), `noPrice:1` (no thâo-rài / quality adjectives — e.g. money) | `{pos:"n",cls:["place"],a:"a shop",the:"the shop",at:"at the shop",to:"to the shop"}` |
| `adj` | `en`, `of` classes it can describe, `attr:1` = usable attributively in khǒ orders (*khǒ náam yen*) | `{pos:"adj",en:"spicy",of:["food"],attr:1}` |
| `cop` | *bpen* "to be + noun" — drives the noun-predicate frame (*phǒm bpen khon dii*, *nîi bpen rót*, *khǎo bpen khrai?*) | `{pos:"cop"}` |
| `dir` / `turn` / `park` | taxi-Thai frame: *líao sáai/khwǎa (thîi-nîi)*, *jòrt thîi-nîi*, *… dâi mǎi?* | `{pos:"dir",en:"left"}` |
| `dem` / `loc` / `time` | `en` (`this`, `here`, `today`); `loc` may set `prox:1` (here vs there) | `{pos:"loc",en:"here",prox:1}` |
| `aux` | handled by name in the frames (*yàak, dâi, yùu*) — no other fields | `{pos:"aux"}` |

Function words (*mâi, mǎi, thîi, gàp, rʉ̌ʉ, khráp, khǒ*, question words) are referenced by id inside the frames and need no tag; a frame simply doesn't fire until its words are in the bank.

**Upcoming lessons.** Some patterns need teaching before they're drilled even if the words already exist. `LESSONS` in `index.html` lists them, and a frame with `lesson:"x"` only fires once `LESSONS.x.day` is set to the day it was learned. Currently gated: `exist` — *mii* as "there is / there are" (*place + mii + thing*), `day:null` = not taught yet, so *mii* only appears as "have" (subject + mii + object). To introduce it, set `LESSONS.exist.day` to that day's number.

## Run locally

Just open `index.html`, or `python3 -m http.server` and visit http://localhost:8000 (needed for the service worker / PWA install).

Audio uses the browser's built-in Thai text-to-speech voice (iOS ships one; on desktop you may need to install one).
