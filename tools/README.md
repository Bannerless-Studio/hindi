# tools/ — Hindi pack builder inputs (dev/agent notes)

Purpose: everything the pack build reads or writes for the Hindi (`hi`) pack.
The actual build logic lives in vocab-engine's `engine/tools/packbuilder` and
`engine/tools/packbuilder/langs/hi.py`; this directory holds Hindi's
hand-maintained overrides plus the generated reports and frozen id map. Read
this before deciding what else in `tools/` you need to open.

## Hand-maintained (edit these to change the pack)

- `build_pack.py` — shim that runs
  `python3 -m packbuilder build --lang hi --repo .`. No Hindi logic here.
- `gloss_overrides.json` — hand gloss fixes, keyed `"lemma|pos"` in the
  dictionary spelling.
- `forced_a1.txt` — an everyday A1 core list (closed grammatical sets live
  in `langs/hi.py`).
- `generated_examples.tsv` — 1,093 candidate sentences written for this
  pack; 1,034 are selected (marked `"src": "gen"` in `pack/sentences.json`).
  Append-only.
- `passages_src.json` — source text + rules for the 60 reading passages.

## Generated (do not hand-edit; rebuild instead)

- `id_map_v1.json` — frozen `"folded lemma|pos"` -> word id map. Keeps
  learner progress stable across rebuilds; append-only even before a schema
  change.
- `REPORT.md` — full build report (word-selection funnel, sentence stats,
  CEFR cross-check). The manual QA/verdict section inside is preserved
  across regenerations.
- `REPORT_passages.md` — per-passage coverage numbers and QA notes for the
  Read tab passages.

## Not part of the pack build

- `requirements.txt` (packbuilder deps + Stanza) is installed once into
  `.venv`. Sources for the build itself (Tatoeba, FrequencyWords, wordfreq,
  kaikki Wiktionary, the Stanza hi model) download into `.cache/`
  (gitignored) and are not tracked here.

## Rebuilding from scratch

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
`.cache/derived/`, so only the first build is slow.

## Word-selection and level-band methodology

Candidate (lemma, POS) pairs are ranked by a blended frequency score, the
mean of the log subtitle rank and the log `wordfreq` rank.

- **A1** (600 words): every forced item, then the highest-ranked remaining
  words.
- **A2**: the next 700 by rank.
- **B1**: the next 700 by rank.

This is a reproducible proxy for CEFR level, not an official classification.
Full funnel numbers are in the generated `REPORT.md`.

## Hindi rules in `langs/hi.py` (summary; details and edge cases in the file)

- **Tagging.** Stanza 1.14.0 with the hi default model (UD Hindi-HDTB) tags
  the corpus. Normalisation is done in `hi.py`: NFC, folds (nukta,
  chandrabindu to anusvara, class nasal plus virama to anusvara, glide
  spellings गये/गए and रुपये/रुपए, Devanagari digits), and stripping the danda
  (।) and punctuation. Verbs are taught as infinitives (जाना, करना). Nouns
  are taught in the singular direct case and adjectives in the masculine
  direct form. Oblique and plural forms are `alt` forms, never entries.
- **Compound postpositions** are single words (के लिए, के बाद, के साथ, के
  बारे में, की तरफ़, के पास, की वजह से). They also form after a possessive or
  interrogative genitive (मेरे लिए, किसके लिए).
- **Light verbs and vector verbs.** Light-verb compounds (काम करना, शुरू
  करना, याद आना) and vector compounds (चला जाना, भूल जाना, दे देना) come from
  a hand list. There are 105 of them in the pack, each with linked
  sentences. The noun and the verb may have a particle or one adverb
  between them. The V2 of an unlisted compound, passive जाना and conjunctive
  कर/के link nothing. A noun after a genitive or an adjective is never read
  as a verb stem (रात की बस ली: bus, not बसना).
- **Closed sets forced to A1:** days/months/seasons (वसंत, alt बसंत);
  numbers 0-20, the tens, सौ and हज़ार; colours (with बैंगनी), greetings and
  politeness words (with फिर मिलेंगे); pronouns with honorific आप,
  possessives (with किसका) and question words (with कौन-सा, linking
  कौनसा/कौन सी/कौन-से as one word); postpositions, conjunctions, particles,
  and the copula and auxiliaries. `tools/forced_a1.txt` adds an everyday A1
  core list, including a handful of level caps (`level_ceiling`) and kept
  past-cut items (`keep_keys`); see the file for the exact word list.
  Wiktionary has no entry for कॉलेज, so `hi.py` gives it the entry of कालिज.
  सभी is taught through सब, whose gloss names it. Register is marked in
  glosses: तू intimate, तुम familiar, आप polite.
- **Spelling variants** are one word: ख़्याल, अमरीकी, अंतर्राष्ट्रीय, छुपना,
  छुपाना and बसंत link ख़याल, अमेरिकी, अंतरराष्ट्रीय, छिपना, छिपाना and वसंत,
  and ship as their alts (`SPELLING_VARIANTS`).
- **Alts are owned.** A word never carries an alt that is another pack
  word's spelling or, for a content word, a function word's form. करना has
  no की (the genitive, an alt of का with के), and की वजह से has no से.
- **Tagger POS vs pack POS.** A token tagged NOUN or DET links the pack's
  adverb or adjective with the same lemma (कल, आज, बहुत, ज़्यादा, कम). A
  reduplicated token links its word (कम-से-कम, धीरे-धीरे).
- **की as a verb** (करना's perfective) needs a ने subject or a following
  verb (मैंने मदद की, की गई). Otherwise it is the genitive (कहानी है प्यार और
  दोस्ती की).
- **Second entries** exist only for a distinct sense or part of speech with
  at least a 20% share: क्या (what / question particle), तो (particle /
  "then"), बस (just / bus).
- **Sentences.** 3-14 tokens. A1 allows 3, A2 needs at least 4, and B1 at
  least 5. Proper nouns are never linked.
- **Content policy.** The shared sensitive filter applies, with Hindi terms
  added in `hi.py`. Candidate sentences about violence, weapons, death,
  drugs or sex are held at B1 (174 in this build). Others are dropped at
  every level (98 candidates): rape/sexual abuse/self-harm; religious or
  communal side-taking (a religion or country word with an enmity, hatred,
  terror or killing word); the `POLICY_HI` list (terrorism, party politics,
  a deity called "the greatest", a named current office holder, explicit
  sex or nudity). Sex words themselves stay at B1 with written sentences.
  Profanity is on a hand list. Tatoeba text is cleaned: a final full stop
  becomes the danda, zero-width joiners are removed.

## Reading passages (`passages_src.json`, `langs/hi.py`)

The builder links word ids the same way it does for example sentences and
enforces in-pack coverage of at least 95% at A1/A2 and 93% at B1, plus a
level budget (an A1 passage may use at most 3 A2 words and no B1 words; an
A2 passage at most 3 B1 words). Word bands are A1 60-90, A2 100-140, B1
150-200, counted as tokens (a regex word count breaks on Devanagari
matras). All 60 passages pass at coverage >= 0.98. Passage-only rules in
`langs/hi.py`:

- hyphenated pairs (माता-पिता, धीरे-धीरे) split into their words;
- auxiliaries and vector or passive verbs (बन **गया**, ख़रीदा **जा** सकता)
  are not counted;
- a light-verb compound the pack lacks (नौकरी करना) links its parts.

## Builder deviations (shared hooks not added)

- `hi.py` does normalisation itself (NFC, nukta, chandrabindu, class nasal,
  glide ये/यी, Devanagari digits); `indic_nlp_library` is not used.
- The lexicon's frequency lookups read a folded wordfreq table, patched in
  `bind_lexicon` (picklable callables), because no shared hook exists.
- The shared sentence regex cannot count Devanagari words, so passages use
  `passage_words_counted`. Spans use `span_fold`, which is per-character, so
  passage text is written in the folded spelling of the multi-character
  rules (रुपए, not रुपये).
