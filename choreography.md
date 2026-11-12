# Choreography — "I Spent 50 Hours In Solitary Confinement" (crew-5 episode)

**Synthesised 2026-11-11 ~23:00 EST from the four extraction parts (choreography-part-A/B/C/D.md) + the beat research (mrbeast-crew5-challenge-choreography.md).** This is the file the Drawing Tool's per-shot choreography data (`CINE_CHOREO`) is built from. One section per shot: videoTime, duration, cast, and a timeline of what each figure does and when.

## Method

- **Footage:** `solitary-confinement-chrome.mp4` (951 s, 640×360, re-encoded Chrome-friendly copy of `r7zJ8srwwjk`).
- **Extraction:** four subagents, each watching the footage windows `[videoTime, videoTime+duration]` at 3 s sampling (one frame at videoTime, then every 3 s), viewing the frames directly and describing what each visible person does. Frames in `/tmp/choreo-part-A/` (37), `/tmp/choreo-part-B/` (32), `/tmp/choreo-part-C/` (41), `/tmp/choreo-part-D/` (47) — ephemeral, not preserved.
- **Identification:** Jimmy = orange/red jumpsuit (reliable). The other boys wear plain black BEAST shirts / street clothes in this footage, NOT the cartoon colour code (Chris teal, Chandler red, Karl purple, Tyler yellow), so several figures could not be named from the frames alone and are left unassigned rather than guessed. Where a movement is assigned to a named figure on role/context rather than a confirmed frame, it is flagged **UNVERIFIED** or **staging pick**.
- **3 s sampling caveat:** anything that happens entirely between two 3-second samples is not captured. Where a shot's described action does not appear in any sampled frame, that is stated honestly rather than invented.

## The synthesis rule (what this file does, and why)

The videoTimes were curated for **empty visuals** (the v4 clean-background pass), so they are systematically **out of phase with the action** — the same misalignment as the audio (FIT 5 / PARTIAL 1 / MISMATCH 19). Most windows show Jimmy alone, often lying on the bed. The signature beat movements (mirror-lick, speaker smash, door blocking, roof climb, TV breaking, dinner, sliming) mostly do **not** appear in the windows at 3 s sampling.

So each shot uses **two sources, labelled per movement**:

- **WINDOW** — what the real person actually does in the sampled frames of this shot's `[videoTime, videoTime+duration]` window. Used wherever it matches the beat and the cast. This is the most faithful source: the figure does what the real person does at that moment.
- **BEAT** — the scene's actual choreography from the beat research (`mrbeast-crew5-challenge-choreography.md`, grounded in the full transcript and the video). Used **only** for a signature beat movement the window misses. This is still the real people's real choreography — the scene's documented movements — just at a different footage time than the background frame. It is never an invented pose.

The rule, per shot: **window where it matches the beat/cast; beat research where the window misses the signature movement.** Every movement below carries its source. Where the two sources disagree (the window shows one thing, the beat says another), the file says which won and why.

**Shots where the BEAT source carries the shot:** 05 (the smash), 06 (the speaker-wheeling), 08 (the roof climb), 11 (the dinner), 15-Tyler (the bag arrival), 18 (the TV break), 19 (the feast), 21 (the home stretch), 22 (the door + leaf blower), 25 (the sliming). **Shots where the WINDOW carries the shot:** 01, 02, 03, 04, 07 (thin — listening only), 09, 10, 12, 13, 14, 16, 17, 20, 23, 24. Shot 07 is the thinnest: neither the window nor the beat research shows the cast doing anything specific, so the boys stand and listen (stated honestly).

## Motion vocabulary (what the tool can render)

The figure rig is a standing cartoon (legs, torso, arms, head). These are the channels `CINE_CHOREO` drives; every move below is written in them:

- **armR / armL** (0..1): one arm's raise. 0 = hanging at the side; 0.3 = slightly raised, hand at chest; 0.5 = raised ~60°, hand at shoulder/head height (holding something in front, hand-to-face); 0.7 = high, hand above the head; 1.0 = straight up overhead. The right arm was the old generic gesture; the left arm is new (mirrored rotation).
- **lean** (degrees, ±8°): whole-body rotation around the feet. + = forward / leaning in; − = back / looking up. A look up is a lean back; a look down is a lean forward.
- **crouch** (0..1): vertical squash around the feet. 1.0 = deep crouch (~35% shorter); 0.65 = sitting / low; 0.3 = a flinch. Sitting on the bed is rendered as crouch — the rig has no chair/bed seat, so a sit reads as a lowered figure, not a seated one (noted per shot).
- **lying** (0..1): whole-body rotation around the feet. 1.0 = fully lying down, body horizontal, head to the left, feet to the right. A lying figure's bob reads as breathing. A lying arm raise sticks up from the body, matching Jimmy raising his arm while lying on the bed.
- **bob** (kept, all figures): idle bob, 5 px, ~1.6–2.0 s period, per-figure phase. **mouth** (kept, speaker only): talking mouth ~5.25 cycles/sec. Both unchanged from the movement round.

A move is `{at, dur, ch, val}` — seconds within the shot, the channel, the target value. Values ramp in/out smoothly (~0.4 s); adjacent moves on the same channel overlap by 0.4 s so a value change is smooth, not a dip to zero. Unsigned channels (armR, armL, crouch, lying) combine by max; lean (signed) combines by sum, so a + to − transition passes through zero. **Workaround channels (built 2026-11-11 ~19:30, after Nick refused the limits):** **walk** (0..1) switches the bob from the idle rhythm (5 px, ~1.6–2.0 s) to a walking rhythm (~0.55 s period, ~7 px amplitude) with leg alternation where the legs are separate elements; **walkX** (signed px, cumulative) translates the figure horizontally across the floor, ground shadow travelling with it — a lateral walk sets walk=1 + walkX to the displacement, an in-place walk (toward/away from camera) sets walk=1 with walkX=0. **tongue** (0..1) switches the figure's mouth to the tongue-out expression (Chris's tongue mouth) regardless of the bible/override expression, beating even the talking mouth while on — it is an expression, not a motion. **laugh** (0..1) gives any figure (not just the speaker) a fast-cycling laughing mouth (~8–10 cycles/sec) plus a fast bouncy shake in the bob. **Foreground layers:** a ceiling occluder (a ceiling shape with a hole cut out, drawn over the figure's body so only the head shows through, before the HOUR badge and scene label) for a head through a ceiling hole; a **sheet** prop (white sheet over a lying figure's torso, head and feet out); an animated **door** prop (a door panel swinging open around its hinge). **Hand-held props** (phone, speaker, bag, ball) attach to a figure's hand(s) and move with the figure. See the Workarounds section below for which shots use each.

## Workarounds — Nick refused the limits (2026-11-11 ~19:06)

Nick: "I refuse to believe that there's no work around here." He was right — the limits in the Motion vocabulary above were called too quickly. All seven workarounds are built into the Drawing Tool and wired into the per-shot data (two artifact edits, both verified: Edit 1 capabilities — 63/63 assertions, all 12 inline scripts node --check, still composition byte-identical; Edit 2 data — 61 motion/data unit tests + 41 plate tests, all passing). Motion stays deterministic in (shot, figure, t); every new channel is 0 at t=0, so the still PNG export is unchanged. Backgrounds, videoTimes, and all other episode data were NOT changed. One scope note from the Edit 2 builder: the brief said "data only", but the previous build's CINE_SCENE_FX/CINE_HOLDS renderers were shot-static (no time windows, no way to show a head whose owner is not in the cast, no set-down prop), so five small backward-compatible renderer additions were needed for the timed windows (optional at/dur on FX/holds, a 0.4 s occluder ramp/opacity, a hole-clipped headCharId plate, a dropAt floor prop); entries without at/dur render exactly as before.

- **WALKING (walk + walkX):** Shot 01 Jimmy t=3–6 (foot stepping, in place, walkX=0); shot 02 Jimmy t=3–6 (walks toward camera, in place); shot 03 Jimmy t=15–18 (walking toward the door, back to camera, in place); shot 14 Jimmy t=9–12 (walking toward camera carrying the TV, in place); shot 23 Jimmy t=12–14 (walks out through the doorway, walk=1 + walkX=+40 — verified against the frame, the white door fills the right of the frame, so the sign is correct). The per-shot lines that said "walking is not representable" are superseded by these moves.
- **HEAD THROUGH THE CEILING HOLE (ceiling occluder):** Shot 09 Karl t=6–9 — head upside-down (180°) through the hole; the old crouch 1.0 + lean +8° approximation for that window is DELETED (replaced by the occluder); Karl's t=3–6 crouch 0.5 (lowering toward the hole) and t=9–12 recovery are kept. Shot 16 t=3 — a head peering down through the hatch (upright, tilted forward), staged as Karl, flagged **UNVERIFIED** (Karl is the roof person in this beat but is not in shot 16's cast and the identification is not confirmed from the frame; Karl appears only as the head at t=3 and has no other moves in shot 16). Shot 16's occluder window is t=3–6 (matching Jimmy's phone beat in the same window).
- **LYING UNDER A SHEET (sheet prop):** Shot 18 Jimmy t=0–9 — sheet over the lying figure's torso, head (left) and feet (right) out. Jimmy's lying 1.0 is kept.
- **TONGUE-OUT EXTREME CLOSE-UP (tongue channel + camera):** Shot 15 Jimmy t=12–15 — tongue=1; shot 15's camera changed from wide to close-up. Jimmy's expressionOverride stays "angry"; the tongue overrides the mouth during t=12–15 only.
- **NON-SPEAKERS LAUGHING (laugh channel):** Shot 09 Karl t=12–14 — laugh=1 (crouching/laughing, holding a white object); the existing crouch 0.6, armR 0.5, armL 0.5 are kept. The white object Karl holds is not rendered as a prop (it is unidentified — not assigned a phone/speaker/bag/ball).
- **HOLDING A PHONE / SPEAKER / BAG (hand-held props):** Shot 02 Jimmy phone t=9–12 (both hands at chest); shot 12 Jimmy phone t=3–6 (left hand); shot 16 Jimmy phone t=3–6 (right hand to mouth); shot 13 Jimmy speaker t=12–15 (both hands at chest); shot 01 Jimmy speaker t=12–18 (one in each hand, hand:'both', both arms overhead); shot 15 Tyler bag t=3–9 (both hands low, carrying the bag of alarm clocks — the bag detaches and rests on the floor from t=9, when Tyler sets it down); shot 16 Tyler bag t=6–9 (both hands low, white bag); shot 10 Chandler ball t=9–12 (between the hands). The per-shot lines that said a figure "holds the pose empty-handed" are superseded where a prop is now assigned. Four existing choreo values were aligned to the brief's stated arm values in the Edit 2 build: shot 12 Jimmy armL 0.7→0.6; shot 16 Jimmy armR 0.5→0.7; shots 15/16 Tyler arms 0.3→0.5 (shot 15's duration set to exactly t=3–9 so the set-down lands at t=9).
- **UNIDENTIFIED MEN — STAGED AROUND (shot 23):** Chandler's pink-hoodie-man moves are DELETED (t=3–6 walks in/looks up, t=6–9 leans in at hatch/hand on door, t=9–12 reaches down to handle/opens door, t=12–15 stands facing Jimmy) and Chris's t=12–15 move (third man in black, standing/watching/listening) is DELETED. Both return to baseline (standing, idle bob) and watch Jimmy come out. The door-opening beat is now the **door prop**, which swings open by itself over t=9–12 — no person opens it. No shot's beat depends on an unidentified man. The blue/teal-hoodie ladder carrier (shots 21/22/25) remains unmapped, as before; the ladder sequence is used in no shot's choreography.
- **SITTING:** still reads as crouch, as before. Not a blocker.
- **Still genuinely not done:** the TV Jimmy carries in shot 14 (t=9–12) is not rendered as a hand-held prop — he holds the carrying pose (armR 0.5, armL 0.5) but the TV object itself is not drawn (the workaround prop list was phone/speaker/bag/ball; a carried TV was not in scope). The white object Karl holds in shot 09 t=12–14 is likewise not rendered (unidentified). The dangling leg beside Karl's head in shot 09 t=6–9 is not rendered (only the head shows through the hole).

## Cross-shot findings (read first)

- **Shots 21, 22 and 25 share the same footage.** vt 494–510 is one ladder/camera sequence in all three (byte-identical frames). Their listed actions (door reach, sliming payoff) are not what's there. The ladder sequence is used **once** — nowhere, in fact: shots 21, 22 and 25 all use BEAT choreography instead, so the ladder is not triplicated and each shot tells its own beat. The ladder still exists as a shot prop (shot 12/13) and in the backgrounds.
- **The videoTimes are misaligned with the beats for most shots.** The background plate is a still from the window; the figure's choreography is the beat's real movement. The two do not always coincide in time — the same misalignment the audio already has. This is stated per shot, not hidden.
- **Two unidentified men recur and are NOT mapped to cast:** a man in a **pink hoodie + black cap** (shots 23–24, at the door and at a desk) and a man in a **blue/teal hoodie + black cap** carrying the red ladder (shots 21/22/25). Both are described in the extraction but left unassigned. **Shot 23 no longer assigns the pink-hoodie man's door-opening to Chandler** — that staging pick was REMOVED in the workarounds round (see Workarounds): the door now opens by itself as a prop (t=9–12), and Chandler and Chris stand at baseline. No shot's beat depends on an unidentified man.
- **Non-cast people in the windows are not encoded.** The tool renders only a shot's listed cast. People who appear in a window but are not in that shot's cast (Karl in shot 10's window, the two boys at the hatch in shot 03, the three men outside in shot 11, the control-room men, the camera operator) are noted in the shot section but get no choreography.

---

## Shot 01 — "The setup"

- **videoTime:** 178 · **duration:** 18 s · **cast:** Jimmy, Chris, Chandler, Karl, Tyler
- **Window (Part A):** Jimmy only. Empty room (t=0–3); Jimmy's foot stepping (t=3–6); Jimmy standing on the chair, right arm raised to the tripod, left hand gripping a tripod leg (t=6–12); Jimmy both arms raised overhead holding a speaker in each hand, looking up, grinning (t=12–18). **Chris licking the mirror does NOT appear in any sampled frame.**
- **Beat (Beat 1):** Jimmy centre-room gesturing at the walls/bed/mirror, trying the door; the four boys pressed against the mirror, Chris licking it.

### Choreography

- **Jimmy (WINDOW):**
  - t=6–12: right arm up to the tripod (armR 0.8), left hand gripping the tripod leg (armL 0.3), looking up (lean −4°).
  - t=12–18: both arms raised overhead holding a speaker in each hand (armR 1.0, armL 1.0), looking up, grinning (lean −4°).
  - t=0–6: baseline (empty room, then a walking step — walking is not representable; stand + bob).
- **Chris, Chandler, Karl, Tyler (BEAT — pressed against the mirror):** lean in (lean +6°) for the whole shot (t=0–18). Chris's tongue is his bible expression, not a motion. The window does not show them; the beat research documents them pressed against the glass.

---

## Shot 02 — "The setup"

- **videoTime:** 5 · **duration:** 12 s · **cast:** Jimmy, Chris, Chandler, Karl, Tyler
- **Window (Part A):** Jimmy only. Empty door (t=0); Jimmy walks toward camera, left arm slightly out (t=3); Jimmy stands still, arms at sides, talking (t=6); close-up talking (t=9); Jimmy stands holding a phone at chest, seen through the mirror, with one unidentifiable pointing finger at him (t=12).
- **Beat (Beat 1):** Jimmy gesturing at the walls/bed/mirror, trying the door; the four boys pressed against the mirror.

### Choreography

- **Jimmy (WINDOW):**
  - t=3–6: left arm slightly out (armL 0.25) while walking toward camera (walking not representable; stand + bob).
  - t=6–9: stand, arms at sides, talking (baseline + mouth).
  - t=9–12: both hands raised to chest holding a phone (armR 0.5, armL 0.5), looking down at it (lean +4°).
- **Chris, Chandler, Karl, Tyler (BEAT):** lean in at the mirror (lean +6°, t=0–12). The unidentifiable pointing finger at t=12 is **not** assigned to any named figure.

---

## Shot 03 — "The TV clips"

- **videoTime:** 123 · **duration:** 20 s · **cast:** Jimmy
- **Window (Part A):** Jimmy + 2 unnamed boys at the hatch + a camera operator (not cast). Empty wall/hatch (t=0); two boys lean in at the hatch, faces at the glass, grinning (t=3–6); Jimmy stands carrying the chair with both hands (t=9); almost empty (t=12); Jimmy lifts the chair overhead with both arms, walking to the door, back to camera (t=15); two boys lean in at the hatch again, peering (t=18); one reaches up to slide the hatch shut, the other steps back laughing (t=20). The TV content (croissant loop, Minecraft cats) does not appear in any sampled frame.
- **Beat (Beat 2):** Jimmy on the bed, then on the floor, hands over his ears; the boys laughing behind the mirror, taking turns at the controls.

### Choreography

- **Jimmy (WINDOW):**
  - t=9–12: standing, holding the chair in front of him with both hands (armR 0.5, armL 0.5).
  - t=15–18: lifting/carrying the chair overhead with both arms, walking toward the door (armR 1.0, armL 1.0; walking not representable; stand + bob).
  - t=0–9, 12–15, 18–20: baseline.
- **The two boys at the hatch are NOT in this shot's cast** and get no choreography. The camera operator (blue shirt, cap) is not cast.

---

## Shot 04 — "The alarm and the smash"

- **videoTime:** 215 · **duration:** 15 s · **cast:** Jimmy
- **Window (Part A):** Jimmy lying on the bed throughout. Alarm screen, no people (t=0); lying, talking, right hand low (t=3); lying, left hand raised gesturing/pointing (t=6–9); lying, left arm thrown up high, eyes closed (t=12); lying, left hand to cap brim (t=15).
- **Beat (Beat 3):** Jimmy at the desk, grabs the tripod, swings it like a bat, smashes the speakers; lies on the bed.

### Choreography

- **Jimmy (WINDOW — the alarm reaction; this shot is the alarm, the smash is shot 05):** lying on the bed for the whole shot (lying 1.0, t=0–15).
  - t=6–12: left hand raised, gesturing/pointing while talking (armL 0.6).
  - t=12–13.5: left arm thrown up high, hand open, eyes closed — the "snap" (armL 1.0).
  - t=13.5–15: left hand brought down to touch/adjust his cap brim (armL 0.4).
  - The described "Jimmy snaps" has no distinct physical snap in the frames; the arm thrown up with eyes closed at t=12 is the closest thing to it.

---

## Shot 05 — "The alarm and the smash" ★ BEAT source

- **videoTime:** 339 · **duration:** 15 s · **cast:** Jimmy
- **Window (Part A):** Jimmy lying on the bed throughout. Empty corner (t=0); lying, pointing at camera (t=3); lying, pinched-finger gesture (t=6); lying, hand to face (t=9); lying on his side, knee up (t=12); lying flat under the blanket, eyes closed (t=15). **The speaker smash does NOT appear in any sampled frame — no tripod, no speakers, no standing, no smashing.**
- **Beat (Beat 3):** Jimmy grabs the camera tripod, swings it like a bat, smashes the two speakers together. "Batter up. Shut up, I'm tired! … Yes, die! Yes, it's gone!" Then he lies on the bed to sleep.
- **Why BEAT wins:** this shot IS the smash ("Jimmy smashes the speakers with the tripod"). The window shows lying on the bed — the smash is not in it. The beat research gives the actual smash choreography.

### Choreography

- **Jimmy (BEAT — the smash; WINDOW for the lying at the end):**
  - t=0–3: standing at the desk, looking at the speakers (lean +3°).
  - t=3–6: grabs the tripod — both arms reach to hold it in front of him (armR 0.5, armL 0.5).
  - t=6–7.5: wind-up — both arms high with the tripod (armR 1.0, armL 1.0).
  - t=7.5–8.5: the swing through and impact — both arms sweep down (armR 0.15, armL 0.15). The crash cue fires at t=0 in the manifest; the visual smash is staged mid-shot here.
  - t=8.5–12: silence, relief — arms down, standing (baseline).
  - t=12–15: lies on the bed to sleep (lying 1.0) — this matches the window's final frames (lying flat under the blanket, eyes closed).
- **Honest note:** the window shows Jimmy lying on the bed for the whole 15 s; the smash choreography comes from the beat research (transcript + video), not from this window. The background frame (vt=339) is an empty room corner — the figure smashing in front of it is the beat's real choreography at a different footage time.

---

## Shot 06 — "The hiding plan" ★ BEAT source (boys) + WINDOW (seated boy)

- **videoTime:** 343 · **duration:** 16 s · **cast:** Chandler, Karl, Tyler
- **Window (Part A):** overlaps shot 05 almost entirely (343–359 vs 339–354), so most of it is the same Jimmy-on-the-bed footage. The mirror already covered with cardboard/tape at t=0 (the *result*, no people, no action). One boy (dark hair, black sweatshirt, likely Chandler but not provable) sits on a couch outside, leaning slightly forward, talking (t=16). **No door-blocking or mirror/camera-covering action in any sampled frame.**
- **Beat (Beat 4):** Jimmy's hiding plan (mattress to the door, tape the mirror, spin the camera) — that is Jimmy, who is NOT in this shot's cast. The boys' counter: they wheel in a big speaker and point it at the wall; Hank's "Clouds?" plays on it.
- **Manifest conflict, stated plainly:** the shot action says "The boys block the door and cover the mirror and the cameras," but per the beat research that is Jimmy's plan, not the boys'. The boys' real action in this beat is the speaker. The choreography follows the beat research, not the manifest's wording.

### Choreography

- **Chandler, Karl, Tyler (BEAT — wheeling in the speaker):**
  - t=0–6: pushing the big speaker in — both arms forward (armR 0.5, armL 0.5), leaning into the push (lean +6°).
  - t=6–10: Karl (the engineer) points/adjusts the speaker (armR 0.7, lean +4°). The point is assigned to Karl by role (he is the hardware guy), not from a frame — a staging pick.
  - t=10–16: baseline (the speaker plays; the boys listen).
- **Chandler (WINDOW — the seated boy at t=16):** sitting on the couch, talking — crouch 0.7 at t=14–16 (a lowered figure; a true sit is not representable). He is the speaker in this scene (figIdx 0), so his mouth talks. The identification as Chandler is likely but not provable from the frame alone.

---

## Shot 07 — "The hiding plan" (thinnest shot — listening only)

- **videoTime:** 216 · **duration:** 14 s · **cast:** Chandler, Karl, Tyler
- **Window (Part B):** Jimmy alone lying on the bed (t=3–12): pointing at camera (t=3), hand-to-chin (t=6), touching cap (t=9), arm raised high (t=12). **No cast member appears. No Hank, no radio.**
- **Beat (Beat 4):** Hank's "Clouds?" on a loop, played on the big speaker outside the room. The boys are outside with the speaker.

### Choreography

- **Chandler, Karl, Tyler:** stand outside, listening to "Clouds?" — lean in slightly (lean +3°, t=0–14). Karl fiddles with the speaker (armR 0.4, t=4–8).
- **Honest note:** this shot's boy choreography is minimal because neither the window nor the beat research shows them doing anything specific here. Jimmy's lying poses in the window are NOT mapped onto the boys (Jimmy is not in this shot's cast). "Clouds?" is a sound cue, not a movement.

---

## Shot 08 — "The roof and the goat" ★ BEAT source

- **videoTime:** 45 · **duration:** 14 s · **cast:** Karl
- **Window (Part B):** door handle (t=0); Jimmy's fist knocking (t=3, hand only); Jimmy standing/walking in the warehouse, arms at sides (t=6); Jimmy standing in the inset cell + three unidentifiable men in street clothes standing still (one leaning/bowing his head, hand near his face; two upright, hands in pockets) (t=9); black "TRY TO" card (t=12). **No roof climb. No identifiable Karl.**
- **Beat (Beat 5):** Karl on the roof with the speakers; Jimmy stands on the bed reaching up, fingers just short of the hatch; the goat scream; Jimmy dives under a blanket.
- **Why BEAT wins:** this shot IS the roof climb ("Karl climbs onto the roof above the room"). The window shows no climb and no Karl.

### Choreography

- **Karl (BEAT — the climb):**
  - t=0–4: climbing up onto the roof — both arms reaching up (armR 0.9, armL 0.9), leaning forward into the climb (lean +5°).
  - t=4–8: on the roof, setting up the speakers — arms down adjusting (armR 0.4, armL 0.4), looking down at the hatch (lean +6°).
  - t=8–14: standing on the roof, speakers set up, looking down through the hatch (lean +4°).
- **The three men at t=9 are not mapped** — none can be confirmed as Karl (no purple, no face match).

---

## Shot 09 — "The roof and the goat"

- **videoTime:** 402 · **duration:** 14 s · **cast:** Karl
- **Window (Part B):** goat on the TV only (t=0); washed-out wall (t=3); Karl's head pokes upside-down through the ceiling hole, mouth open, leg dangling (t=6); Jimmy standing holding a phone to his face, looking up (t=9); Karl (black sweatshirt, identified by context, uncertain) crouching/laughing, knees bent, holding a white object in both hands (t=12).
- **Beat (Beat 5):** Karl on the roof; Jimmy reaches up through the ceiling; the goat scream; Jimmy dives under a blanket.

### Choreography

- **Karl (WINDOW):**
  - t=3–6: lowering toward the hole (crouch 0.5).
  - t=6–9: head poking upside-down through the ceiling hole — **approximated** as a deep crouch with the head down low (crouch 1.0, lean +8°). The rig has no head-only-through-a-hole pose and no ceiling/hole prop; the deep crouch + lean is the closest readable pose. The dangling leg is not rendered.
  - t=9–12: recovering (crouch easing off).
  - t=12–14: crouching/laughing, knees bent, both hands holding a white object out in front (crouch 0.6, armR 0.5, armL 0.5, lean +4°).
- **Jimmy is not in this shot's cast** (his phone-to-face look-up at t=9 is noted, not encoded). The goat is on the TV only — never a live animal in the room, so no goat is staged.

---

## Shot 10 — "The freestyle rap"

- **videoTime:** 603 · **duration:** 20 s · **cast:** Jimmy, Chandler
- **Window (Part B):** empty cell, rope hanging (t=0); Jimmy standing, back to camera, both arms raised overhead gripping the rope, looking up (t=3); Jimmy's arm reaching in with the stick/rope (t=6); Chandler + Karl (not cast) in BEAST shirts talking outside — Karl's hand on Chandler's chest (t=9), Karl two-handed gestures (t=12), Chandler open-palm gesture (t=15); Jimmy lying in bed, fingers pinched, talking (t=18).
- **Beat (Beat 6):** the three boys at the hatch lowering the speakers on a rope like a bucket into a well; the rap plays; Jimmy lies on the bed, eyes open, refusing to react.

### Choreography

- **Jimmy (WINDOW + BEAT):**
  - t=3–6: standing, both arms raised overhead gripping the rope, looking up (armR 1.0, armL 1.0, lean −4°) — the shot's signature pose.
  - t=6–9: arm reaching in holding the rope (armR 0.7).
  - t=9–20: lying on the bed, eyes open, refusing to react (lying 1.0) — BEAT (Jimmy is not visible in the window 9–18; the beat research has him lying on the bed through the rap).
  - t=18–20: lying, one hand raised with fingers pinched, talking (armR 0.4) — WINDOW.
- **Chandler (WINDOW):**
  - t=9–12: standing, hands open and lowered, holding a small ball (armR 0.3, armL 0.3).
  - t=12–15: turned toward Karl, listening (lean +3°).
  - t=15–18: facing camera, right hand raised in an open-palm talking gesture (armR 0.7).
- **Karl's talking gestures (t=9–15) belong to Karl, who is not in this shot's cast — not encoded.** The rap itself is a sound cue.

---

## Shot 11 — "Filet mignon dinner" ★ BEAT source (eating) + WINDOW (point)

- **videoTime:** 270 · **duration:** 20 s · **cast:** Jimmy
- **Window (Part B):** empty cell (t=0); Jimmy sitting/crouching at the bed edge, leaning forward, looking down at a phone (t=3); three men outside (Karl arms-out palms-up, then pointing; Chandler hand on hip, then palms-up; a third man with a shoe/phone) — none in cast (t=6–15); Jimmy close-up, index finger pointing straight up, talking (t=18).
- **Beat (Beat 7):** a plate slides under the door/hatch; Jimmy sits at the desk eating filet mignon with a plastic fork; the boys watch through the mirror, annoyed.
- **Why BEAT wins for the eating:** the shot's action is the dinner. The window shows Jimmy with a phone and pointing — no dinner, no tray, no filet mignon. The eating comes from the beat research.

### Choreography

- **Jimmy (BEAT — eating; WINDOW — the point):**
  - t=3–15: sitting at the desk, leaning forward over the plate, eating with a fork (crouch 0.65, lean +5°, armR 0.5 — the fork hand held near the face). A sit is rendered as crouch (a lowered figure); the desk and plate are in the background, not the rig.
  - t=15–18: standing up from the desk (crouch easing to 0).
  - t=18–20: index finger pointing straight up, talking (armR 0.8) — WINDOW.
- **The three men outside (t=6–15) are not in this shot's cast — not encoded.**

---

## Shot 12 — "The ladder, the hole, the theft"

- **videoTime:** 485 · **duration:** 15 s · **cast:** Jimmy, Karl
- **Window (Part B):** red ladder alone (t=0); Jimmy's POV, both hands reaching down through the ceiling hole, phone in the left hand (t=3); Jimmy's right arm reaching up, hand gripping a black tool against the wall — **the hole-making hand is Jimmy's (orange sleeve), not Karl's** (t=6); empty corner (t=9); Jimmy standing, facing camera, talking (t=12); Jimmy standing behind the ladder, right hand on the rail, slight lean (t=15).
- **Beat (Beat 8):** Jimmy on the ladder reaching up into the hole Karl cut; pulls the speakers out, tosses them behind the bed; pats the camera.

### Choreography

- **Jimmy (WINDOW):**
  - t=3–6: both hands reaching down through the ceiling hole, phone in the left hand (armR 0.7, armL 0.7, lean +5°). The footage is a POV shot; the cartoon renders it as Jimmy leaning over the hole, both arms reaching down.
  - t=6–9: right arm reaching up, hand gripping the tool against the wall (armR 0.8).
  - t=9–13: standing, facing camera, talking (baseline + mouth).
  - t=13–15: standing behind the ladder, right hand resting on the rail, slight lean (armR 0.3, lean +3°).
- **Karl: baseline (no moves).** He does not appear in any sampled frame, and the window shows Jimmy's hand making the hole, not Karl's. **Manifest conflict, stated plainly:** the shot action says "Karl puts a hole through it," but the sampled frames show Jimmy's orange-sleeved hand doing it. Karl gets no hole-making choreography from this window.

---

## Shot 13 — "The ladder, the hole, the theft"

- **videoTime:** 490 · **duration:** 15 s · **cast:** Jimmy, Karl
- **Window (Part C):** speakers/cable on the floor, a hand/cable at the hole (t=0–3, no figure); Jimmy close-up talking, head still (t=3–6); Jimmy standing on the ladder, left hand on the rail, turned toward the wall/hole (t=6–9); Jimmy higher on the ladder, left hand on the ladder top, right arm raised high reaching up toward the wall speaker/hole, head tilted up (t=9–12); Jimmy down from the ladder, both hands holding the stolen speaker(s) at chest, head bowed (t=12–15).
- **Beat (Beat 8):** Jimmy on the ladder reaching into the hole, pulling the speakers out, tossing them behind the bed, patting the camera.

### Choreography

- **Jimmy (WINDOW — matches the beat):**
  - t=6–12: standing on the ladder, left hand on the rail/top (armL 0.3), turned toward the wall (lean +4°).
  - t=9–12: right arm raised high, reaching up toward the hole/speaker (armR 1.0), head tilted up (lean −4°).
  - t=12–15: down from the ladder, both hands holding the speaker(s) at chest, head bowed looking down at them (armR 0.5, armL 0.5, lean +5°).
  - t=0–6: baseline (speakers on the floor, then talking to camera).
- **Karl: baseline (no moves).** Not visible in any sampled frame. The t=0–3 frames (speakers/cable, hole) are presumably Karl's side of the wall, but Karl himself is never seen.

---

## Shot 14 — "The Drake Bell Cameo"

- **videoTime:** 122 · **duration:** 22 s · **cast:** Jimmy
- **Window (Part C):** empty ceiling/wall (t=0–3); two men in black outside the door leaning in at the hatch, Jimmy's face through the hatch holding a phone (t=3–6); empty wall (t=6–9); Jimmy walking toward camera carrying the TV in both hands at chest (t=9–12); Jimmy wide-stance swing/throw at the door, right arm extended, the TV flying (t=12–15); Jimmy bent over, right arm reaching down to grab the tipped chair (t=15–18); two men outside reacting/laughing (t=18–21, not cast); a man in a black hoodie raises his right arm to point/tap the hatch (t=21, uncertain whether Jimmy).
- **Beat (Beat 9):** the TV shows Drake Bell; Jimmy sits up straight, energised; the boys groan and slap their foreheads; Karl shrinks behind Chris.

### Choreography

- **Jimmy (WINDOW — the throw is the backfire beat):**
  - t=3–6: face at the hatch, holding the phone up with both hands (armR 0.5, armL 0.5).
  - t=9–12: walking toward camera carrying the TV in both hands at chest (armR 0.5, armL 0.5; walking not representable; stand + bob).
  - t=12–13: wind-up for the throw — right arm high (armR 1.0).
  - t=13–14.5: the throw release — the arm sweeps down (armR 0.1).
  - t=15–18: bent over, right arm reaching down to grab the tipped chair (lean +8°, armR 0.4).
  - t=0–3, 6–9, 18–22: baseline.
- **Drake Bell is a face on the TV only, never in the room — no Drake figure.** The two men outside are not in this shot's cast. The beat research's "Jimmy sits up straight, energised" is the watching moment; the window's throw is the backfire payoff — the window wins for the visual.

---

## Shot 15 — "Tyler's alarm clocks"

- **videoTime:** 340 · **duration:** 15 s · **cast:** Jimmy, Tyler
- **Window (Part C):** empty cell (t=0–3); Jimmy lying on his back, pointing at camera (t=3–6); Jimmy lying, right hand to his face (t=6–9); Jimmy lying on his side, knee up, arm hanging down (t=9–12); Jimmy extreme close-up, tongue out (t=12–15, face only). **Tyler not visible in any sampled frame. No bag, no clocks.**
- **Beat (Beat 10):** a pile of alarm clocks slides in, all ringing, tops removed; Jimmy lies on the bed, hands behind his head, not asleep; a siren goes off.

### Choreography

- **Jimmy (WINDOW — lying throughout):** lying on the bed for the whole shot (lying 1.0, t=0–15).
  - t=3–6: right hand raised, index finger pointing at the camera (armR 0.7).
  - t=6–9: right hand brought up to cover his mouth/nose (armR 0.4).
  - t=9–12: lying on his side, knee up, arm hanging down (armR 0).
  - t=12–15: the tongue-out close-up is a facial beat on an extreme wide-angle shot — **not encodable as body choreography** (Jimmy's expression is "grin" in the bible; the motion system does not change expressions).
- **Tyler (BEAT — the bag arrival; he is not visible in this window):**
  - t=3–9: arrives carrying the bag of alarm clocks — both arms low holding the bag (armR 0.3, armL 0.3), leaning forward (lean +5°).
  - t=9–12: sets the bag down (arms to 0, lean to 0).
  - t=12–15: baseline (the clocks ring; Tyler watches).

---

## Shot 16 — "Tyler's alarm clocks"

- **videoTime:** 405 · **duration:** 15 s · **cast:** Jimmy, Tyler
- **Window (Part C):** blank wall (t=0); a head only, peering down through the ceiling hatch, with a dark object (bag/clock?) beside it (t=3); Jimmy standing, phone to his mouth (t=3–6); a man in black in the doorway, crouching, both hands low holding a white bag, grinning (t=6–9); Jimmy both arms raised overhead, reaching up, looking up (t=9–12); Jimmy standing, head bowed, arms at sides (t=12–15).
- **Beat (Beat 10):** the pile of ringing clocks; Jimmy lies on the bed, hands behind his head; the siren; the boys realise they are entertaining him.

### Choreography

- **Jimmy (WINDOW):**
  - t=3–6: standing, right hand raised holding a phone to his mouth (armR 0.5).
  - t=9–12: both arms raised high overhead, reaching up, looking up (armR 1.0, armL 1.0, lean −4°).
  - t=12–15: standing, head bowed, looking down, arms at sides (lean +6°).
- **Tyler (WINDOW — UNCERTAIN):** the man in black with the white bag at t=6–9 is the only candidate, and he does not wear Tyler's yellow. The bag matches the beat ("bag of alarm clocks"), so he is encoded as Tyler with the flag:
  - t=6–9: crouching/leaning forward in the doorway, both hands low holding the white bag (crouch 0.5, lean +6°, armR 0.3, armL 0.3).
  - t=0–6, 9–15: baseline.
- **The head-only through the ceiling hatch (t=3) is not representable** — a cartoon figure cannot do "head poking through a ceiling hole" with the standard figure rig unless a ceiling/hole prop is staged for it. Not encoded.

---

## Shot 17 — "Night one"

- **videoTime:** 520 · **duration:** 24 s · **cast:** Jimmy
- **Window (Part C):** empty wall (t=0); Jimmy lying on the bed, arms folded across his body (t=3); control-room cutaway — two men in black at a desk (t=3–9, not cast); Jimmy sitting on the bed edge, looking up at the TV (t=9–12); Jimmy standing, back to camera, watching the TV, arms at sides (t=12–15); Jimmy right arm raised straight up, fist clenched — a fist pump (t=15–18); Jimmy standing, arms down, watching (t=18–21); Jimmy turning left, right arm raised bent, pointing/reaching up at the TV (t=21–24). Drake Bell is a head-and-shoulders image on the TV/inset (t=12–24), not a person in the room.
- **Beat (Beat 11):** the room goes dark; Jimmy asleep; the mirror empty; one cartoon "Z".

### Choreography

- **Jimmy (WINDOW — the whole back half is one sustained "standing, watching the TV" pose with two raised-right-arm beats):**
  - t=0–9: lying on the bed, arms folded across his body (lying 1.0, armR 0.4, armL 0.4). The control-room cutaway (t=3–9) is a different location; Jimmy is still lying in the room, so the lying pose holds through it.
  - t=9–12: sitting on the bed edge, looking up at the TV (crouch 0.6, lean −5°). A sit is rendered as crouch.
  - t=12–21: standing, back to camera, watching the TV, looking up (lean −3°).
  - t=15–18: right arm raised straight up, fist clenched — the fist pump (armR 1.0).
  - t=21–24: turning left, right arm raised bent, pointing/reaching up at the TV (armR 0.7, lean −2°).
- **The two men at the control-room desk and Drake Bell on the TV are not cast — not encoded.** The beat research's "asleep" is the night's end; the window ends with Jimmy pointing at the TV, and the window wins for the closing beat.

---

## Shot 18 — "Morning, day two" ★ BEAT source (the TV break) + WINDOW (waking)

- **videoTime:** 805 · **duration:** 16 s · **cast:** Jimmy
- **Window (Part C):** empty cell (t=0); Jimmy lying on his side, face in the pillow, eyes open (t=3); Jimmy lying flat under the sheet, only his head and bare feet sticking out (t=3–6); cutaway, two men at a desk (t=6–9, not cast); the wall TV showing a cartoon (t=9–12, no people); Jimmy lying on his back, right arm bent behind his head, left arm extended out/off the bed (t=12–15). **The TV-breaking is NOT seen in any sampled frame. The TV is intact on the wall at t=12.**
- **Beat (Beat 12):** Jimmy wakes, grabs the TV and breaks it; the boys panic; a cake slides in; lasagna and chicken; Jimmy eats at the desk.
- **Why BEAT wins for the break:** the shot's action is "Jimmy breaks the TV." The window shows Jimmy lying in bed in three still poses — no standing, no swinging, no breaking. The break comes from the beat research.

### Choreography

- **Jimmy (WINDOW — waking/lying; BEAT — the break):**
  - t=0–9: lying in bed, waking (lying 1.0). The flat-under-the-sheet pose (only head and feet out) is a bedding/prop gag, not a body pose — the figure lying flat (lying 1.0) approximates it; the sheet itself is not rendered.
  - t=9–10: stands up (lying easing to 0).
  - t=10–11: both arms up to smash the TV — wind-up (armR 1.0, armL 1.0).
  - t=11–12: the smash down (armR 0.2, armL 0.2).
  - t=12–16: lying on his back, right arm bent behind his head, left arm extended out off the bed (lying 1.0, armR 0.4).
- **The two men at the desk (t=6–9) are not cast — not encoded.**

---

## Shot 19 — "Morning, day two" ★ BEAT source (the feast) + WINDOW (the point)

- **videoTime:** 830 · **duration:** 16 s · **cast:** Jimmy
- **Window (Part D):** empty wall (t=0–3); Jimmy bathroom selfie, talking (t=3–6); Jimmy raises his left hand, index finger up — an emphasis point (t=6–9); Jimmy's arm through the cell-door hatch holding a black phone aloft (t=9–15); Karl + Chandler standing face to face talking (t=15, not cast).
- **Beat (Beat 12):** a cake slides in; lasagna and chicken; Jimmy eats at the desk, happy; the boys watch through the mirror, miserable.
- **Why BEAT wins for t=9–15:** the shot's action is "Cake, lasagna and chicken arrive. Jimmy eats." The window shows Jimmy's arm through the hatch holding a phone — no food, no eating. The eating is the signature movement the window misses.
- **What loses:** the window's hatch reach (t=9–15, arm through the hatch holding the phone aloft) is a real Jimmy movement in this window, but it is NOT the beat. It is superseded by the eating. Stated here so the choice is visible; the parent/Nick can override it and take the hatch reach instead.

### Choreography

- **Jimmy (WINDOW — the point; BEAT — the feast):**
  - t=3–6: standing, talking to the camera (baseline + mouth).
  - t=6–9: left hand raised, index finger up — the emphasis point (armL 0.8).
  - t=9–15: sitting at the desk eating — cake, lasagna, chicken (crouch 0.65, lean +5°, armR 0.5, the fork hand).
  - t=15–16: baseline (Karl + Chandler cutaway, not cast).
- **Karl + Chandler at t=15 are not in this shot's cast — not encoded.**

---

## Shot 20 — "Maddy visits"

- **videoTime:** 521 · **duration:** 22 s · **cast:** Jimmy
- **Window (Part D):** empty wall, "I'M SO CONFUSED" (t=0–3); Jimmy sitting on the bed, legs stretched out, leaning back (t=3–6); Karl cutaway at a desk, right hand raised open as he talks (t=6–9, not cast); Jimmy sitting, looking up at the TV (t=9–15); Jimmy stands, back to camera, facing the TV (t=15–18); Jimmy raises his right fist at the TV (t=18–21); Jimmy arm down, facing the TV (t=21–22). **Maddy is not visible in any sampled frame.** The "visitor" in this window is a dark-haired man on a video call on the TV (on-screen text reads "Drake Bell"). There is no one at the door.
- **Beat (Beat 13):** Maddy at the mirror/door; Jimmy sits on the bed unmoved; the boys hover behind her; Jimmy refuses deadpan.

### Choreography

- **Jimmy (WINDOW):**
  - t=3–15: sitting on the bed, legs out, leaning back, then sitting looking up at the TV (crouch 0.6, lean −4°). A sit is rendered as crouch.
  - t=15–18: stands, back to camera, facing the TV (lean −3°).
  - t=18–21: right arm straight up, fist clenched at the screen (armR 1.0, lean −3°) — the shot's one big gesture, and the closest thing to a "refusal" in the footage.
  - t=21–22: arm down at his side, standing facing the TV (lean −3°).
- **Maddy is not visible in the window and not in the cast — no Maddy figure.** The fist pump is the refusal. Karl's cutaway gesture (t=6–9) is not encoded (not cast).

---

## Shot 21 — "The home stretch" ★ BEAT source (Beat 14)

- **videoTime:** 483 · **duration:** 24 s · **cast:** Jimmy
- **Window (Part D):** the ladder/camera sequence — empty ceiling hatch (t=0–3); Jimmy's arms up into the hatch holding a speaker (t=3–9, POV); Jimmy's hand holding the speaker aloft (t=9–12); empty bathroom (t=12–15); Jimmy on the ladder, left hand on top, smiling (t=15–18); Jimmy reaches up to the wall camera (t=18–21); Jimmy on the ladder looking down (t=21–24); a man in a blue/teal hoodie carries the ladder in (t=24, not cast).
- **Beat (Beat 14):** Jimmy alone on the mattress, staring at the ceiling; the mirror empty; a "Geronimo!" fake-out (a loud entrance that is nobody); Jimmy shrugs and lies back down.
- **Why BEAT wins:** the window is the ladder sequence — the same footage as shots 22 and 25. Using it here would triplicate the ladder across three shots. The home-stretch beat is the quiet: Jimmy lying on the mattress, staring at the ceiling, the fake-out, the shrug. The beat research gives that choreography; the window does not contain it.

### Choreography

- **Jimmy (BEAT — the home stretch):**
  - t=0–6: lying on the mattress, staring at the ceiling (lying 1.0).
  - t=6–9: sits up at the "Geronimo!" fake-out, looking toward the door (crouch 0.6, lean +4°).
  - t=9–10: shrugs — both arms slightly out/up — and realises it is nobody (armR 0.4, armL 0.4).
  - t=10–24: lies back down, staring at the ceiling (lying 1.0).
- **The blue/teal-hoodie man carrying the ladder is not cast — not encoded.** The "running out of patience" beat is not visible as a distinct gesture in the window — Jimmy reads cheerful/busy in the sampled frames, not impatient.

---

## Shot 22 — "The final morning and the time trick" ★ BEAT source (Beat 15)

- **videoTime:** 495 · **duration:** 15 s · **cast:** Jimmy, Chris, Chandler
- **Window (Part D):** the same footage as shot 21, t=12–24 (vt 495–510) — empty bathroom, Jimmy on the ladder adjusting the wall camera, the blue-hoodie man carrying the ladder in. **No door, no reaching for the door, and neither Chris nor Chandler appears in any sampled frame.**
- **Beat (Beat 15):** Jimmy wakes, pees, looks for his timer (gone); the boys outside with a leaf blower; the clock hits zero; Jimmy refuses to leave and closes the door himself; an hour later the door opens for real.
- **Why BEAT wins:** the shot's action is "Jimmy reaches for the door." The window is the ladder sequence — no door. The beat research has the door reach and the door close (the punchline of the whole video).

### Choreography

- **Jimmy (BEAT — the door):**
  - t=0–3: standing, looking for his timer (gone) — looking down/around (lean +3°).
  - t=3–6: reaches for the door (armR 0.7, lean +5°).
  - t=6–9: refuses to leave, pushes the door shut — both arms forward (armR 0.6, armL 0.6, lean +6°).
  - t=9–15: door closed, Jimmy stands, arms down (baseline).
- **Chandler (BEAT — holds the leaf blower):** both arms forward, aiming the blower (armR 0.6, armL 0.6, lean +6°, t=0–9). **Staging pick:** the beat research says "the boys outside with a leaf blower" and names no holder; Chandler is the pick (tall, two-handed prop). Not a frame fact.
- **Chris (BEAT — beside Chandler, watching):** leaning in, watching (lean +4°, t=0–9).
- **The window's ladder sequence is NOT used here.**

---

## Shot 23 — "The final morning and the time trick"

- **videoTime:** 861 · **duration:** 15 s · **cast:** Jimmy, Chris, Chandler
- **Window (Part D):** the timer hits zero — "00:00:02" over the door (t=0–3, empty); a man in a **pink hoodie + black cap** looks up at the timer (t=3–6); the pink-hoodie man leans in at the hatch, right hand flat on the door (t=6–9); the pink-hoodie man reaches down to the handle and opens the door (t=9–12); Jimmy walks out of the doorway (t=12–15); Jimmy gestures with an open palm while talking (t=15); a third man in a black t-shirt stands watching at the far left (t=15).
- **Beat (Beat 15):** the clock hits zero; the door opens; Jimmy refuses to leave, closing the door himself; an hour later the door opens for real.
- **The "Jimmy closes the door on himself" beat is NOT in this window** — the door is being opened, not closed, in the sampled frames. This window is the door opening and Jimmy walking out (the resolution, after the extra hour).

### Choreography

- **Jimmy (WINDOW):**
  - t=0–12: inside the room, not visible — baseline (the figure stands in the shot's staging).
  - t=12–14: walks out through the doorway, arms at sides (walking not representable; stand + bob).
  - t=14–15: right arm out to the side, open palm, gesturing while talking (armR 0.7, lean +3°).
- **Chandler (WINDOW — the pink-hoodie man, UNVERIFIED assignment):** the pink-hoodie man matches no cast outfit, so he cannot be confirmed as Chris or Chandler from the frames. He is most likely one of the boys (he is at the door with Jimmy, talking). The door needs an opener, so the movements are assigned to Chandler as a **staging pick, flagged UNVERIFIED** — not a confirmed identification:
  - t=3–6: walks in, looks up at the timer (lean −5°).
  - t=6–9: leans in at the hatch, right hand flat on the door (lean +6°, armR 0.5).
  - t=9–12: reaches down to the door handle, opens the door (armR 0.4, lean +5°).
  - t=12–15: stands facing Jimmy, listening (lean +3°).
- **Chris (WINDOW — the third man in black at t=15, UNVERIFIED):** standing, watching/listening (lean +4°, t=12–15). He wears a black t-shirt with no teal/blonde visible, so he cannot be confirmed as Chris by outfit either.

---

## Shot 24 — "The sliming"

- **videoTime:** 804 · **duration:** 15 s · **cast:** Jimmy, Chris, Chandler, Karl, Tyler
- **Window (Part D):** empty floor (t=0–3); Jimmy asleep in bed (t=3–9); the pink-hoodie man at a desk, turned in his chair, talking (t=9–12, cutaway, not cast); a TV showing a cartoon (t=12–15, no people); Jimmy waking, right arm raised, hand rubbing his eyes (t=15). **No door opens and no sliming happens in this window; Chris, Chandler, Karl and Tyler do not appear.**
- **Beat (Beat 16):** the four boys in a row outside under a bucket of slime; Jimmy points at the bucket; the slime dumps. (That is shot 25's material — the payoff. This shot is the door opening, the setup for it.)

### Choreography

- **Jimmy (WINDOW):** lying in bed for the whole shot (lying 1.0, t=0–15).
  - t=3–9: asleep, still (lying 1.0, arms 0).
  - t=12–15: waking, right arm raised, hand rubbing/shielding his eyes (armR 0.5).
- **Chris, Chandler, Karl, Tyler (staging choice — waiting outside the door):** leaning in, waiting (lean +4°, t=0–15). They are not visible in the window; the lean-in is a staging choice (they are outside, about to be slimed), not a frame fact. The "door opens" beat is not in this window.

---

## Shot 25 — "The sliming" ★ BEAT source (Beat 16 — the payoff)

- **videoTime:** 494 · **duration:** 15 s · **cast:** Jimmy, Chris, Chandler, Karl, Tyler
- **Window (Part D):** the same ladder sequence as shots 21/22 (vt 494–509) — empty floor, Jimmy's selfie close-up, Jimmy on the ladder adjusting the wall camera, the blue-hoodie man carrying the ladder in. **No sliming, no slime, and none of Chris, Chandler, Karl or Tyler appears in any sampled frame.**
- **Beat (Beat 16):** Jimmy comes out. "I survived 50 hours of your torture… Go ahead, dump the slime." The four boys stand in a row outside the room, a bucket of green slime above them. Jimmy points at the bucket. The slime dumps. The boys are covered in green. Chris tries to hug Jimmy: "Huggies, huggies! — No, Chris, no!" Jimmy steps back.
- **Why BEAT wins:** this is the payoff — the episode ends on the sliming, not on a twist. The window is the ladder sequence (wrong). The beat research HAS the sliming. The tool already coats the four boys in slime in this shot (sh.prop==="slime").

### Choreography

- **Jimmy (BEAT):**
  - t=0–3: standing, free, grinning (baseline + mouth).
  - t=3–6: points at the bucket (armR 0.8).
  - t=6–9: the slime dumps — Jimmy stands, arms down, watching and grinning (baseline).
  - t=9–12: Chris reaches for a hug — Jimmy steps back and holds a hand up, "No, Chris, no!" (lean −6°, armR 0.6).
  - t=12–15: stands clean, grinning, arms down (baseline).
- **Chris (BEAT):**
  - t=0–6: standing in the row, looking up at the bucket (lean −3°).
  - t=6–9: the slime dumps — flinches, bracing (crouch 0.3, lean +4°).
  - t=9–12: covered in slime, reaches for a hug — arms open (armR 0.6, armL 0.6).
  - t=12–15: standing, covered, arms down (baseline + slime coat).
- **Chandler, Karl, Tyler (BEAT):**
  - t=0–6: standing in the row, looking up at the bucket (lean −3°).
  - t=6–9: the slime dumps — flinching, bracing (crouch 0.3, lean +4°).
  - t=9–15: standing, covered in slime (baseline + slime coat).
- **The window's ladder sequence is NOT used here.**

---

## What could NOT be represented (honest list)

These are real movements in the footage or the beats that the figure rig cannot do. They are noted here, not faked with a wrong pose:

1. **Walking** (shots 01, 02, 03, 14, 23): the figures stand in fixed slots; a walk reads as standing + bob. The figure does not travel across the frame.
2. **Head-only through a ceiling hole** (shots 09, 16): Karl's head poking upside-down through the ceiling hatch has no body to pose. Shot 09 approximates it as a deep crouch + lean; shot 16 does not encode it at all.
3. **Lying flat under a sheet with only head and feet out** (shot 18): a bedding/prop gag, not a body pose. The figure lying flat (lying 1.0) approximates it; the sheet is not rendered.
4. **The tongue-out extreme close-up** (shot 15, t=12–15): a facial beat on a distorted wide-angle close-up. The motion system does not change expressions; Jimmy's expression stays "grin".
5. **Laughing for non-speakers** (shot 09 Karl, shot 10 Karl): the talking mouth is speaker-only. A non-speaker's laugh has no mouth channel.
6. **Holding a phone / speaker / bag as a prop** (shots 02, 10, 12, 13, 15, 16, 19): the arm pose is encoded; the object itself needs the shot's prop. Where a shot has no prop for the object (the phone, the bag), the figure holds the pose empty-handed.
7. **Sitting on the bed / at the desk** (shots 11, 17, 19, 20): rendered as crouch (a lowered figure). There is no chair/bed seat in the rig, so a sit reads as low, not seated.
8. **The ground shadow stays at the feet when a figure lies down.** The shadow is drawn outside the body group and does not stretch under the lying body. (The tool stretches it in the choreography build — see the tool change note in NOTES.md.)
9. **Unidentified people are not mapped.** The pink-hoodie man (shots 23–24) and the blue/teal-hoodie man (shots 21/22/25) are described in the extraction but, except for the flagged staging pick in shot 23, their movements are not assigned to any cast figure. If their identities are confirmed later, shots 23 (and the shot-24 cutaway) can be re-mapped.

## What was NOT choreographed (deliberately)

- **Non-cast people in the windows** (Karl in shot 10, the hatch boys in shot 03, the three men outside in shot 11, the control-room men, the camera operator, the pink/blue-hoodie men): the tool renders only a shot's listed cast.
- **The goat** (shot 09): on the TV only, never a live animal in the room.
- **Drake Bell** (shot 14): a face on the TV only, never in the room.
- **Maddy** (shot 20): not visible in the window and not in the cast.
- **The "Jimmy closes the door on himself" beat** (shot 23's window): not in the window at vt=861 — the door opens and Jimmy walks out. The close lives at a different videoTime than 861 (Beat 15's earlier moment). Shot 22 stages the close from the beat research instead.

## Source tally

- **WINDOW carries the shot:** 01, 02, 03, 04, 09, 10, 12, 13, 14, 16, 17, 20, 23, 24 (14 shots; 07 is window-thin — listening only).
- **BEAT carries the shot (signature movement the window misses):** 05, 06, 08, 11, 18, 19, 21, 22, 25 (9 shots) + Tyler's arrival in 15.
- **Mixed (window for one figure, beat for another):** 10 (Jimmy lying = BEAT, the rest = WINDOW), 15 (Jimmy = WINDOW, Tyler = BEAT), 19 (Jimmy's point = WINDOW, the feast = BEAT).
- **Shots where the window and the beat disagree and the file chose:** 05 (smash over lying), 19 (eating over the hatch reach), 21 (home stretch over the ladder), 22 (door over the ladder), 25 (sliming over the ladder). Each choice is stated in its shot section with the reason.

## Tool encoding

`CINE_CHOREO` in the Drawing Tool: keyed by global shot number (1–25, timeline order), then by character ID (`fig-crew-jimmy`, `fig-crew-chris`, `fig-crew-chandler`, `fig-crew-karl`, `fig-crew-tyler`), then a list of moves `{at, dur, ch, val}` with `ch` in `armR | armL | lean | crouch | lying`. It applies only to the solitary episode (gated on `sc.setId==="solitary-room"`); every other episode keeps the old generic gesture-at-the-beat. The bob and the talking mouth are unchanged. Motion stays a pure deterministic function of (shot, figure, t).
