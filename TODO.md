# TODO (v2 candidates)

Residuals from the v1.1 build and QA (2026-09-26, engine `ac2a8c1`). The
rules are in `engine/tools/packbuilder/langs/hi.py` and summarised in the
README.

## Sentences
- 1,034 of 3,172 sentences were written for the pack, and 368 words have only
  written sentences. More Tatoeba Hindi with English links would replace
  them.
- There is no audio. Tatoeba's audio export has no Hindi recordings
  (checked 2026-09-26). Synthetic audio (Piper) is the likely path, as for
  Persian and Urdu.
- Minor, not counted as wrong: से in फिर से "again" links the postposition.
  The pack has no separate फिर से adverb entry (फिर alone already glosses
  "then; again"), so the "add फिर से as a unit" rule doesn't apply; left as
  documented. Tatoeba typos (कि for की, को for कोई) link as written.
- A possessive before a directional adverb and a verbal noun (उसके बाहर
  जाने की आवाज़ "the sound of him going out") reads as the compound
  postposition के बाहर. उसके पीछे भागना "run after him" needs the compound,
  so no surface rule separates them.

## Words and glosses
- Religious nouns (भगवान, अल्लाह, परमेश्वर, ईश्वर, हिंदू, मुसलमान) are kept
  with neutral glosses. Sentences that pair a religion or country with
  enmity words are dropped. मुस्लिम "Muslim" is a religion adjective, not a
  nationality one, and is out of the v1.1 nationality-gloss fix below.
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
- लेना has no लिए/लिये alt, because के लिए owns that form. A perfective ले लिए
  is not highlighted as लेना.

## v1.1 QA (2026-09-26, engine `ac2a8c1`)
Classes found and fixed; per-class counts are before -> after the engine
bump + hi.py fix (2000-word pack, 3172 -> 3170 sentences after the चीज़
drops moved one word past the B1 cut and the rank shuffle net -2 sentences).
- **Bad Tatoeba translations (false friend):** चीज़ "thing" glossed as
  "cheese" (पनीर is the real word) in 5 corpus rows, 2 of them in the pack
  (s0336, the old s1765/new s1749 "मैंने बहुत सारा चीज़ दिया।"). All 5 are now
  in `bad_text_re`, dropped at every level.
- **Noun spelling variant colliding with a verb inflection:** भरती, an
  unhaltanted spelling of भर्ती "recruitment, admission", is also भरना's
  feminine participle "filling". Audited every pack noun for a
  halant/nukta/chandrabindu-drop that collides with a verb stem+ending;
  भरती/भरना is the only instance. Before: भरती + होना/करना misread as भरना
  (1 sentence, s1249/old id, "टॉम को ... वायु सेना में भरती होना था"). After:
  भर्ती + होना/करना/कराना resolves to the noun भर्ती, matching the halant
  spelling's own sentences (494199, 9013203, 9976048 in the corpus).
- **Nukta-distinct lemma pair:** सज़ा "punishment" folds (nukta dropped) to
  सजा, the stem of सजाना "to decorate". Audited every such pair with a
  corpus sense on both sides; सज़ा/सजा is the only one (सजाना itself has no
  pack entry — its only corpus support was misread सज़ा दी). Before: a bare
  सज़ा + दी/हुई/सुनाई (no preceding genitive/adjective) could still misread
  as सजाना. After: सज़ा stays the noun before देना/होना/मिलना/पाना/सुनाना/
  भुगतना regardless of what precedes it (`NUKTA_NOUN_LV` in hi.py).
- **Second-entry rule admits a same-sense alt-POS pair:** वही (pron "that"
  vs. det "same"), ठीक (adv "well" vs. adj "fine, good, okay"), बाक़ी (adj
  "remaining" vs. noun "the rest"), मूर्ख (adj "stupid" vs. noun "a fool"),
  विरोधी (adj "opposing" vs. noun "an opponent") each held 2 pack entries;
  the English-gloss-overlap heuristic (`SECOND_SENSE_OVERLAP`) read them as
  distinct senses. Before: 5 words x 2 entries = 10 pack slots. After: 5
  entries, `drop_keys` redirects the dropped POS's sentences to the
  survivor, whose gloss merges both senses (hindi/tools/gloss_overrides.json).
  चीनी (sugar noun / Chinese adj) is a real pair and is unchanged.
- **Nationality adjective one-word gloss:** भारतीय, अमेरिकी, अंग्रेज़ी,
  ब्रिटिश, रूसी, चीनी, जापानी glossed with just the English demonym (Indian,
  American, ...), risking a learner reading it as the country name rather
  than the grammatical adjective. All 7 now read "<Demonym> (nationality/
  adj)" (hindi/tools/gloss_overrides.json). मुस्लिम "Muslim" is a religion
  adjective and was left alone.
- **Passage residual from the engine bump:** the 2b1e21e V2/passive-जाना
  link fix reshuffled corpus-derived word ranks pack-wide (link resolution
  feeds frequency); रुक जाना moved from A2 to B1, breaking an A1 passage
  (p0020) that used it. Rewrote that sentence to the bare A1 verb रुकना
  ("बारिश रुक जाती है" -> "बारिश रुकती है").
- Live check from the v1 QA round, now checked off: "मैंने बहुत सारा चीज़
  दिया।" (dropped, see above); के लिए / धीरे धीरे highlighting are engine-side
  and unrelated to this pack's data, left for the engine.

## Typed production
- RESOLVED (engine `122d88a`). `typing.accents: lenient` now folds
  chandrabindu against anusvara (माँ = मां, हँसना = हंसना) as well as the
  nukta, at every level (PACK_SCHEMA.md "Lenient typing letter folds"). Every
  lenient fold is guarded: a typed answer that matches only after folding is
  rejected when it spells another pack word. Hindi has no colliding pair for
  either fold (PACK_SCHEMA.md's collision table lists 0 for hindi), so the
  guard never rejects a hindi answer in practice; it stays correct if a
  future word addition creates one.

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

## Engine, low priority
- Multi-token units (के लिए) link as one entry but only the tapped half
  highlights; a reduplicated query ("धीरे धीरे") doesn't fold to the single
  lemma. Both engine-side.

## v1.1 spot-QA residuals (2026-09-26, KEEP LIVE)
Sample of 60 sentences with changed links: 351 links, 2 wrong (both old). All 60 new
V2/passive जाना links correct. Classes to fix in `langs/hi.py` for v1.2:
- Bare stem + होना is always the noun (लूट हुई links लूटना; also जीत, हार, मार).
  The सज़ा fix special-cases one word (NUKTA_NOUN_LV); generalise it in the stem block.
- दिखाई/सुनाई देना (LIGHT_VERBS, not pack words) fall to the causative-stem rule and link
  दिखाना/सुनाना + देना (7 sentences). Link only the light verb when the compound is not in
  the pack, as the passage path already does, or add both compounds.
- Colloquial करा (= किया) links कराना (s0732, s1822).
- Level drift from V2 linking: राष्ट्रपति A2→A1 (should stay A2), ध्यान देना A2→B1 (too
  high). Floor listed compounds or keep a hand A1 list. Other 11 level moves are sane.
- Merged entries lost sense examples: वही "same" (det) has no sentence; ठीक "fine/okay"
  has none and "ठीक सामने" (exactly) is not in the gloss. Merge rule should require ≥1
  example per merged sense. रख लेना dropped by rank drift.
- p0020 distractor "सो जाते हैं" is an A2 compound inside an A1 passage.

## Republish on engine 0e2bb0c
- ~~passages --check reports 1 pre-existing error: p0016 links the B1 word सुनाना
  at A1.~~ **Fixed 2026-09-28** (uncommitted, for the republish wave): the link
  was right (सुनाती = सुनाना, moved A2 -> B1 by a words rebuild); p0016 sentence 8
  and question 3 now use बताती (बताना A1). passages rebuilt (0 errors). Class
  guard on vocab-engine branch engine-data-fixes: `packbuilder check` (check.sh)
  now fails when a shipped passage links a word above its level budget, so a
  words rebuild that re-levels a passage word fails before shipping.

Republish dbbf541: 6 reduplication cloze gaps (धीरे-धीरे etc.) now blank the whole token (engine locateWord).

## Republish 09e90bc (2026-09-29)
- Republish 09e90bc: sentence spans (17882/17946 linked words placed); inflected forms now cloze targets
- Republish ef44c6e: बम, विस्फोट, शराब A2→B1 (quota: चीनी, कृपा, सक्रिय B1→A2); deleted override key अंतर्राष्ट्रीय|adj; set-counter and no-voice planner fixes. Known: चीनी "sugar" still links the "Chinese" sentence मेरी को चीनी समझ में आती है (now at A2)

## Republish 0d542de (2026-10-08, port wave 3)
- Republish 0d542de: typed modes, day-aware scheduling, reading rotation, goals, pairs, frequency tiers, Progress v2, redesigned tabs, session estimates. Pack diff vs df54d3e: every word gains `ft` (100 ambient / 1385 core / 515 peripheral), pack.json gains the generic flag set + `eta`; nothing else (tools/eta.json: ETA curves from 3 sims at 85%, 400 sessions, seeds 5/6/7; gate on seeds 8/9/10 passes for all 3 goals and the A1/A2 level gates; `check.sh` runs `packbuilder enrich --check`).
- Migration proof: rollback hash df54d3e399defbf21f30a09fb01cc0bf0139fd58; previous live md5 index a63ee9756c9409f4cf6b75017d239d80, sw 237b4773d659a0ffbf9130885cb49b54. Storage: new fields day/sn/t/u/f/p/pm/pv/pause/read.done s,ls/today.tw on first use; boot writes nothing; previous build ef44c6e/aa00571 carries them (migration [port] 9/9).
- Live proof 6822584 (2026-10-08) KEEP: 12-session seed (primer learned) from df54d3e byte-equal after boot/reload/Progress open (leaving Progress adds only prog.pv); one Today session writes w/sets/sessions/script/sn/day/pm; that record boots on df54d3e byte-equal (boot + reload), one key vocab_hi, a session runs there; back on live byte-equal; 0 console errors, 0 failed requests.
