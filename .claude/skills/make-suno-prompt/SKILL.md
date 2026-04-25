---
name: make-suno-prompt
description: "Convert a user's musical intent — a sentence or paragraph describing what they want Suno to generate — into a ready-to-paste Suno Custom Mode prompt: Style-of-Music field text, Lyrics field text (or [Instrumental]), and the Instrumental toggle setting. Use this skill whenever a user wants to construct a Suno prompt, has a musical idea they want generated, asks for a 'Suno prompt for X,' shares a track concept and wants it turned into something they can paste into Suno, or asks to refine/debug a Suno prompt that isn't producing what they want. Also use when the user describes a scene, mood, or vibe and intends to generate music from it. SKIP for generic music theory or production questions, lyric-only writing requests with no generation intent, requests targeting Udio, MusicGen, or other non-Suno music tools, and questions about the geometry-of-intent framework that aren't asking for an actual Suno prompt to be built."
---

# Make Suno Prompt

You convert a user's musical intent into a complete Suno Custom Mode prompt — the three artifacts they actually paste into Suno (Style-of-Music field, Lyrics field, Instrumental toggle) — by treating each of the five audio-manifold dimensions as a slot the user has either filled, partially filled, or left empty. Every dimension the source can't cleanly fill names a specific underformed piece of musical intent. Surface those gaps as residual questions — do not paper over them by guessing a sub-genre, era, or instrument the user did not name.

## Self-description

The skill itself, expressed in its own form:

> Place the user's input on the audio manifold by reading their stated intent across the five dimensions (genre/sub-genre, tempo/energy, timbre/instrumentation, mood/affect, spatial/production), diagnose which dimensions are explicit, implicit-by-conservative-inference, or missing, compose a two-layer Suno prompt (1–3 sentence concept anchor plus 4–7 atomic tags) into the Style-of-Music field text plus a Lyrics-field metatag DAG (or `[Instrumental]`) plus the Instrumental toggle setting, run a manifold check that enforces the guide's objective construction rules, and report the three pasteable artifacts with a residual-gaps list keyed to the five dimensions.

## Rationale (background — read once, then carry into every conversion)

*A Suno prompt is not a wish list of adjectives. It is a coordinate-specification mechanism that locates a region inside Suno's audio-semantic manifold. When prompts fail, the cause is geometric: the region the prompt specifies does not overlap with the region containing the track the user wants. Surface rephrasing does not fix that. The conversion the skill performs is therefore not a stylistic rewrite of the user's description; it is a forcing function that asks, for each named axis of audio space, whether the user has constrained it, left it deliberately open, or simply not thought about it yet. Empty axes become residual gaps, not invented values.*

*The two-layer template — concept anchor plus atomic tags — is load-bearing, not stylistic. Atomic tags constrain dimensions independently and leave the coupling between them to the model's prior, which is the geometry of least resistance and is exactly what produces generic output. Concept anchors constrain the coupling itself through learned co-occurrence, so the small region the prompt lands in is internally coherent rather than a generic point on a high-dimensional intersection. Tags give control over named axes; anchors give cohesion between axes for free. A calibrated prompt uses both, and the 4–7 budget applies only to the atomic tag layer.*

*A note on this document: a SKILL.md is permanent background carried into every conversion, not a single navigational instruction. The dimension list, the construction rules, the diagnostic table, and the worked examples below are standing CONSIDERING for you. The Procedure section is the actual operational core; everything around it exists to support that core or to teach you the framework once.*

## Reference: the five audio-manifold dimensions

Every Suno prompt positions output along (at minimum) these five dimensions. A calibrated prompt either constrains each one or deliberately leaves it open; an unexamined dimension is the failure mode the skill exists to catch.

| Dimension | Constrained by | Example lock |
|---|---|---|
| Genre / sub-genre | Genre tags, era markers | "1980s synthwave", "atmospheric post-rock" |
| Tempo / energy | BPM numbers, feel descriptors | "140 BPM, driving", "glacial pace" |
| Timbre / instrumentation | Specific instruments, texture tags | "warm analog synth pad", "brushed drums, upright bass" |
| Mood / affect | Emotion descriptors, contextual cues, through-line phrases | "melancholic", "3 AM observation lounge", "lonely but not unhappy" |
| Spatial / production | Mix and texture terms | "wide stereo, cavernous reverb", "tape-saturated, intimate" |

Mood/affect can be specified in three forms, ordered from most to least information-dense — contextual cues (single phrases like "rainy Tuesday afternoon" that bias many correlated attributes through co-occurrence), through-line phrases (short clauses like "lonely but not unhappy" or "doesn't build toward anything" that fix listener stance and harmonic logic), and atomic adjective tags (the conventional "melancholic, hopeful, driving" vocabulary). Cues and through-lines belong in the anchor layer; adjectives belong in the tag layer. Coupling-unconstrained outputs — the right palette with no point of view — are almost always missing one of the first two forms.

## Reference: the two-layer template

Layer 1, the **concept anchor**, is 1–3 sentences of prose specifying scene, time, listener posture, and emotional logic. It biases many correlated dimensions at once. It does not count against the 4–7 budget because a scene phrase is not in dimensional competition with a tag.

Layer 2, the **atomic tags**, is 4–7 comma-separated descriptors. Each tag should lock a distinct named dimension. The budget is the semantic density sweet spot: under four leaves the region too large (underconstrained), over seven introduces dimensional competition that fragments the feasible region (overconstrained).

A typical calibrated Style-of-Music field is anchor-then-tags or anchor-tags-trajectory (a closing prose clause describing how the piece moves from start to finish). The two layers can be interleaved, but their roles must stay separate: prose for coupling, tags for independent locks. Mixing the roles — prose written as a tag list, or tags written as a substitute for narrative — is a documented failure mode.

## Reference: Custom Mode field split

The conversion produces three artifacts, one per Custom Mode field:

- **Style of Music** (~1,000 chars) — sound. The specified region in audio space. Anchor + tags + optional trajectory.
- **Lyrics** (~3,000 chars) — words and temporal structure. The traversal path through that region. For instrumental tracks, the Lyrics field carries only structural metatags as a DAG (`[Soft Intro] → [Slow Build] → [Climax] → [Fade Out]`), or just `[Instrumental]` if no specific arc is needed.
- **Instrumental toggle** — boolean. Disables vocals at the model's primary input channel.

Style and Lyrics fields are not interchangeable. Style is *where*; Lyrics is *the path through*.

## Reference: construction rules (the objective floor)

These are the rules the manifold check enforces. They are not stylistic preferences; each one corresponds to a geometric failure mode named in the source guide.

1. **Two layers, role-separated.** Prose for the anchor, comma-separated tags for the atomic layer. Mixing the roles couples things that should be independent or fails to couple things that should be coupled.
2. **4–7 atomic tags.** Anchors and trajectory clauses do not count. Under four is underconstrained; over seven risks overconstraint and contradiction.
3. **Front-load important terms.** First 20–30 words of the Style field carry the most weight. Sub-genre and era go near the top.
4. **No contradictions.** "High energy" + "chill", "minimal" + "lush", "frantic" + "meditative" specify empty intersections. The model hedges and produces neither.
5. **No artist names.** Describe the style dimensionally instead.
6. **Sub-genre and era over bare genre.** "Electronic" selects a hyperplane of incompatible styles. "1980s synthwave" performs manifold selection.
7. **Instrumentation specificity.** "Warm analog synth pad" locks three dimensions; "synth" locks none. Limit to 2–4 key instruments per prompt.
8. **All five dimensions either constrained or deliberately open.** A dimension you didn't think about is the dimension Suno fills from its prior — the geometry of least resistance.
9. **Anchor must specify coupling.** A 1–3 sentence anchor that only restates tags as prose ("driving synthwave with neon-lit feel") is not an anchor; it is a tag list with extra words. A real anchor places the listener in a scene that biases correlated dimensions through co-occurrence.
10. **Instrumental tracks reinforce from all three channels.** Toggle on, "instrumental" in the Style tag list, and `[Instrumental]` (or an instrumental-shaped metatag DAG) in the Lyrics field. One channel alone is underconstrained; three together create redundant constraint.

## Reference: diagnostic table

When the user reports a prompt is failing — or when the input describes a track they tried to generate and didn't get — map the symptom to a geometric cause before refining:

| Symptom | Geometric cause | Failure mode | Fix |
|---|---|---|---|
| Generic, bland, "could be anything" | Region too large | Underconstrained | Add sub-genre, era, specific instruments |
| Right palette, no point of view | Coupling between dimensions unconstrained | Coupling-unconstrained | Add a 1–3 sentence concept anchor and a through-line phrase |
| Wrong genre feel | Region in the wrong place | Displaced | Remove the descriptor pulling off-target; reframe with different anchoring terms |
| Muddy, confused, hedged | Contradictory or competing descriptors | Overconstrained | Cut to 4–5 tags targeting distinct dimensions |
| No dynamic arc | No temporal structure | Structurally misaligned | Add metatags in Lyrics field |
| Vocals appear in instrumental track | Vocal dimension constrained on only one channel | Underconstrained on one axis | Toggle + style tag + `[Instrumental]` metatag |
| Right style, off execution | Region right, sample unlucky | Sampling variance | Re-roll; the prompt is fine |

## Procedure

The procedure is a five-step DAG: **Place on the manifold → Diagnose the five dimensions → Compose the three artifacts → Manifold-check → Report**. Each step gates the next; do not skip ahead.

### Step 1 — Place the input on the audio manifold

Before diagnosing any dimension, read the user's intent and locate it as coarsely as possible on the manifold. Is there a named genre or sub-genre, even imprecise? An era? A scene or mood that anchors a region even without a genre? A target use (soundtrack cue, study music, club set) that implies a region? The output of this step is a one-sentence placement that you carry into Step 2 — something like *"low-energy late-night instrumental, leaning yacht-rock-adjacent, scene-anchored"* or *"high-energy electronic dance with no era specified."*

If the input is too vague to place even coarsely — a single word like "something cool" or "make me a song" — stop and ask one clarifying question rather than guessing. The skill cannot construct a useful coordinate from no coordinate.

If the input describes a failed generation rather than a fresh idea, also classify the failure against the diagnostic table above. This determines which dimensions to prioritize tightening in Step 3.

### Step 2 — Diagnose against the five dimensions

Walk the source against each of the five dimensions and record one of: *explicit* (the user named it), *implicit* (conservatively inferable from what they did name — e.g., "1980s synthwave" implies analog timbres without the user saying so), or *missing* (the user did not constrain this axis and conservative inference would be invention).

Use this as a checklist:

- **Genre / sub-genre.** Did the user name a sub-genre and era, or just a top-level genre, or neither?
- **Tempo / energy.** Is there a BPM, a feel descriptor, or only an implied energy from context?
- **Timbre / instrumentation.** Did the user name 2–4 specific instruments with timbral qualifiers, or only a general family ("synths"), or nothing?
- **Mood / affect.** Did the user supply a contextual cue, a through-line phrase, an adjective, or some combination — or none of these?
- **Spatial / production.** Did the user name mix qualities (reverb, stereo width, saturation, intimacy), or is this axis untouched?

Also note three structural commitments the user may or may not have made: vocals or instrumental, target length / structural arc, and any explicit exclusions ("no drums," "no autotune"). These bear on the Lyrics field and the Instrumental toggle, not the Style field.

The empty slot is the diagnosis. Do not silently invent values to fill it. Conservative inference is allowed only when one explicit choice clearly implies another (sub-genre implying era, ambient implying slow tempo); flag any inference as such so the user can correct it. Anything not in the source and not a flagged inference goes to the residual-gaps list, not into the prompt.

### Step 3 — Compose the three artifacts

Compose in order, using only material from the source plus flagged conservative inference.

First, **Style of Music**. Write a 1–3 sentence concept anchor that places the listener in a scene biasing the correlated dimensions the user cares about most. The anchor should specify scene, time, listener posture, and emotional logic — not just restate tags as prose. Then write 4–7 atomic tags, comma-separated, each locking a distinct dimension. Front-load sub-genre and era. If the user specified a dynamic arc, append a closing trajectory clause after the tags. Keep the whole field under ~1,000 characters.

Second, **Lyrics field**. If the track has vocals, draft section structure as a metatag DAG (`[Intro] → [Verse] → [Chorus] → ...`) and place placeholder lyrics under each section, or — if the user did not supply lyrics — output the metatag DAG alone and flag lyric content as a residual gap. If the track is instrumental, output a metatag DAG specifying the dynamic arc the user described (e.g., crescendo, tension-release, atmospheric stasis), or `[Instrumental]` alone if no arc was specified.

Third, **Instrumental toggle**. Set true for instrumental tracks (and confirm "instrumental" appears in the Style tag list and `[Instrumental]` in the Lyrics field — three reinforcing channels). Set false for vocal tracks.

### Step 4 — Manifold check

Read the composed Style field and check, in order:

1. **Two layers, role-separated.** Is there a real concept anchor (scene, time, posture, logic) and a real tag list (4–7 atomic, comma-separated)? Or did the anchor collapse into a tag-restatement, or the tags spill into prose?
2. **Tag count.** 4–7. Count them. Anchors and trajectory clauses do not count.
3. **No contradictions.** Read each tag against the anchor and against every other tag. Energy contradictions ("driving" + "chill"), density contradictions ("minimal" + "lush"), and stance contradictions ("celebratory" + "mournful") are the common ones.
4. **No artist names.** Search the entire field for names; replace any with dimensional descriptors.
5. **Front-loading.** Are sub-genre and era in the first 20–30 words? If they ended up at the back, reorder.
6. **Five dimensions accounted for.** Each dimension is either constrained in the prompt, deliberately left open, or in the residual gaps. None of them is silently filled with invention.
7. **Anchor specifies coupling.** Does the anchor place the listener in a scene that biases multiple dimensions through co-occurrence, or is it just tags-as-prose? If the latter, rewrite or move its content into the tag layer and drop the anchor.
8. **Instrumental coherence.** If instrumental, are all three channels reinforcing? If vocal, is the toggle off and is "instrumental" absent from the tags?
9. **Faithfulness.** Anything in the prompt that isn't in the source or a flagged inference is silent invention; pull it out and move it to residual gaps.

If any check fails, return to Step 3 and fix it. The objective rules are the floor. The looser register-end question — would the user actually press Generate on this? — is a downstream subjective check, not a substitute for the floor.

### Step 5 — Report

Use this exact structure:

```
**Diagnosis**
[2–4 sentences naming which of the five dimensions the source explicitly constrained, which were inferable, and which were left open. If the input describes a failed prior generation, name the geometric failure mode.]

**Style of Music** (paste into the Style of Music field)
> [1–3 sentence concept anchor. 4–7 atomic tags, comma-separated. Optional closing trajectory clause.]

**Lyrics** (paste into the Lyrics field)
> [Metatag DAG with optional lyric content, or [Instrumental], or a metatag DAG describing the instrumental arc.]

**Instrumental toggle**: [true | false]

**Residual gaps**
- Genre/sub-genre: [question, if missing]
- Tempo/energy: [question, if missing]
- Timbre/instrumentation: [question, if missing]
- Mood/affect: [question, if missing]
- Spatial/production: [question, if missing]
- [Structural items — vocals, length, exclusions — only if relevant and unspecified]

[Omit a Residual gaps line if the dimension was constrained or deliberately open. Omit the whole section if there are no gaps.]
```

Keep the diagnosis tight. The three artifacts are the deliverable, not commentary about them. Do not lecture the user on the geometric framework; assume they want the prompt and the gaps it surfaces.

Do NOT call the Suno API or attempt to generate the track. ONLY report the artifacts.

## Worked examples

### Example 1 — underconstrained input; the residual-gaps list does the work

**Source**: *"I want to make a chill electronic track for studying."*

**Diagnosis**: Coarse placement is "low-energy electronic background music." Genre is named only at the top level (electronic) — sub-genre and era are missing. Energy is partially constrained ("chill") but tempo is unspecified. Instrumentation, mood beyond "chill," and production are all untouched. The input describes a use case (study music) which biases toward instrumental, mid-low energy, foreground-receding texture, but the user has not yet committed to any specific region. This is the underconstrained failure mode; the bulk of the work is in the residual gaps, not in the prompt.

**Style of Music**:
> A long task on a quiet afternoon, headphones on, attention on something else. Electronic study music, mid-low energy, foreground-receding, warm texture, instrumental, music that holds attention without asking for it.

**Lyrics**:
> [Instrumental]

**Instrumental toggle**: true

**Residual gaps**
- Genre/sub-genre: "Electronic" is a hyperplane. Lo-fi hip-hop, ambient, deep house, downtempo, chillwave, and dub techno all qualify and produce very different tracks — which sub-region?
- Tempo/energy: A BPM target or descriptor (slow ambient, mid-tempo groove, downtempo) would lock pace; "chill" alone leaves a wide band.
- Timbre/instrumentation: 2–4 key instruments would lock spectral character. Rhodes? Analog pads? Vinyl-textured drum samples? A specific bass register?
- Spatial/production: Wide stereo and reverbed, or close and dry? Tape-saturated and lo-fi, or clean and modern?

### Example 2 — well-specified input with a contradiction the manifold check catches

**Source**: *"I want a high-energy ambient track — driving and explosive but also meditative and atmospheric, with massive reverb and frantic drums."*

**Diagnosis**: Coarse placement is "ambient with energy contradictions." The user named genre (ambient) and production (massive reverb), and supplied energy and mood descriptors — but those descriptors specify an empty intersection. "Ambient + frantic drums," "meditative + driving," and "explosive + atmospheric" are the overconstrained failure mode: the model cannot satisfy both poles, so it hedges and produces neither. The fix is not to balance the contradictions but to pick which side the user actually wants and rebuild around it. Flagging this back to the user is the conversion's main job; the prompt below picks the lower-energy side as the more coherent reading of "ambient" and surfaces the alternative as a residual gap.

**Style of Music**:
> A vast empty space at the moment between dusk and night — no event, only presence. Dark ambient, atmospheric pads, no drums, slow evolving texture, massive reverb, instrumental, sound that fills space rather than moves through it.

**Lyrics**:
> [Atmospheric Intro]
> [Gentle Movement]
> [Fade Out]

**Instrumental toggle**: true

**Residual gaps**
- Mood/affect (contradiction to resolve): The source asks for both "meditative + atmospheric" and "high-energy + driving + frantic." These specify an empty region. The composed prompt resolves toward the atmospheric reading; if you actually want the high-energy reading, the right region is something like *industrial techno or post-rock crescendo*, not ambient — and the prompt needs to be rebuilt around that target.
- Tempo/energy: Even within the atmospheric reading, no tempo is specified — slow ambient (50–70 BPM feel) or glacial (drone-rate)?

### Example 3 — well-specified input that converts cleanly

**Source**: *"A late-night jazz piece — like a near-empty hotel bar after midnight, two strangers each staying for their own reasons. Saxophone lead, brushed drums, double bass, piano. Slow. Intimate but not confessional. No vocals. Should feel like a single quiet conversation that doesn't resolve."*

**Diagnosis**: All five dimensions are explicitly or near-explicitly constrained. Genre/sub-genre is named (smooth/late-night jazz, jazz ballad register implied by the scene). Tempo is "slow." Instrumentation is fully specified at four instruments, the calibrated maximum. Mood is supplied in all three forms — contextual cue (the hotel bar scene), through-line phrases ("intimate but not confessional," "doesn't resolve"), and adjective register (slow, intimate). Spatial/production is implied by "intimate" and the bar scene. The user has also committed to instrumental and to a non-resolving arc. This is a near-textbook input; the conversion is a faithful rendering, not a repair.

**Style of Music**:
> Concept: A near-empty hotel bar after midnight. Two strangers, both staying late for their own reasons. Smooth jazz, saxophone lead, brushed drums, double bass, gentle piano, slow tempo, intimate and unhurried, instrumental, conversational phrasing that lets the room stay quiet, music that doesn't resolve.

**Lyrics**:
> [Instrumental Intro]
> [Solo]
> [Interlude]
> [Solo]
> [Fade Out]

**Instrumental toggle**: true

*(No residual gaps — every dimension is constrained either by an explicit tag or by the anchor's coupling. The piano is gentle by inference from "intimate but not confessional"; flag if the user actually wanted a more virtuosic piano voice.)*

## Notes on extending the skill (meta — not used during a conversion)

The five dimensions are the core set the source guide names, not exhaustive. Suno's manifold has further axes — vocal character, regional/cultural inflection, era of recording technique distinct from era of composition — that occasionally matter. Add a new dimension only if it corresponds to a distinct geometric axis the existing five can't constrain through composition, and only after the failure mode shows up repeatedly. Resist adding axes for surface features ("more guitars," "louder") that the existing dimensions already cover.

The skill is also paraphrase-invariant: a careful rewording of a user's input — same musical intent, different phrasing — should produce the same diagnosis, the same composed prompt up to wording, and the same residual gaps. If your output keys off the user's exact words rather than their intent, rework it.
