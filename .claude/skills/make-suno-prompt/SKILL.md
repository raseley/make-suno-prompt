---
name: make-suno-prompt
description: "Convert a user's musical intent — a sentence or paragraph describing what they want Suno to generate — into a ready-to-paste Suno Custom Mode configuration: Style-of-Music field text, Lyrics/Arrangement field text, Exclude Styles field, Instrumental toggle, and slider settings. Use this skill whenever a user wants to construct a Suno prompt, has an instrumental or vocal track concept to generate, asks for a 'Suno prompt for X,' or asks to refine/debug a prompt that is producing muddy mixes, wandering structures, looping stagnation, or phantom vocal leaks."
---

# Make Suno Prompt

You convert a user's musical intent into a complete Suno Custom Mode setup — the five concrete inputs pasted or configured in Suno (Style-of-Music field, Lyrics/Arrangement field, Exclude Styles field, Instrumental toggle, and Control Sliders) — by treating each of the audio-manifold dimensions as an explicit coordinate. Every unconstrained dimension represents an underformed piece of musical intent. Surface those gaps as residual questions rather than silently hallucinating generic fillers.

## Self-description

> Place the user's input on the audio manifold across its primary dimensions (genre/sub-genre, tempo/meter/key, timbre/instrumentation, mood/affect, spatial/production), diagnose which dimensions are explicit, implicit, or missing, compose a dual-layer Style field (concept anchor plus atomic G-M-E-P tags), a dynamic bracketed Lyrics/Arrangement DAG, an artifact-suppressing Exclude Styles string, and exact slider calibrations, run a manifold check enforcing anti-bleed and anti-distortion rules, and report the pasteable artifacts with a targeted residual-gaps list.

## Rationale & Core Concepts

*A Suno prompt is a coordinate-specification mechanism inside Suno's audio-semantic manifold. Prompts fail geometrically: the target region does not overlap with the requested track. Surface rephrasing does not solve this; disciplined dimensional locking does.*

*For instrumental compositions in particular, the audio engine lacks vocal phonemes to anchor arrangement transitions or mask audio artifacts. Generating studio-grade instrumentals requires a **Quadruple Lock** against hallucinated vocals and frequency mud: (1) Instrumental toggle enabled, (2) `instrumental` style tag, (3) bracketed arrangement cues in the Lyrics field, and (4) strict vocal/artifact filters in the Exclude Styles field.*

*Leaving the Lyrics field empty for instrumentals forces the model into stochastic, repetitive 60-second loops. Populating the Lyrics field with **bracketed arrangement choreography** (`[Section - instrumentation, dynamic, tempo]`) provides temporal narrative and progression while forbidding unbracketed text prevents the model from attempting to sing or mumble phonetic cues.*

---

## Reference: The Audio-Manifold Dimensions

Every calibrated Suno generation positions sound across these primary axes:

| Dimension | Constrained By | High-Fidelity Example |
|---|---|---|
| **Genre / Sub-genre & Era** | Specific sub-genres, regional scenes, era anchors | `1970s ECM Nordic jazz`, `1990s Bristol trip-hop`, `modern hybrid cinematic orchestral` |
| **Tempo, Meter & Key** | Explicit BPM, time signatures, harmonic tonality | `82 BPM, D minor`, `140 BPM, 6/8 meter, E dorian` |
| **Timbre & Instrumentation** | Role-separated instruments (solo lead vs. rhythm bed) | `lead cello melody, fingerpicked acoustic guitar, bowed double bass, sparse timpani` |
| **Mood & Affect** | Contextual scenes, through-line clauses, emotional stance | `intimate but unhurried`, `tension without resolution`, `cold 3 AM observation deck` |
| **Spatial & Production** | Stereo field, room acoustics, saturation, dynamics | `close-mic intimate, warm room acoustics, natural stereo width, tape warmth` |

---

## Reference: Custom Mode Field Architecture

1. **Style of Music Field (~1,000 chars)**: The acoustic coordinates. Formatted as **Concept Anchor + G-M-E-P Atomic Tags**:
   * **Concept Anchor**: 1–2 sentences establishing scene, acoustics, listener posture, and dimensional coupling.
   * **G-M-E-P Tags**: 4–7 comma-separated descriptors covering **G**enre/era, **M**ood, **E**nsemble/instruments, and **P**roduction space. Front-load genre, era, BPM, and key.
2. **Lyrics / Arrangement Field (~3,000 chars)**: The traversal path through time.
   * *For Vocal Tracks:* Section metatags (`[Verse]`, `[Chorus]`, etc.) paired with lyrical lines.
   * *For Instrumental Tracks:* A sequential Directed Acyclic Graph (DAG) of bracketed arrangement cues (`[Section - instrumentation, dynamics, tempo]`). Never leave completely blank. Never write prose outside brackets.
3. **Exclude Styles Field (Negative Prompt)**: The boundary fence. Suppresses vocal bleed, digital phase artifacts, clipping, and frequency muddiness.
4. **Instrumental Toggle**: Boolean (`true` | `false`).
5. **Control Sliders**:
   * **Style Influence (70–80%):** Enforces strict adherence to specified tempo, key, and instrumentation. Lower only if intentional genre blending is desired.
   * **Weirdness / Variety (35–50%):** Balances melodic coherence against monotony. Settings above 60% trigger harmonic clashing and structural decay in long instrumental sections.

---

## Reference: Construction Rules (The Objective Floor)

1. **The Quadruple Lock for Instrumentals:** If the track is instrumental, toggle = `true`, `instrumental` must appear in Style tags, the Lyrics field must contain bracketed arrangement cues, and Exclude Styles must suppress vocal forms.
2. **Lyrics Field Cues Must Be Bracketed:** In instrumental mode, write *only* inside brackets (e.g., `[Theme A - solo cello enters, gentle acoustic fingerpicking]`). Text outside brackets will occasionally be chanted or whispered by the synthesis engine.
3. **Role-Separated Instrumentation:** Do not list vague instrument clouds (`guitars, synths, drums`). Specify 2–4 distinct instruments partitioned by register and role (lead melody vs. rhythm foundation).
4. **Tone & Tempo Anchors:** Always specify explicit BPM and musical keys where harmonic stability is critical.
5. **4–7 Atomic Tags:** Anchors and bracketed cues do not count toward this limit. Under four tags leaves the space underconstrained; over seven fragments the prompt and causes dimensional competition.
6. **No Artist Names:** Use dimensional descriptors and regional/era anchors instead of artist handles.
7. **Anti-Distortion Rule:** Avoid stacking high-gain descriptors (`heavy distortion`, `cranked limiter`, `saturated fuzz`) in the positive prompt, as Suno's engine collapses saturated elements into phase-smeared digital artifacting. Describe timbral weight instead (`thick fuzz bass`, `driven tube preamp warmth`).
8. **Outro Finality:** Tracks without explicit resolve cues loop or cut off abruptly. Always terminate instrumental structures with dynamic resolution tags (e.g., `[Outro - decrescendo, natural reverb tail] [End]`).

---

## Reference: Diagnostic Table

| Symptom | Geometric / Algorithmic Cause | Fix |
|---|---|---|
| **Vocal bleed / whispering in instrumental** | Single-channel constraint failure | Verify toggle is ON, add `instrumental` to tags, fill Exclude Styles with vocal filters. |
| **Track stagnates into repetitive 60s loops** | Empty Lyrics field; missing traversal path | Add bracketed dynamic cues (`[Build]`, `[Theme B]`, `[Breakdown]`) in Lyrics box. |
| **Muddy, phasey high frequencies / swishing** | Excessive high-gain tags or overconstrained mix | Remove distortion/limiter terms; add `harsh highs, phase issues, clipping` to Exclude Styles. |
| **Wandering harmonic progressions / sour notes** | Weirdness slider set too high (>60%) | Drop Weirdness to 35–45%; specify exact musical key (e.g., `D minor`). |
| **Generic, stock-library feel** | Weak sub-genre coordinate; no anchor coupling | Replace top-level genre with micro-genre + era; add 1–2 sentence concept anchor. |
| **Abrupt ending or endless fade** | Missing terminating cue | Terminate arrangement with `[Outro - natural acoustic decay] [End]`. |

---

## Procedure

Execute this five-step sequence: **Place → Diagnose → Compose → Manifold Check → Report**.

### Step 1 — Place the Input on the Audio Manifold
Locate the user's intent in coordinate space: identify target genre/sub-genre, era, emotional posture, tempo/energy feel, and instrumentation. Identify whether the intent is vocal or instrumental. If the input is a single ambiguous word, ask one targeted clarifying question before generating.

### Step 2 — Diagnose the Five Dimensions
Evaluate the source across the primary dimensions. Mark each as:
* *Explicit:* Directly stated by the user.
* *Implicit:* Conservatively inferable from context (e.g., "1970s ECM jazz" implies warm room acoustics, double bass, and dynamic acoustic pianos).
* *Missing:* Unspecified. Do not invent filler; route missing dimensions to the residual-gaps list.

### Step 3 — Compose the Generation Artifacts
1. **Style of Music:** Draft a 1–2 sentence Concept Anchor that couples scene, room, and posture. Follow with 4–7 atomic tags (Genre/Era, BPM/Key, Specific Solo & Rhythm Instrumentation, Spatial/Mix qualities).
2. **Lyrics / Arrangement:** 
   * If instrumental: Draft a sequential, bracketed arrangement DAG detailing dynamic shifts, instrumentation entries, and a resolving `[End]`.
   * If vocal: Draft structural metatags (`[Verse]`, `[Chorus]`, etc.) with provided lyrics or placeholders.
3. **Exclude Styles:** Populate negative constraints. For instrumentals, always enforce anti-vocal and anti-artifact strings.
4. **Instrumental Toggle & Sliders:** Set toggle (`true` or `false`). Calibrate Style Influence (default 70–80%) and Weirdness/Variety (default 35–50%).

### Step 4 — Manifold Check
Verify against the objective rules:
* Are all instrumental channels locked (Toggle, Style tag, Lyrics DAG, Exclude)?
* Are bracketed arrangement cues cleanly written with zero unbracketed text?
* Are atomic tags within the 4–7 count?
* Are sub-genre, era, BPM, and key front-loaded?
* Are artist names eliminated?
* Are contradictory descriptors eliminated?

### Step 5 — Report
Output using this exact structure:

**Diagnosis**
[2–4 sentences evaluating explicit, implicit, and missing dimensions. Name any identified failure modes if diagnosing a previous generation.]

**Style of Music** (paste into Style of Music field)

> [Concept Anchor prose. 4–7 atomic tags: Sub-genre/Era, BPM/Key, Instrumentation, Spatial/Mix.]

**Lyrics / Arrangement** (paste into Lyrics field)

> [Bracketed arrangement cues DAG for instrumentals, or metatags + lyrics for vocal tracks]

**Exclude Styles** (paste into Exclude Styles field)

> [Negative prompt tags targeting vocals, bleed, clipping, and mix artifacts]

**Generation Controls**

* Instrumental Toggle: [true | false]
* Style Influence: [70–80%]
* Weirdness / Variety: [35–50%]

**Residual Gaps**

* [Dimension]: [Targeted question for missing or ambiguous axes]

---

## Worked Examples

### Example 1: Ambient Study Track (Underconstrained Input)

**Source:** *"I want a chill ambient track to study to. No words."*

**Diagnosis:** Coarse placement is low-energy functional ambient. Top-level genre is named ("ambient"), but sub-genre, era, instrumentation, tempo, key, and production are completely open. The user explicitly committed to instrumental ("no words"). Missing dimensions must be surfaced as residual gaps rather than invented.

**Style of Music**:
> A quiet room on a rainy afternoon, attention resting completely on the desk. Warm ambient, 65 BPM, slow analog synth pads, soft electric piano, gentle tape warmth, intimate stereo width, instrumental.

**Lyrics / Arrangement**:
> [Intro - soft analog pad swell, tape hiss, 65 BPM]
> [Section A - gentle electric piano chords enter, wide reverb]
> [Texture - subtle sub-bass warmth, slow filter sweep]
> [Outro - electric piano fades, sustaining pad decay]
> [End]

**Exclude Styles**:
> vocals, voice, singing, humming, choir, chanting, drums, percussion, harsh highs, clipping

**Generation Controls**:
- Instrumental Toggle: true
- Style Influence: 75%
- Weirdness / Variety: 35%

**Residual Gaps**:
- Genre/sub-genre: Ambient encompasses distinct spaces—drone ambient, warm lo-fi, Berlin school, or modern neoclassical. Which direction fits your study environment?
- Timbre/instrumentation: Do you prefer synthetic textures (analog pads), acoustic elements (felt piano, cello), or organic field recordings?
- Spatial/production: Intimate and tape-saturated, or vast, cavernous, and cinematic?

---

### Example 2: Cinematic Orchestral Action (Contradiction & Dynamic Structuring)

**Source:** *"Epic massive battle music, hyper-aggressive with huge heavy metal distortion, blast beats, but also pure acoustic orchestral strings and quiet meditation."*

**Diagnosis:** Coarse placement is hybrid cinematic/metal. The prompt suffers from severe overconstraint and sonic contradiction: "quiet meditation" directly opposes "hyper-aggressive blast beats", and stacking heavy metal distortion on top of orchestral strings creates high-frequency phase smearing in Suno's audio engine. The composition is stabilized by resolving the contradiction into a dynamic narrative arc (building from quiet string tension to a heavy hybrid climax) while moving the high-gain descriptors into disciplined acoustic/hybrid tags.

**Style of Music**:
> The tense silence before the vanguard charges across open ground. Modern hybrid cinematic orchestral, 145 BPM, D minor, driving staccato strings, heavy taiko percussion, aggressive synth bass, brass fanfares, wide dynamic range, instrumental.

**Lyrics / Arrangement**:
> [Intro - quiet solo cello motif, low tension drone, 145 BPM, D minor]
> [Build - staccato violins enter, rhythmic taiko percussion swells]
> [Main Theme - full brass fanfare, driving orchestral rhythm bed]
> [Climax - massive hybrid percussion impact, frantic ascending string runs]
> [Outro - heavy stinger hit, natural orchestral hall reverb tail]
> [End]

**Exclude Styles**:
> vocals, singing, choir, speech, distorted clipping, fuzzy high-end, muddiness, tape hiss

**Generation Controls**:
- Instrumental Toggle: true
- Style Influence: 80%
- Weirdness / Variety: 40%

**Residual Gaps**:
- Mood/affect (resolved contradiction): The source asked for quiet meditation alongside hyper-aggressive blast beats. The prompt structures this as a dynamic build from tension to climax. If you prefer a pure death-metal arrangement instead of an orchestral hybrid, the genre coordinate must be completely relocated.

---

### Example 3: Expressive Solo Jazz (Clean, Fully Specified Input)

**Source:** *"A 1970s ECM-style Nordic jazz instrumental. Melancholic, sparse, unhurried. 78 BPM in G minor. Solo lead cello melody, delicate ECM grand piano, brushed double bass. Close-mic recording, warm studio room acoustics."*

**Diagnosis:** Fully constrained coordinate. Sub-genre, era, mood, explicit BPM, musical key, role-separated instrumentation, acoustic space, and instrumental commitment are all clearly locked. No residual gaps exist; the conversion formats the input directly into the four execution artifacts without synthetic inflation.

**Style of Music**:
> Cold daylight across an empty Scandinavian coastline, desolate but peaceful. 1970s ECM Nordic jazz, 78 BPM, G minor, expressive solo cello melody, delicate acoustic piano, brushed double bass, warm room acoustics, unhurried phrasing, instrumental.

**Lyrics / Arrangement**:
> [Intro - sparse acoustic piano chords rubato, warm room tone]
> [Theme - solo cello enters with primary melody, 78 BPM, G minor]
> [Development - brushed double bass provides subtle low-end accompaniment]
> [Cello Solo - expressive melodic variations, dynamic piano response]
> [Outro - cello sustains final tonic note, piano chord ring-out, natural decay]
> [End]

**Exclude Styles**:
> vocals, singing, humming, choir, synthesizers, electric instruments, harsh transients, digital distortion, clipping

**Generation Controls**:
- Instrumental Toggle: true
- Style Influence: 78%
- Weirdness / Variety: 38%

*(No residual gaps — all five dimensions and production parameters are constrained.)*
