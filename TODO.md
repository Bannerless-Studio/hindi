# TODO (v2 candidates)

Residuals from the v1 build and QA. The rules already in place are in
`engine/tools/packbuilder/langs/hi.py` and summarised in the README.

## Before publishing
- `engine/` points at vocab-engine `0341bc5` (origin/main), which does not
  have `langs/hi.py` yet. Until that file is committed upstream and the
  submodule is bumped, build and check with
  `PACKBUILDER_PATH=../vocab-engine/tools`.
- Nothing in this repo is committed yet. `./check.sh` passes the packbuilder
  check and `validate_pack.py` (0 errors, 18 warnings). The stale-build
  guard fails only because `index.html` and `sw.js` are not tracked or
  committed.

## Sentences
- 1,034 of 3,172 sentences were written for the pack, and 368 words have only
  written sentences. More Tatoeba Hindi with English links would replace
  them.
- There is no audio. Tatoeba's audio export has no Hindi recordings
  (checked 2026-09-26; the scout's "105 permissive clips" did not reproduce).
  Synthetic audio (Piper) is the likely path, as for Persian and Urdu.
- Link residuals from the seed 42 sample (2 of 505 wrong):
  - भरती, a variant spelling of भर्ती "recruitment", reads as a form of भरना
    "to fill" (भरती होना).
  - A possessive before a directional adverb and a verbal noun (उसके बाहर
    जाने की आवाज़ "the sound of him going out") reads as the compound
    postposition के बाहर. उसके पीछे भागना "run after him" needs the compound,
    so no surface rule separates them.
- Minor, not counted as wrong: से in फिर से "again" links the postposition.
  Tatoeba typos (कि for की, को for कोई) link as written.
- Nukta folding merges सज़ा "punishment" with सजा, the stem of सजाना "to
  decorate". A noun after a genitive or an adjective is kept as a noun (a
  class fix that took सजाना out of the pack, since its count came from
  सज़ा दी). A bare उसे सज़ा दी can still read as सजाना.

## Words and glosses
- Same-sense duplicate entries kept by the second-entry rule: वही, ठीक
  (adj and adv), बाक़ी, मूर्ख, विरोधी. चीनी (sugar / Chinese) is a real pair.
- Nationality adjectives have one-word glosses (Indian, American, Chinese).
  The scan flags them as single-capital glosses; they are correct.
- Religious nouns (भगवान, अल्लाह, परमेश्वर, ईश्वर, हिंदू, मुसलमान) are kept
  with neutral glosses. Sentences that pair a religion or country with
  enmity words are dropped.
- बीमार is A1 but बीमार होना "to be ill, to fall ill" is B1. बीमार है links the
  compound, so A1 passages avoid it.
- खाना is one entry ("to eat; (noun) food"). The noun has no entry of its
  own, because the second-entry rule found no separate share over 20%.
- Frequency floor: `min_corpus_tokens` is 1, not 3, because the Hindi
  Tatoeba corpus is small (13,286 sentences with English).

- Requested words not added: रेस्टोरेंट, संग्रहालय, पल, रंगीन and कुल have no
  Tatoeba tokens, so they are not candidates. Forcing them would put them at A1.
  पच्चीस (25) is outside the closed numeral set. सभी is a form of सब, not a
  lemma. Adding the requested words dropped नाज़ुक, न्यायालय, एशियाई, क़ाबू, दूत
  at the B1 cut. तोहफ़ा is kept by `keep_keys` (no passage uses it now). The forced
  words pushed केंद्र, नंबर, प्रदेश, यात्रा करना, शिक्षा, चुनना, ज़मीन, मालिक,
  साथी, ठीक, हे, विकास, राष्ट्रपति, भारतीय, विश्वास, जल्द and उम्र from A1 to A2.
  उम्र and ठीक are debatable at A2.
- The QA round of 2026-09-26 moved सहायक, मुस्लिम, ग़ुस्सा, कृपा, चीनी, सक्रिय,
  जोखिम and सफ़ाई from A2 to B1. Merging spelling variants freed five places.
- लेना has no लिए/लिये alt, because के लिए owns that form. A perfective ले लिए
  is not highlighted as लेना.

## Script primer
- ङ and ञ have no example words at A1-B1; a note gives the modern ं spelling.
  Several units (ओ, द्ध, ऋ, ऑ, visarga, ज्ञ, श्र, द्य) take their first example
  from A2 (validator warnings).
- `tts: true` relies on a hi-IN browser voice. It has not been checked on
  real devices.

## Passages
- Word bands are A1 60-90, A2 100-140 and B1 150-200, per the passages
  brief. The Persian rules block uses 90-120 for A2 and 110-150 for B1.
- mc keys found verbatim in the text: A1 5/40, A2 2/43, B1 2/59. The correct
  option is the unique longest in 10 of 142 mc items (7%).
- Out-of-pack nouns declared with a reason: पढ़ाई "studies" (the pack has
  पढ़ाई करना) and दौड़ "race" (the pack has दौड़ना).
- The name सीमा was renamed ललिता in 4 passages, because सीमा is a pack word
  ("border").
- Passage-only rules in `hi.py`: hyphen splitting, uncounted auxiliaries
  and vector verbs, splitting of non-pack compounds, and an undeclared PROPN
  re-read as a common word. The corpus build does not use them. Try them on
  the corpus in v2.

## Builder deviations (shared hooks not added)
- `hi.py` does normalisation itself (NFC, nukta, chandrabindu, class nasal,
  glide ये/यी, Devanagari digits); `indic_nlp_library` is not used.
- The lexicon's frequency lookups read a folded wordfreq table, patched in
  `bind_lexicon` (picklable callables), because no shared hook exists.
- The shared sentence regex cannot count Devanagari words, so passages use
  `passage_words_counted`. Spans use `span_fold`, which is per-character,
  so passage text is written in the folded spelling of the multi-character
  rules (रुपए, not रुपये).
