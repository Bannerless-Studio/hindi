# Hindi A1-B1 vocab pack

Static data pack for a language-agnostic vocab trainer (`key: "hi"`). It has
2000 words spanning A1-B1, each with a short English gloss and a
romanisation. Every word has at least two example sentences with English
translations. The Read tab adds 60 short reading passages with
comprehension questions (see "Reading passages" below).

**Live:** https://bannerless-studio.github.io/hindi/

**Script primer.** A "देवनागरी" stage runs before A1 and teaches the
Devanagari script in 73 units over 10 sets. The units are:
- the independent vowels;
- the matras, each paired with its vowel (का with आ);
- the consonants by varga, then य र ल व and श ष स ह;
- the nukta consonants (क़ ख़ ग़ ज़ ड़ ढ़ फ़);
- anusvara, chandrabindu, visarga and virama;
- ऑ and its sign ॉ, for English loanwords (डॉक्टर, कॉलेज);
- six common conjuncts (क्ष त्र ज्ञ श्र द्य द्व).

The first set includes प, ल and ग, so every example word in it is readable
from that set alone (अपना, अगर, अलग). Notes cover रु/रू, र in clusters (reph
and rakar) and the modern ं spelling for ङ/ञ.

Items drill symbol to sound, sound to symbol, composing a syllable, and
reading A1 words. The stage can be skipped with "I can read it" and brought
back later from Progress. Sounds use the browser's hi-IN voice (`tts: true`):
a consonant is spoken with its inherent a (क = ka) and a matra on क (का = kā).
hi-IN voices are common on phones and desktops, but voice quality has not
been checked on real devices. The primer is emitted by
`python3 -m packbuilder script --lang hi .` into `pack/script.json`.

This repo holds the Hindi data pack and the Hindi data files its build reads,
plus [`vocab-engine`](https://github.com/Bannerless-Studio/vocab-engine) as a git
submodule at `engine/`. The engine holds the shared UI and drill logic and
the shared pack builder, `engine/tools/packbuilder`. The builder's Hindi
rules live in `engine/tools/packbuilder/langs/hi.py`.

**Scope note:** this app is a vocabulary base for B1; an exam also needs
grammar, writing and speaking practice. The app teaches words, glosses and
example sentences, and does not teach those skills.

**Data quality.** Samples checked by hand:

| Sample | Result |
|---|---|
| Words, seed 41, 60 per level (180) | 175/180 correct primary sense before the last gloss fixes (all 5 fixed); 0 wrong part of speech |
| Sentences, seed 42, 30 per level (90) | 2 wrong links of 505 (99.6%); one more link follows a typo in the Tatoeba text |

Tatoeba has too few usable Hindi sentences for many words, so **1,034 of the
3,172 example sentences were written for this pack**. They are marked
`"src": "gen"` in `pack/sentences.json`. Their source is
`tools/generated_examples.tsv`, which has 1,093 rows; the rest are not
selected. They use pack words only and are linked through the same
pipeline as the Tatoeba text. 368 words have only written sentences. The
other 2,138 sentences come from Tatoeba. The written sentences are
machine-written and reviewed, but not by a native Hindi speaker. Tatoeba's
audio export has no recording of any Hindi sentence (checked against the
2026-09-26 export), so the pack ships no sentence audio. Speech uses the
browser's hi-IN voice. Every word has a romanisation. Known residuals are
in `TODO.md`.

## Reading passages (Read tab)

`pack/passages.json` holds 60 short reading texts, 20 each at A1, A2 and B1,
with 4-5 comprehension questions each (296 in total: 142 multiple-choice,
154 true/false). The format is in the engine's `docs/PACK_SCHEMA.md`. The
texts were written for this pack (`"src": "gen"`) and their source is
`tools/passages_src.json`. Rebuild from that source with:

```
PYTHONPATH=engine/tools python3 -m packbuilder passages --lang hi .   # --check: report only
python3 engine/tools/jsonify_pack.py pack                             # passages go into passages.js
```

The builder links word ids the same way it does for the example sentences.
It enforces in-pack coverage of at least 95% at A1 and A2 and at least 93%
at B1. It also enforces a level budget: an A1 passage may use at most 3 A2
words and no B1 words, and an A2 passage at most 3 B1 words. Word bands are
A1 60-90, A2 100-140 and B1 150-200. Words are counted as tokens, because
Devanagari matras break a regex word count. All 60 passages pass with
coverage of at least 0.98. A few nouns are declared out of pack with a
reason, for example पढ़ाई "studies" (the pack has only पढ़ाई करना).
Per-passage numbers are in `tools/REPORT_passages.md`.

Passage-only rules in `langs/hi.py`:
- hyphenated pairs (माता-पिता, धीरे-धीरे) split into their words;
- auxiliaries and vector or passive verbs (बन **गया**, ख़रीदा **जा** सकता)
  are not counted;
- a light-verb compound the pack lacks (नौकरी करना) links its parts.

The passages and questions are machine-written by Claude and checked by the
builder and a manual QA pass. They have not had a native-speaker review.

## Script and display

- The font is [Noto Sans Devanagari](https://fonts.google.com/noto/specimen/Noto+Sans+Devanagari)
  (OFL), loaded from Google Fonts, with a 1.8 line height for matras and
  conjuncts. `langTag` is `hi`, and the text runs left to right.
- Words display in the dictionary spelling, with nukta (ज़रूरत, क़दम) and
  chandrabindu (माँ, हाँ). Matching folds nukta, chandrabindu to anusvara, a
  class nasal plus virama to anusvara (हिन्दी = हिंदी), the glide spellings
  (गये = गए, रुपये = रुपए) and Devanagari digits. Only matching changes; the
  display does not. Passage texts are written in the folded spelling of those
  multi-character rules (रुपए, किराए), so their tap targets line up.
- **Romanisation** (`pron`) follows Wiktionary's IAST-like scheme: ā ī ū
  for the long vowels, ṭ ḍ ṇ for the retroflexes, ś ṣ for the sibilants, ṅ ñ
  for the velar and palatal nasals. Nukta letters are q x ġ z ṛ f. Anusvara
  is n or m before a consonant and a tilde otherwise (माँ = mā̃). The final
  schwa is dropped (कमरा = kamrā). Words Wiktionary gives no romanisation
  are transliterated with the same scheme. The primer uses it too.
- Typed production is on: `typing: {caseSensitive: false, accents: lenient,
  strictFromLevel: null}`. Lenient accents fold the nukta (a typed जरूरत is
  accepted for ज़रूरत) and, as of engine `122d88a`, also fold chandrabindu
  against anusvara (माँ = मां, हँसना = हंसना), at every level. Every fold is
  guarded against collisions with another pack word, but hindi has no
  colliding pair at either fold, so nothing is rejected on that account.

## Hindi rules (summary; details in `langs/hi.py`)

- **Tagging.** Stanza 1.14.0 with the hi default model (UD Hindi-HDTB) tags
  the corpus. Normalisation is done in `hi.py`: NFC, the folds listed above,
  and stripping the danda (।) and punctuation. Verbs are taught as
  infinitives (जाना, करना). Nouns are taught in the singular direct case and
  adjectives in the masculine direct form. Oblique and plural forms are
  `alt` forms, never entries.
- **Compound postpositions** are single words (के लिए, के बाद, के साथ,
  के बारे में, की तरफ़, के पास, की वजह से). They also form after a possessive
  or interrogative genitive (मेरे लिए, किसके लिए).
- **Light verbs and vector verbs.** Light-verb compounds (काम करना,
  शुरू करना, याद आना) and vector compounds (चला जाना, भूल जाना, दे देना) come
  from a hand list. There are 105 of them in the pack, each with linked
  sentences. The noun and the verb may have a particle or one adverb
  between them. The V2 of an unlisted compound, passive जाना and
  conjunctive कर/के link nothing. A noun after a genitive or an adjective is
  never read as a verb stem (रात की बस ली: bus, not बसना).
- **Closed sets forced to A1.** These are:
  - days, months and seasons (वसंत, with बसंत as its alt);
  - numbers 0-20, the tens, सौ and हज़ार;
  - colours (with बैंगनी), greetings and politeness words (with फिर मिलेंगे);
  - pronouns with honorific आप, possessives (with किसका) and question words
    (with कौन-सा, which links कौनसा, कौन सी and कौन-से as one word);
  - postpositions, conjunctions, particles, and the copula and auxiliaries.

  `tools/forced_a1.txt` adds an everyday A1 core list. It includes हिंदी,
  कॉलेज, रोज़, वापस, दाएँ/बाएँ and पढ़ाना, and everyday words that rank alone put
  at B1 (दादा, दादी, नींद, भूख, ठंड, and बीच to match के बीच). मामा, मिठाई, केक, सिनेमा, रिश्तेदार,
  दफ़्तर and पैदल are capped at A2 (`level_ceiling` in `hi.py`). खाना बनाना,
  संगीत, धीरे, बाल, लौटना, हवा and के नीचे are capped at A1 so the forced
  words don't push them to A2. बग़ीचा, उगाना, पूरा करना, प्रदूषण and तोहफ़ा are kept in the list past
  the cut (`keep_keys`).
  Wiktionary has no entry for कॉलेज, so `hi.py` gives it the entry of कालिज.
  सभी is taught through सब, whose gloss names it. Register is marked in
  glosses: तू intimate, तुम familiar, आप polite.
- **Spelling variants** are one word: ख़्याल, अमरीकी, अंतर्राष्ट्रीय, छुपना,
  छुपाना and बसंत link ख़याल, अमेरिकी, अंतरराष्ट्रीय, छिपना, छिपाना and वसंत,
  and ship as their alts (`SPELLING_VARIANTS`).
- **Alts are owned.** A word never carries an alt that is another pack word's
  spelling or, for a content word, a function word's form. करना has no की
  (the genitive, an alt of का with के), and की वजह से has no से.
- **Tagger POS vs pack POS.** A token tagged NOUN or DET links the pack's
  adverb or adjective with the same lemma (कल, आज, बहुत, ज़्यादा, कम). A
  reduplicated token links its word (कम-से-कम, धीरे-धीरे).
- **की as a verb** (करना's perfective) needs a ने subject or a following
  verb (मैंने मदद की, की गई). Otherwise it is the genitive (कहानी है प्यार
  और दोस्ती की).
- **Second entries** exist only for a distinct sense or part of speech with
  at least a 20% share: क्या (what / question particle), तो (particle /
  "then"), बस (just / bus).
- **Sentences.** Sentences have 3-14 tokens. A1 allows 3, A2 needs at least
  4, and B1 at least 5. Proper nouns are never linked.
- **Content policy.** The shared sensitive filter applies, with Hindi terms
  added in `hi.py`. Candidate sentences about violence, weapons, death,
  drugs or sex are held at B1 (174 in this build). Others are dropped at every
  level (98 candidates in this build):
  - rape, sexual abuse or self-harm;
  - religious or communal side-taking: a religion or country word together
    with an enmity, hatred, terror or killing word;
  - the `POLICY_HI` list: terrorism, party politics, a deity called "the
    greatest", a named current office holder, and explicit sex or nudity.

  Sex words themselves stay at B1 with written sentences. Profanity is on a
  hand list. Tatoeba text is cleaned: a final full stop becomes the danda, and
  zero-width joiners are removed.

## Sources and licences

| Data | Source | Licence | Used for |
|---|---|---|---|
| Spoken/subtitle frequency | [hermitdave/FrequencyWords](https://github.com/hermitdave/FrequencyWords) (`hi_full.txt`, OpenSubtitles 2018) | CC BY-SA 4.0 | word ranking |
| Written/general frequency | [`wordfreq`](https://github.com/rspeer/wordfreq) Python package | CC BY-SA 4.0 | word ranking |
| Glosses, POS, gender, romanisation, inflection tables | [kaikki.org](https://kaikki.org) Hindi Wiktionary extract | CC BY-SA 3.0 / GFDL | glosses, POS, `pron`, `alt` forms |
| POS tagging / lemmatisation (build time only) | [Stanza](https://stanfordnlp.github.io/stanza/) 1.14.0 (Apache-2.0), hi default model trained on UD Hindi-HDTB | model data **CC BY-NC-SA 4.0** | corpus POS and lemmas, sentence links. No model files or tagger output ship; the pack ships only words, glosses and Tatoeba/written sentences. This is the same stance as the Russian and Persian packs. |
| Example sentences | [Tatoeba](https://tatoeba.org) `hin_sentences_detailed.tsv` | CC BY 2.0 FR | sentence text (contributors in `pack/attribution.json`) |
| Sentence translations | Tatoeba `eng_sentences.tsv` + `hin-eng_links.tsv` | CC BY 2.0 FR | English translations |
| Written sentences | `tools/generated_examples.tsv`, written for this pack | same as this repo | 1,034 sentences marked `"src": "gen"` |
| Reading passages | `tools/passages_src.json`, written for this pack | same as this repo | 60 passages |
| Font | Noto Sans Devanagari via Google Fonts | SIL OFL 1.1 | display only |

Licence: code MIT, pack data CC BY-SA 4.0, see LICENSE.

No graded Hindi word list is used or shipped.

## Level bands

Candidate (lemma, POS) pairs are ranked by a blended frequency score, the
mean of the log subtitle rank and the log `wordfreq` rank.

- **A1** (600 words): every forced item, then the highest-ranked remaining
  words.
- **A2**: the next 700 by rank.
- **B1**: the next 700 by rank.

This is a reproducible proxy for CEFR level. It is not an official CEFR
classification.

## Layout

```
pack/                pack.json, words.json, sentences.json, passages.json, script.json, attribution.json (+ generated .js)
engine/              git submodule -> vocab-engine (UI, drills, tools/packbuilder, langs/hi.py)
tools/
  build_pack.py      shim: python3 -m packbuilder build --lang hi --repo .
  gloss_overrides.json   hand gloss fixes ("lemma|pos", dictionary spelling)
  forced_a1.txt      A1 core list (closed sets are in langs/hi.py)
  generated_examples.tsv   sentences written for this pack (src "gen")
  passages_src.json  reading passages source (rules + 60 passages)
  id_map_v1.json     frozen "folded lemma|pos" -> word id (keeps learner progress across rebuilds)
  requirements.txt   packbuilder deps + stanza
  REPORT.md          generated build report
  REPORT_passages.md generated passages report
build.sh             builds index.html (+ sw.js) from pack/ + engine/
check.sh             packbuilder check + engine validator + stale-build guard
```

## Rebuilding

```
git clone --recurse-submodules <this repo>
cd hindi
python3 -m venv .venv && source .venv/bin/activate
pip install -r tools/requirements.txt     # the Stanza hi model downloads into .cache/stanza on first build

python3 tools/build_pack.py               # pack/*.json + tools/REPORT.md
python3 -m packbuilder script --lang hi . # pack/script.json (PYTHONPATH=engine/tools)
PYTHONPATH=engine/tools python3 -m packbuilder passages --lang hi .
python3 engine/tools/jsonify_pack.py pack # pack/*.js
./build.sh                                # index.html + sw.js
./check.sh                                # checks + stale-build guard
```

Sources download once into `.cache/` (gitignored). The build is
deterministic: two runs from cache give byte-identical `pack/*`, the
reports, `index.html` and `sw.js`. Stanza output is cached per sentence in
`.cache/derived/`, so only the first build is slow. To build against a
vocab-engine checkout other than the submodule, set
`PACKBUILDER_PATH=../vocab-engine/tools` for `tools/build_pack.py` and
`./check.sh`. QA helpers run with
`PYTHONPATH=engine/tools python3 -m packbuilder {scan,sample} --lang hi --repo .`.
