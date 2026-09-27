# Hindi A1-B1 vocab pack

Live at **https://bannerless-studio.github.io/hindi/**. A free,
offline-capable vocabulary trainer for Hindi, built on the shared
[`vocab-engine`](https://github.com/Bannerless-Studio/vocab-engine). It
teaches 2000 words spanning A1-B1, each with a short English gloss and a
romanisation. Every word has at least two example sentences with English
translations. The Read tab adds 60 short reading passages with
comprehension questions.

**Scope note:** this app is a vocabulary base for B1; an exam also needs
grammar, writing and speaking practice. The app teaches words, glosses and
example sentences, and does not teach those skills.

## Using the trainer

- **Script primer.** A "देवनागरी" stage runs before A1 and teaches the
  Devanagari script in 73 units over 10 sets: the independent vowels; the
  matras, each paired with its vowel (का with आ); the consonants by varga,
  then य र ल व and श ष स ह; the nukta consonants (क़ ख़ ग़ ज़ ड़ ढ़ फ़); anusvara,
  chandrabindu, visarga and virama; ऑ and its sign ॉ for English loanwords
  (डॉक्टर, कॉलेज); and six common conjuncts (क्ष त्र ज्ञ श्र द्य द्व). The
  first set includes प, ल and ग, so every example word in it is readable
  from that set alone (अपना, अगर, अलग). Items drill symbol to sound, sound to
  symbol, composing a syllable, and reading A1 words. It can be skipped with
  "I can read it" and reopened from Progress. Sounds use the browser's hi-IN
  voice; voice quality has not been checked on real devices.
- **Today** runs one daily session: review, learn new words, listen, recall,
  sentences, then (once unlocked) a reading passage. Each stage skips itself
  when it has too little due material.
- **Words** is a searchable browser with per-set drills; **Test** has a
  placement test plus free tests; **Progress** shows stats and lets you
  export, import or reset your progress.
- **Read tab passages.** 60 short reading texts (20 each at A1, A2, B1),
  4-5 comprehension questions each (296 in total: 142 multiple-choice, 154
  true/false). A passage's spaced re-read (after 7 days) becomes a
  listening pass when every sentence has audio: the text stays hidden and
  some questions are audio-only.
- **Offline.** The page is cached on first visit and keeps working without
  a connection; a new build updates the cache in the background.
- Your progress is stored only in your browser (`localStorage`). Use
  Progress to export a backup or move it to another device.

## Script and display

- The font is [Noto Sans Devanagari](https://fonts.google.com/noto/specimen/Noto+Sans+Devanagari)
  (OFL), loaded from Google Fonts. The text runs left to right.
- Words display in the dictionary spelling, with nukta (ज़रूरत, क़दम) and
  chandrabindu (माँ, हाँ). Matching folds nukta, chandrabindu to anusvara, a
  class nasal plus virama to anusvara (हिन्दी = हिंदी), the glide spellings
  (गये = गए, रुपये = रुपए) and Devanagari digits, so a search or a typed
  answer in either spelling still matches; the display itself does not
  change.
- **Romanisation** (`pron`) follows Wiktionary's IAST-like scheme: ā ī ū for
  the long vowels, ṭ ḍ ṇ for the retroflexes, ś ṣ for the sibilants, ṅ ñ for
  the velar and palatal nasals. Nukta letters are q x ġ z ṛ f. Anusvara is n
  or m before a consonant and a tilde otherwise (माँ = mā̃). The final schwa
  is dropped (कमरा = kamrā). All 2000 words have a romanisation.
- Typing drills accept a typed answer without nukta or with chandrabindu
  written as anusvara (जरूरत for ज़रूरत; माँ = मां, हँसना = हंसना) at every
  level.

## Data quality

Hand-checked samples:

| Sample | Result |
|---|---|
| Words, seed 41, 60 per level (180) | 175/180 correct primary sense before the last gloss fixes (all 5 fixed); 0 wrong part of speech |
| Sentences, seed 42, 30 per level (90) | 2 wrong links of 505 (99.6%); one more link follows a typo in the Tatoeba text |

Tatoeba has too few usable Hindi sentences for many words, so **1,034 of
the 3,172 example sentences were written for this pack** (marked `"src":
"gen"` in `pack/sentences.json`; source in `tools/generated_examples.tsv`).
368 words have only written sentences; the other 2,138 sentences come from
Tatoeba. The written sentences are machine-written and reviewed, but not by
a native Hindi speaker.

The Read tab passages were written for this pack and checked by the
builder plus a manual QA pass; they have not had a native-speaker review.

Tatoeba's audio export has no recording of any Hindi sentence (checked
against the 2026-09-26 export), so the pack ships no sentence audio.
Speech uses the browser's hi-IN voice.

Level bands (A1/A2/B1) are a reproducible frequency-based proxy for CEFR,
not an official classification. No graded Hindi word list is used or
shipped. Known residuals are tracked in `TODO.md`.

## Sources and licences

| Data | Source | Licence | Used for |
|---|---|---|---|
| Spoken/subtitle frequency | [hermitdave/FrequencyWords](https://github.com/hermitdave/FrequencyWords) (`hi_full.txt`, OpenSubtitles 2018) | CC BY-SA 4.0 | word ranking |
| Written/general frequency | [`wordfreq`](https://github.com/rspeer/wordfreq) Python package | CC BY-SA 4.0 | word ranking |
| Glosses, POS, gender, romanisation, inflection tables | [kaikki.org](https://kaikki.org) Hindi Wiktionary extract | CC BY-SA 3.0 / GFDL | glosses, POS, `pron`, `alt` forms |
| POS tagging / lemmatisation (build time only) | [Stanza](https://stanfordnlp.github.io/stanza/) 1.14.0 (Apache-2.0), hi default model trained on UD Hindi-HDTB | model data **CC BY-NC-SA 4.0** | corpus POS and lemmas, sentence links. No model files or tagger output ship; the pack ships only words, glosses and Tatoeba/written sentences. Same stance as the Russian and Persian packs. |
| Example sentences | [Tatoeba](https://tatoeba.org) `hin_sentences_detailed.tsv` | CC BY 2.0 FR | sentence text (contributors in `pack/attribution.json`) |
| Sentence translations | Tatoeba `eng_sentences.tsv` + `hin-eng_links.tsv` | CC BY 2.0 FR | English translations |
| Written sentences | `tools/generated_examples.tsv`, written for this pack | same as this repo | 1,034 sentences marked `"src": "gen"` |
| Reading passages | `tools/passages_src.json`, written for this pack | same as this repo | 60 passages |
| Font | Noto Sans Devanagari via Google Fonts | SIL OFL 1.1 | display only |

Licence: code MIT, pack data CC BY-SA 4.0, see LICENSE.

## Rebuild and publish

This repo holds the Hindi data pack and the data files its build reads.
`vocab-engine` (above) is a git submodule at `engine/` and holds the shared
UI, drill logic and pack builder; all Hindi-specific rules live in
`engine/tools/packbuilder/langs/hi.py`. For the exact rebuild/check commands
and file-by-file notes on `tools/`, see `tools/README.md` and `CLAUDE.md`.
