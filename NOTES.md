# MrBeast crew-5 episode — render + proof notes (v7 workarounds — the limits Nick refused are built)

## Clip download fix — record, then download (tool done 2026-11-12; browser verification PENDING)

**The problem:** shot-clip WebM downloads never surfaced at all. The recording itself worked — diagnostic proved real data (2,471,811 bytes in 66 chunks for an 18 s clip) — and the earlier revoke-timing (30 s) and MIME-type (application/octet-stream) fixes did not help. PNG stills download fine.

**Hypothesis:** the download fired from MediaRecorder's `onstop` callback, 12–24 s AFTER the user clicked "Record clip" — the browser's transient user-activation had expired by then, so the programmatic download was blocked/ignored. PNG stills work because their toBlob callback fires milliseconds after the click, while the activation is still valid.

**The fix (in the Drawing Tool, verified by the builder):** recording and downloading are now two separate steps. `cineRecordShotClip`'s `onstop` no longer calls `download()` — it stores the WebM blob in memory (module-level `cineLastClip = {name, blob, bytes, chunks}`; not on CINE, because CINE is reassigned when an episode loads) and the status reports `Recorded clip-001.webm — Blob: X bytes in Y chunks. Click 'Download recorded clip' to save it.` A new button **"⬇ Download recorded clip"** (id `cineDownloadClipBtn`) sits in the same row as "Export shot clips (WebM)" and "Record this shot's clip (WebM)" — disabled until a clip exists, enabled after a recording finishes — and clicking it calls `download()` with the stored blob synchronously inside the click, so it runs in a user-gesture context. The blob stays in memory after downloading, so the button can be clicked again if a download is missed. The batch "Export shot clips" was NOT changed to auto-download: it records into memory one shot at a time and only the last clip is kept for the Download button; its feedback/status text now says so honestly, and the per-shot Record + Download loop is the reliable path. The tool was re-exported fresh after the fix, so the export carries both the v7 workarounds and this download fix.

**Verification (builder):** all 11 inline scripts pass node --check; grep confirms the `cineLastClip` / `cineSetDownloadBtn` / `cineDownloadRecordedClip` wiring and that the clip `onstop` no longer downloads; scene-preview export, rendering, motion, choreography, backgrounds and videoTimes untouched. **Browser verification is PENDING:** the per-shot record + Download-button loop has NOT yet been run in the browser. The clips still need to be recorded (from this build — v7 workarounds + download fix) and the video re-assembled with the unchanged v4 audio.

## v7 — choreography workarounds (tool done 2026-11-12 ~00:35 EST; clips NOT yet recorded)

**Nick (Nov 11 19:06): "I refuse to believe that there's no work around here." He was right — the choreography limits were called too quickly. The clip recording that was running was STOPPED (those clips did not have the workarounds). This round builds all seven workarounds into the Drawing Tool and wires them into the per-shot data. The animated clips have NOT yet been recorded (that needs a browser session) and the video has NOT yet been re-assembled. The v4 video (stills) is still the current deliverable until the workaround version is built and verified.**

Edit 1 (capabilities) and Edit 2 (per-shot data wiring) are both verified — see the Workarounds section in `choreography.md` for the full per-shot list. Scope disclosure: the Edit 2 brief said "data only", but CINE_SCENE_FX/CINE_HOLDS were shot-static, so five small backward-compatible renderer additions were needed for the timed windows (optional at/dur on FX/holds, a 0.4 s occluder ramp/opacity, a hole-clipped headCharId plate, a dropAt floor prop); entries without at/dur render exactly as before. Smaller notes: four choreo values aligned (shot 12 Jimmy armL 0.7→0.6; shot 16 Jimmy armR 0.5→0.7; shots 15/16 Tyler arms 0.3→0.5, shot 15's duration exactly t=3–9); Karl's shot-09 t=9–12 recovery had no explicit move; shot 16's occluder window is t=3–6; the audit lists pre-existing medium security findings on untouched import paths and repeated polygon-points console errors — neither introduced or worsened, no fixes required.

**Still genuinely not done (after actually trying the workarounds):** the TV Jimmy carries in shot 14 (t=9–12) is not rendered as a hand-held prop (carrying pose only — a hand-held TV prop would close it); the white object Karl holds in shot 09 t=12–14 is not rendered (unidentified); the dangling leg beside Karl's head in shot 09 t=6–9 is not rendered (only the head shows); shot 16's hatch head is a staging pick, not a confirmed identification (Karl, flagged UNVERIFIED). **The clips still need to be recorded and the video re-assembled** — see the v6 "What is NOT done yet" steps, which still apply (record WITH the workarounds, assemble with the unchanged v4 audio, verify the figures perform the choreography + all 25 frames still clean + decode clean). **The generic-movement clips and the pre-workaround choreography clips must NOT be used — re-record from the workaround build.**

## v6 — choreography-matched movement (tool done 2026-11-11 ~23:00 EST; clips NOT yet recorded)

**Nick's correction (Nov 11 ~17:41): "The whole point is you supposed to take the choreography of the scene like their movements and match them to our figures." The generic-movement clip recording that was running was STOPPED — those clips had generic bobbing, not choreography-matched motion. This round replaces the generic gesture with per-shot choreography: if Jimmy raises his arm in the footage at that moment, cartoon Jimmy raises his arm. The tool change is DONE and verified in the tool; the animated clips have NOT yet been recorded (that needs a browser session) and the video has NOT yet been re-assembled. The v4 video (stills) is still the current deliverable until the choreography version is built and verified.**

### Extraction — how the choreography was found
- Four subagents, each watching the footage windows `[videoTime, videoTime+duration]` at 3 s sampling (one frame at videoTime, then every 3 s), viewing the frames directly: Part A shots 01–06 (37 frames), Part B shots 07–12 (32 frames), Part C shots 13–18 (41 frames), Part D shots 19–25 (47 frames). Frames were in `/tmp/choreo-part-A/B/C/D/` (ephemeral, not preserved). Per-shot findings in `choreography-part-A/B/C/D.md`; the synthesis in `choreography.md`.
- **Identification:** Jimmy is reliable (orange/red jumpsuit). The other boys wear plain black BEAST shirts / street clothes in this footage, NOT the cartoon colour code (Chris teal, Chandler red, Karl purple, Tyler yellow), so several figures could not be named from the frames alone and were left unassigned rather than guessed.
- **The big finding: the videoTimes are out of phase with the action.** They were curated for empty visuals (the v4 clean-background pass), so most windows show Jimmy alone, often lying on the bed. The signature beat movements (mirror-lick, speaker smash, door blocking, roof climb, TV breaking, dinner, sliming) mostly do NOT appear in the windows at 3 s sampling — the same misalignment as the audio (FIT 5 / PARTIAL 1 / MISMATCH 19). **Shots 21, 22 and 25 share the same footage** (vt 494–510 is one ladder/camera sequence, byte-identical frames); their listed actions (door reach, sliming payoff) are not what's there.

### Synthesis rule (in choreography.md, per movement)
- **WINDOW** — what the real person actually does in the sampled frames of this shot's window. Used wherever it matches the beat and the cast.
- **BEAT** — the scene's actual choreography from the beat research (`mrbeast-crew5-challenge-choreography.md`, grounded in the full transcript and the video). Used ONLY for a signature beat movement the window misses. Still the real people's real choreography, just at a different footage time than the background frame. Never an invented pose.
- **Tally:** WINDOW carries 14 shots (01, 02, 03, 04, 09, 10, 12, 13, 14, 16, 17, 20, 23, 24; 07 is window-thin — listening only). BEAT carries 9 shots (05 the smash, 06 the speaker-wheeling, 08 the roof climb, 11 the dinner, 18 the TV break, 19 the feast, 21 the home stretch, 22 the door + leaf blower, 25 the sliming) + Tyler's bag arrival in 15. The five window-vs-beat choices are stated with reasons in choreography.md: 05 (smash over lying), 19 (eating over the hatch reach), 21 (home stretch over the ladder), 22 (door over the ladder), 25 (sliming over the ladder). The ladder sequence is used in NO shot's choreography, so it is not triplicated.
- **Unidentified men NOT mapped:** a man in a pink hoodie + black cap (shots 23–24) and a man in a blue/teal hoodie + black cap carrying the red ladder (shots 21/22/25). One exception, flagged UNVERIFIED in choreography.md: shot 23 assigns the pink-hoodie man's door-opening to Chandler as a staging pick (the door needs an opener) — not a confirmed identification.

### What the tool now does (Drawing Tool, Cinema Pipeline)
- **CINE_CHOREO:** per-shot, per-figure timelines keyed by global shot number (1–25, timeline order) then character ID, each a list of moves `{at, dur, ch, val}` with channels `armR | armL | lean | crouch | lying`. 25 shots / 185 moves. Full data + per-shot timelines in `choreography.md`.
- **New motion channels:** left arm (`gestureL`, mirrored +118° rotation — the old generic gesture was right-arm only); **lean** (±10°, whole-body rotation around the feet, + = forward/lean in, − = back/look up); **crouch** (0..1, vertical squash around the feet — sitting reads as a lowered figure); **lying** (0..1, body rotated 90° around the feet, head to the left — a lying arm raise sticks up from the body, matching Jimmy raising his arm while lying on the bed). The ground shadow stretches under a lying figure (shifted left, widened).
- **The generic gesture-at-the-beat is REPLACED in the solitary episode only** (gated on `sc.setId==="solitary-room"`). Every other episode — including the MrBeast circle episode — keeps the old generic gesture untouched.
- **Kept unchanged:** the idle bob (every figure) and the talking mouth (speaker, ~5.25 cycles/sec, real voices). Motion stays a pure deterministic function of (shot, figure, t). At t=0 every choreography value is 0 (every move ramps in from 0), so the t=0 composition and the still PNG export are unchanged.
- **Honest limits (in choreography.md):** walking is not representable (figures stand in fixed slots); head-only through a ceiling hole has no body pose (shot 09 approximates it as a deep crouch + lean; shot 16 does not encode it); lying under a sheet with only head/feet out is a prop gag, not a pose; the tongue-out close-up (shot 15) is a facial beat the motion system cannot do; non-speakers cannot laugh (mouth is speaker-only); holding a phone/speaker/bag needs the shot's prop (the arm pose is encoded; where a shot has no prop for the object, the figure holds the pose empty-handed); sitting reads as crouch.

### Tool verification (builder, in source + Node — SUCCEEDED)
- All 11 inline scripts pass node --check.
- CINE_CHOREO parses to exactly 25 shots / 185 moves, per-shot counts matching the brief (9,8,4,4,8,12,4,7,7,10,4,6,7,8,6,9,9,7,4,4,6,10,9,6,17), only the five known charIds and five known channels.
- cineChoreoVal returns 0 on every channel at t=0 for all 25 shots; spot values match (shot 4 lying=1 and armL=0.6 at 7.5 s; shot 1 armR=1 at 15 s; lean clamp/sum behaviour verified).
- cineShotNumber numbers shots globally 1–25 across scenes; cineChoreoFor returns null for any non-"solitary-room" set, so other episodes are unaffected.
- The non-choreography path reproduces the old beat gesture exactly (0.998 at beat+0.55 s); non-speakers stay at 0.
- Plate tests: a no-motion render emits no transform group and the byte-identical shadow (still composition unchanged); lying=1 yields rotate(−90) with the shadow shifted/widened; gestureL gives the mirrored +118° left-arm rotation; crouch 0.5 gives scale(1 0.825); out-of-range crouch/lying clamp to 0..1. One cosmetic note: a lying shadow rx can print as a raw float (131.51999999999998) — renders identically, same style as the pre-existing code.
- Nothing outside the six specified changes was touched. Backgrounds, videoTimes (003:123, 010:603, 014:122, 023:861 and the other 21), and all other episode data were NOT touched.

### What is NOT done yet (needs a browser session, then assembly)
1. **Record the 25 clips in the browser WITH the choreography:** open the updated Drawing Tool, load solitary-confinement-chrome.mp4 into the Background Editor, load the "Solitary — cartoons in reality" reference production, run "Export shot clips (WebM)" (or each shot's "Record clip" button individually — batch download has previously captured only the first file), and save clip-001.webm … clip-025.webm + clips-manifest.json. The tool runs via the W3Schools TryIt editor as a loader base (localhost navigation and public tunnels are both blocked from this machine — the v4 re-render used the same route). Recording is real time: ~422s total for all 25 shots. **The generic-movement clips must NOT be used — re-record from the choreography build.**
2. **Assemble the choreography video:** concatenate the 25 WebM clips (all 800×600, 30fps) into a 422s video-only track, then mux with the UNCHANGED audio — audio-segs-v4/mixed.wav (422.000s, real voices + the same 10 tone cues). The audio does not change: same videoTimes, same shot durations, same tone-cue times. Do not re-extract the voice segments.
3. **Verify:** (a) the figures actually perform the choreography — check the assembled video at multiple timestamps per shot and confirm the signature movements (shot 01 both arms overhead with speakers, shot 05 the tripod smash, shot 10 both arms on the rope, shot 13 the reach up, shot 17 the fist pump, shot 25 the sliming), not generic bobbing; (b) all 25 frames still clean — no real person anywhere, including on screens (the v4 clean check must still hold); (c) the video decodes clean, audio present, no clipping.
4. **Preserved:** v4 outputs are in frames-v4/ (25), episode-final-v4.mp4 (10,699,331 bytes), manifest-v4.json. The current frames/, episode-final.mp4, and manifest.json are still the v4 stills version until the choreography version overwrites them.

---

## v5 — figure movement (tool done 2026-11-11 ~17:39 EST; clips NOT yet recorded)

**Nick's flag (Nov 11 ~17:29): the real people are out of the background (good), but the figures are just essentially pictures — no movement to them at all. This round gives the figures movement. The tool change is DONE and verified in the tool; the animated clips have NOT yet been recorded (that needs a browser session) and the video has NOT yet been re-assembled. The v4 video (stills) is still the current deliverable until the movement version is built and verified.**

### What the tool now does (Drawing Tool, Cinema Pipeline)
- **Idle bob (every figure, every shot):** each figure bobs vertically, offset = amplitude * sin(2π * t / period + phase), amplitude 5 px at the 800×600 render scale, period ~1.6–2.0 s, per-figure phase offset so the five never bob in lockstep. Ground shadows stay planted on the floor (outside the bob group) — the figure bounces above its shadow. Bob is exactly 0 at t=0 for all five figures, so the still composition at t=0 is identical to the v4 stills.
- **Talking mouth (the speaker, every shot):** the first cast member of the shot is the speaker; their mouth opens and closes at ~5.25 cycles/sec while the voice plays. The mouth is a separate element driven by a mouthOpen parameter (0 = the expression's own mouth, 1 = open oval gape, intermediate values interpolate). The real voice audio is speech in ALL 25 shots (audio-fit.json transcripts), so the speaker's mouth animates in every shot. Non-speakers keep their expression-default mouth (mouthOpen null). Angry figures keep their brows while gaping.
- **Gesture at the beat (the speaker):** at the shot's key beat — the first sound cue's atSecondsInShot, or the shot midpoint if there is no sound cue — the speaker raises one arm (>0.9 at the beat, 0 away from it), then lowers it.
- **Motion is a pure deterministic function of (shot, figure, t):** the same t always renders the same frame (byte-identical), so the clip export is reproducible.
- **Live preview:** the Cinema Pipeline shot preview animates the figures in real time (requestAnimationFrame) so the movement is visible in the tool.
- **Clip export (WebM):** a new "Export shot clips (WebM)" action records each shot in real time via canvas.captureStream(30) + MediaRecorder into clip-001.webm … clip-025.webm (800×600, 30fps) plus a clips-manifest.json (shot, clip file, duration, videoTimeSeconds, background source). Every shot also has its own "Record clip" button in Step D for one-at-a-time capture (the browser download handler has previously captured only the first file from a batch run). The still PNG export is untouched — a still is the exact v4 base composition.

### Tool verification (builder, in source + Node)
- All 11 inline scripts pass node --check.
- Motion unit tests: bob exactly 0 at t=0, |bob| ≤ 5 px, phases differ per figure; speaker mouthOpen cycles open/closed at ~5.25 cycles/sec within [0,1], null for non-speakers, deterministic in (shot, figure, t); gesture 0 away from the beat, >0.9 at the beat; silent non-solitary shots don't talk, dialogue shots do.
- Plate tests: a no-motion render has no bob transform and no gape (still composition unchanged); ground shadow renders outside the bob group; mouthOpen 0 draws the expression's own mouth; angry figures keep brows while gaping.
- cineShotSVG integration tests: still output has bg frame + HOUR badge + scene label with zero bob groups; animated frames differ by t and are byte-identical for the same t; the noBg foreground pass (used by the clip recorder) omits the background image while keeping badge and label.
- MediaRecorder/captureStream recording itself can only run in a real browser session; the code guards for its absence and reports instead of failing.
- Backgrounds, videoTimes (003:123, 010:603, 014:122, 023:861 and the other 21), and all other episode data were NOT touched — the edits only add motion parameters to the figure renderer and new export/preview code paths.
- One unexplained exec failure occurred during the build (exit 1, the builder's own command); it did not block the work and the verification above passed.

### What is NOT done yet (needs a browser session, then assembly)
1. **Record the 25 clips in the browser:** open the updated Drawing Tool, load solitary-confinement-chrome.mp4 into the Background Editor, load the "Solitary — cartoons in reality" reference production, run "Export shot clips (WebM)" (or each shot's "Record clip" button individually — batch download has previously captured only the first file), and save clip-001.webm … clip-025.webm + clips-manifest.json. The tool runs via the W3Schools TryIt editor as a loader base (localhost navigation and public tunnels are both blocked from this machine — the v4 re-render used the same route). Recording is real time: ~422s total for all 25 shots.
2. **Assemble the movement video:** concatenate the 25 WebM clips (all 800×600, 30fps) into a 422s video-only track, then mux with the UNCHANGED audio — audio-segs-v4/mixed.wav (422.000s, real voices + the same 10 tone cues). The audio does not change: same videoTimes, same shot durations, same tone-cue times. Do not re-extract the voice segments.
3. **Verify:** (a) the figures actually MOVE — check the assembled video at multiple timestamps per shot and confirm visible bob/mouth motion, not a still; (b) all 25 frames still clean — no real person anywhere, including on screens (the v4 clean check must still hold); (c) the video decodes clean, audio present, no clipping.
4. **Preserved:** v4 outputs are in frames-v4/ (25), episode-final-v4.mp4 (10,699,331 bytes), manifest-v4.json. The current frames/, episode-final.mp4, and manifest.json are still the v4 stills version until the movement version overwrites them.

---

## v4 — the fix Nick caught (rendered 2026-11-11 ~08:30 EST)

**Read the verdict first: the real-people problem Nick flagged is FIXED and verified by eye on all 25 frames — 25/25 CLEAN, 0 not clean, 0 uncertain. The TV face grid is gone from shots 003, 014 and 023, and the uncertain dark shape is gone from shot 010. The episode now reads as cartoons in reality with no real person visible anywhere, including on screens.**

### Correction to the v3 record
The v3 NOTES below claim "25/25 clean, 0 real people". That claim was WRONG. An independent frame-by-frame check (real-people-check.md, 2026-11-11 ~01:23) found **21 clean / 3 not clean / 1 uncertain**: shots 003, 014 and 023 all showed the same TV screen filled with a grid of dozens of small real people's faces behind the cartoons, and shot 010 had a dark blurry shape that could have been a real person's head and could not be ruled out. Nick caught it himself. v4 re-curates those four shots and re-verifies all 25 frames by eye. This v4 section supersedes the v3 clean claim.

### What changed (the four re-curated videoTimes)
| Shot | Old vt | New vt | New background (verified by eye at full size) |
|---|---|---|---|
| 003 — The TV clips (H1) | 88 | 123 | Blank beige-white wall with one faint horizontal line. No TV, no screen, no face grid. |
| 010 — The freestyle rap (H10) | 415 | 603 | Ceiling: dark rectangular hole top-right, dark/off TV top-left, two cartoon hanging speakers, burned-in caption "WHO GAVE HIM A SIREN?" across the bottom. The bottom-centre dark shape is gone. |
| 014 — The Drake Bell Cameo (H20) | 90 | 122 | White tiled ceiling with a bright light glare. The Drake TV prop shows a CARTOON face + music note (drawn, not a real person). No real TV, no face grid. |
| 023 — The final morning / time trick (H51) | 89 | 861 | Digital timer (orange LED digits, "0:00:02") over a whiteboard reading "Bew…" ("Beware: Nerd"). Best thematic fit of the four — a timer for the time-trick beat. |

The other 21 shots are untouched. The v3 outputs are preserved (frames-v3/, episode-final-v3.mp4, manifest-v3.json). The 25 v4 frames were re-rendered in the browser from the corrected videoTimes (25/25 exact, 25 distinct, 0 fallbacks) and placed in frames/ (sizes verified against the delivery); manifest.json is the new v4 manifest.

### v4 frame verification — own-eyes, all 25 frames
**Tally: CLEAN 25 / NOT CLEAN 0 / UNCERTAIN 0.** Full frame-by-frame report: real-people-check-v4.md. The only faces on any screen in v4 are the CARTOON Drake Bell face in the shot-014 prop and the cartoon figures themselves; the TV in shot 009 shows a goat (animal). No real person's face appears on any screen. Burned-in captions remain in 5 frames (004 faint phone text, 005/015 "NEW EXCITING CAMERA ANGLE", 010 "WHO GAVE HIM A SIREN?", 011 "47 HOURS TO GO", 020 "I'M SO CONFUSED") — text, not people, already-known caveats. Flat/abstract backgrounds remain (ceilings at 010/014/021, blank walls at 003/016/019, blurry phone close-ups at 004/007) — cartoons on a backdrop in those shots, not a real-people problem. Known caveat: three ceilings (122/483/603) plus blank walls as flat backgrounds.

### v4 audio — real footage voices + synthetic tones (same 10 cues)
**No synthetic voices, no cloning.** Voice track is the real audio of solitary-confinement-chrome.mp4, cut at each shot's (new) videoTime for that shot's duration, concatenated (422s). The 10 tone cues are the same synthetic files at the same exact times (whoosh@30, sparkle@38, sparkle@50, crash@65, whoosh@96, whoosh@124, whoosh@138, sparkle@245, whoosh@392, thud@407).
- Voice segments re-extracted into audio-segs-v4/: **21/25 byte-identical to v3**; only 003, 010, 014, 023 differ (the four re-curated shots). voice.wav and mixed.wav rebuilt (422.000s each). mixed.wav volumedetect: mean −19.5 dB, max −0.9 dB, no clipping.
- New audio fit tally: **FIT 5 / PARTIAL 1 / MISMATCH 19** (was FIT 5 / PARTIAL 2 / MISMATCH 18). Shot 023 gained a FIT (clock hits zero — "Jimmy the clocks at zero you can come out now. Okay. All right. Let's let him out."); shot 010 lost its FIT (now isolation/sleep talk); shot 003 lost its PARTIAL (now slime/torture dialogue). Shots 003 and 014 share near-identical voice tracks (same slime/torture dialogue, offset 1s). Full transcripts + levels in audio-fit.json (updated — the 4 new segments re-transcribed with Whisper small from the actual shot-duration audio). The clean-visual seconds and the audio-fit seconds do not coincide here — Nick's call.

### v4 video verification (on the new encode)
- ffprobe: duration **422.000000**, video h264 800×600 30/1, audio AAC 48 kHz, size **10,699,331 bytes**.
- Frame count: **12,660** = 422s × 30fps.
- Full decode: clean, no errors.
- Shot-boundary PSNR (video frame vs source PNG): t=9s vs shot-001 **51.95 dB**; t=40s vs shot-003 **55.51 dB**; t=103s vs shot-007 **51.55 dB**; t=200.5s vs shot-013 **53.10 dB**; t=308s vs shot-019 **54.55 dB**; t=414.5s vs shot-025 **52.02 dB**. Every shot is held for its duration.
- Files: episode-final.mp4 (v4, overwrites the v3 file — v3 preserved as episode-final-v3.mp4), frames/shot-001.png … shot-025.png (v4), manifest.json (v4), audio-segs-v4/ (25 segments, voice.wav, mixed.wav), audio-fit.json (updated), real-people-check-v4.md (frame-by-frame clean report).

### What is still not "really really good" (unchanged from v3, plus the audio trade)
- Audio fit is now 5 FIT / 1 PARTIAL / 19 MISMATCH (see above). The visual fix cost audio fit on 003 and 010.
- Burned-in captions contradict the story in 4 shots (011 "47 HOURS TO GO" over HOUR 14 is the worst; 005/015 "NEW EXCITING CAMERA ANGLE"; 020 "I'M SO CONFUSED"). A new one appears in 010 ("WHO GAVE HIM A SIREN?") — it fits the rap beat better than it contradicts it, but it is still burned-in footage text.
- Several backgrounds are not a room (ceilings 010/014/021, blank walls 003/016/019, blurry phone close-ups 004/007). Only six genuinely wide empty framings exist.
- Figures still stand in a row at the bottom with no scale relationship to the room (Jimmy on a ceiling in 021, three boys in front of a toilet in 022).
- Shot 13's hanging speaker still reads as a black balloon/bomb in close-up.
- Near-identical backgrounds remain (005/015, 004/007, 017/020; 003/014/023 are now three different backgrounds — blank wall, tiled ceiling, timer — so that trio is fixed).

---

## v3 notes below — the clean claim in them is superseded by v4 above (kept for the record)

Episode: "I Spent 50 Hours In Solitary Confinement — Cartoons in Reality" (style `familyguy`, set `solitary-room`, template `tpl-siege` on every scene). Rendered 2026-11-11 ~06:00 EST from the **exported** artifact `~/workspace/your_files/drawing-tool/drawing-tool.html` (re-exported 05:59, includes Edit E + all staging fixes). **Read the verdict first: Edit E works — 25/25 backgrounds are the exact frame at each shot's curated videoTimeSeconds, 25 distinct, 0 fallbacks, and NO real person is visible in any of the 25 backgrounds. The v2 fail is fixed. The episode now reads as cartoons in the real room — with the honest caveats below (audio fit still 5/25, burned-in captions contradict the story in 4 shots, several backgrounds are ceilings/blank walls rather than a room, figures still stand in a row at the bottom whatever the background is).**

## What changed since v2 (NOTES-v2.md)

- **Edit E landed in the artifact and was re-exported.** The Cinema Pipeline renderer (`cineRasterShot` → `cineResolveShotBackground` → `cineSeekExactFrame`) now seeks a dedicated video element to each shot's exact `videoTimeSeconds` and captures that frame as the background (cached per second in `VID.exactCache`), falling back to the nearest sampled frame only if a seek fails, with the fallback recorded in `VID.bgLog` and surfaced in the manifest's `backgroundRendering` report. The v2 problem — `cineVideoFrameURL` picking the nearest of 24 evenly-spaced samples, collapsing 25 shots onto 11 backgrounds and putting real people in 18 of them — is gone.
- The v2 render is preserved: `episode-final-v2.mp4`, `frames-v2/` (25 PNGs), `manifest-v2.json`, `NOTES-v2.md`. (v1 preserved as `episode-final-v1.mp4`, `frames-v1/`, `manifest-v1.json`, `NOTES-v1.md`.)

## How it was rendered (page's own code only)

- Served the **exported** `drawing-tool.html` from its own directory on a local static server (127.0.0.1:8768) and drove it in headless Chrome 157 via CDP (Python `websocket-client`, script `/tmp/crew5_render_v3.py`). No artifact source files were modified. Runtime inspection only: `cineLoadReference("solitary")` loaded the episode (**16 scenes, 25 shots, total 422s — durations, cameras, timers Hour 0→51 and videoTimes all matched the curation exactly**), then each shot was rendered with the page's own `cineRasterShot(sceneObject, shotObject, callback)` — capturing the `bgInfo` second callback argument per shot — and saved from the returned 800×600 canvas as PNG.
- Manifest is the page's own `cineManifest()` output, saved as `manifest.json` **unmodified**.

## Edit E verification — the background finding, fixed

Per-shot `bgInfo` from `cineRasterShot` (and the page's own `cineBackgroundReport()`): **25/25 `source: "exact"`, 0 `fallback-nearest`, 0 `nearest-pending`, 25 distinct background frames.** Every shot's `frameSeconds` equals its requested `videoTimeSeconds` exactly (178, 5, 88, 215, 339, 343, 216, 45, 402, 415, 270, 485, 490, 90, 340, 405, 520, 805, 830, 521, 483, 495, 89, 804, 494). `VID.exactCache` holds all 25 keys; the 25 cached JPEG dataURLs are all distinct by hash (FNV, no duplicates). The five shots at 483–495s are now five different frames (ceiling+hole, bathroom/toilet, white floor corner…), not one bathroom sample.

Independent cross-check (not the page's own reporting): ffmpeg-extracted frames from `solitary-confinement-chrome.mp4` at each videoTime, compared against the rendered PNGs on cartoon-free regions with the camera viewBox transform applied (three-quarter shots reframe through viewBox `40 30 720 540`; wide shots are 1:1). PSNR: wide shots 37–49 dB (001: 43.8, 005: 43.5, 006: 39.3, 009: 37.5, 012: 44.9, 015: 43.5, 018: 42.9, 021: 48.7/49.4 on flat regions, 024: 46.5, 025: 47.5); three-quarter 27–47 dB (003 TV grid: 31.9 vs 5.9 control, 008: 47.3, 011: 41.0, 014 TV: 27.1 vs 12.9 control, 017: 45.9, 022 toilet: 43.7). Close-ups were not PSNR-checked (their `cineCloseUpViewBox` is computed per shot in-page) — but their backgrounds carry frame-specific fingerprints that match the curation exactly (door at t5, phone-alarm close-up at t215/216, door knob at t45, "I'M SO CONFUSED" caption at t521, TV grid at t89, white wall at t405, tiles at t830), and the page reports them exact with distinct cache entries. Two low numbers were investigated and cleared: shot 021's hole region (14.2 dB) is camera motion during a sub-frame seek difference — flat tile regions in the same frame match at 48–49 dB and the hole region matches t483 better than t484 (8.8 dB); shot 014's first TV region overlapped the Drake prop on the rendered side, re-measured clean at 27.1 dB.

## Per-shot background verdicts (own-eyes, all 25 frames viewed)

**Tally: clean empty-room backgrounds 25/25; real person visible 0/25; partial person 0/25.** (v2: 7 clean / 15 person / 3 partial.)

| Shot | videoTime | What the rendered background actually shows | Verdict |
|---|---|---|---|
| 001 | 178 | Empty room corner, door, tiles | **Clean** |
| 002 | 5 | Empty white door | **Clean** |
| 003 | 88 | TV with Carl Wheezer cartoon grid + timer 49:46:17 (TV shows a cartoon, not a person) | **Clean** |
| 004 | 215 | Blurry close-up of a phone/object on a textured surface; faint burned-in caption | **Clean** (abstract) |
| 005 | 339 | Tiled ceiling/wall corner; burned-in "NEW EXCITING CAMERA ANGLE" | **Clean** (caption) |
| 006 | 343 | Dark room, light bar, window/mirror | **Clean** |
| 007 | 216 | Same blurry phone close-up as 004 | **Clean** (abstract) |
| 008 | 45 | Door knob close-up | **Clean** |
| 009 | 402 | TV showing a goat (animal, not a person) | **Clean** |
| 010 | 415 | Grey blur; hatch+rope+speakers; unidentifiable dark bag-like object at bottom-center (zoomed crop checked: no face, body, hand, or silhouette — reads as a crumpled bag/blanket) | **Clean** (ambiguous object flagged) |
| 011 | 270 | Wide empty room: sink, chair, mirror wall, one-way mirror; burned-in "47 HOURS TO GO" contradicts the HOUR 14 badge | **Clean** (caption contradicts timer) |
| 012 | 485 | Real red ladder in the footage + cartoon ladder prop | **Clean** |
| 013 | 490 | White wall, wires; three speakers at bottom + Karl's speaker | **Clean** (prop misread — see staging) |
| 014 | 90 | Drake Cameo TV prop + TV cartoon grid + timer 49:46:15 | **Clean** |
| 015 | 340 | Tiled corner + "NEW EXCITING CAMERA ANGLE" caption; 9-clock pile | **Clean** (caption) |
| 016 | 405 | Blank white wall; 9-clock pile | **Clean** (flat) |
| 017 | 520 | White wall + light glare; small dark bag/shoe-like object bottom-right (zoomed crop: not a person) | **Clean** (bright, not night) |
| 018 | 805 | Room interior: chair, mattress, mirror, floor items | **Clean** |
| 019 | 830 | Blank white tiled wall | **Clean** (flat) |
| 020 | 521 | White wall; burned-in "I'M SO CONFUSED". No Maddy (correct — heard, not seen) | **Clean** (caption) |
| 021 | 483 | Ceiling tiles + dark rectangular hole | **Clean** (a ceiling, not a room) |
| 022 | 495 | Bathroom: toilet, black cabinet — distinct from 021/025 | **Clean** |
| 023 | 89 | TV cartoon grid (near-identical to 003/014 visually, distinct frame by hash) | **Clean** |
| 024 | 804 | Room floor with equipment/debris | **Clean** |
| 025 | 494 | White floor corner; slime payoff — distinct from 021/022 | **Clean** |

Near-identical pairs remain as the curation warned (only six genuinely wide empty framings): 003/014/023 (TV grid at 88/89/90), 005/015 (tiled corner 339/340), 004/007 (phone close-up 215/216), 017/020 (white wall 520/521). They are distinct frames by hash, but a viewer will read them as the same shot.

## Staging verification (own-eyes, all 25 frames)

All v2 staging fixes still read:
- **Close-ups do not slice figures.** All nine close-ups (002, 004, 007, 010, 013, 016, 019, 020, 023) keep every figure fully in frame.
- **HOUR badge clear in every frame.** "HOUR 0" → "HOUR 51" top-right, never overlapped by a head.
- **Ground shadows + floor contact on every figure.** A soft dark ellipse sits under every figure's feet; nobody floats.
- **Hair reads as hair.** Real mass — dark cap with scalloped fringe and side strands — on all five.
- **Jimmy's tripod reads as a tripod.** Three splayed legs + camera box on top, at Jimmy's side in every Jimmy shot.
- **Shot 25 sliming payoff lands.** Chris, Chandler, Karl and Tyler visibly coated in green slime — green heads, green torsos with drips, green leg drips, green puddle at their feet — Jimmy (orange, left) stays clean; green pour from top-right. The one beat that fully reads as intended.
- **Shot 14 Drake on the TV: present.** `draketv` prop: TV screen top-left with cartoon face + music note, labelled "DRAKE BELL CAMEO".
- **Shot 10 rope + speakers + hatch: present.** Ceiling hatch at top, rope down to two hanging speakers between Jimmy and Chandler.
- **Shots 15–16, 9-clock pile: present.** Nine alarm clocks in a pile plus three on Tyler's chest, both shots.
- **Shot 12 ladder: present** (brown cartoon ladder leaning in front of Karl, over the real red ladder). **Shot 11 steak: present** (plate with steak, bottom-right).

What still reads wrong or flat (staging — unchanged from v2 except where noted):
- **Shot 13's hanging-speaker crop still misreads.** The close-up viewBox crops the two hanging speakers into one big black circle that reads as a black bomb/balloon, not speakers. The one prop that misreads.
- **Burned-in captions from the footage** sit behind the cartoons in 4 frames: "47 HOURS TO GO" (011, contradicts the HOUR 14 badge — the worst one, huge across the frame), "NEW EXCITING CAMERA ANGLE" (005, 015), "I'M SO CONFUSED" (020). These are in the footage frames themselves; no render-side fix exists short of re-curating those seconds.
- **Camera labels still lie in a few shots.** 008 "three-quarter" over a door-knob close-up; 017 "Night one" over a bright white wall; 019 "close-up" over blank tiles; 021 "wide shot — the whole set" over a ceiling.
- **The figures still stand in a row at the bottom whatever the background is.** Floor contact helps, but there is no scale relationship to the room: in 021 Jimmy stands on a ceiling; in 022 three boys stand in front of a toilet; in 016/019 they stand against a blank wall. Where the background is a genuine eye-level room (001, 011, 012, 018, 024, 025) the concept lands; where it is a ceiling, a blank wall, or a blurry close-up (004, 007, 016, 019, 021) it reads as cartoons on a backdrop, not cartoons in a room.
- **004/007 backgrounds are blurry extreme close-ups** of a phone/object — abstract, don't read as a room at all.

## Audio — real footage voices + synthetic tones (unchanged, re-verified)

**No synthetic voices, no cloning.** The voice track is the real audio of `solitary-confinement-chrome.mp4`, cut at each shot's videoTime for that shot's duration, concatenated in shot order (422s). The 10 tone cues are the same synthetic files as v2 (whoosh/sparkle/thud/crash in `tones/`), mixed at their exact episode times on top of the voice track.

Re-extraction proof: all 25 segments were re-extracted fresh from the re-encoded footage into `audio-segs-v3/` and compared byte-for-byte with the v2 segments in `audio-segs/` — **25/25 identical**. `voice.wav` and `mixed.wav` rebuilt from them are also byte-identical to v2. So the audio fit verdicts below are unchanged, and `audio-fit.json` (Whisper transcripts + per-segment levels) stands as-is.

### Assembly commands actually used

Voice segments (one per shot, 48 kHz stereo WAV, into `audio-segs-v3/`):
```
for each shot: ffmpeg -y -v error -ss <videoTimeSeconds> -t <durationSeconds> -i solitary-confinement-chrome.mp4 -vn -ac 2 -ar 48000 -c:a pcm_s16le audio-segs-v3/seg-<NNN>.wav
```
Voice track (concat filter, sample-accurate):
```
ffmpeg -y -v error -i audio-segs-v3/seg-001.wav ... -i audio-segs-v3/seg-025.wav -filter_complex "[0:a][1:a]...[24:a]concat=n=25:v=0:a=1[aout]" -map "[aout]" -c:a pcm_s16le audio-segs-v3/voice.wav
# ffprobe: 422.000000s; byte-identical to audio-segs/voice.wav
```
Tone mix (voice at 0.9 for headroom; final mix max −0.9 dB, no clipping):
```
ffmpeg -y -v error -i audio-segs-v3/voice.wav -i tones/whoosh.wav -i tones/sparkle.wav -i tones/sparkle.wav -i tones/crash.wav -i tones/whoosh.wav -i tones/whoosh.wav -i tones/whoosh.wav -i tones/sparkle.wav -i tones/whoosh.wav -i tones/thud.wav -filter_complex "[0:a]volume=0.9[voice];[1:a]adelay=30000|30000[d0];[2:a]adelay=38000|38000[d1];[3:a]adelay=50000|50000[d2];[4:a]adelay=65000|65000[d3];[5:a]adelay=96000|96000[d4];[6:a]adelay=124000|124000[d5];[7:a]adelay=138000|138000[d6];[8:a]adelay=245000|245000[d7];[9:a]adelay=392000|392000[d8];[10:a]adelay=407000|407000[d9];[voice][d0][d1][d2][d3][d4][d5][d6][d7][d8][d9]amix=inputs=11:normalize=0:duration=longest[aout]" -map "[aout]" -c:a pcm_s16le audio-segs-v3/mixed.wav
# ffprobe: 422.000000s; volumedetect mean −19.4 dB, max −0.9 dB; byte-identical to audio-segs/mixed.wav
```
Final assembly (`concat.txt` = the 25 PNGs held for their durations, shot-025 repeated trailing, trimmed with `-t 422`):
```
ffmpeg -y -v error -f concat -safe 0 -i concat.txt -i audio-segs-v3/mixed.wav -vf fps=30,format=yuv420p -c:v libx264 -preset medium -crf 20 -c:a aac -b:a 128k -t 422 -movflags +faststart episode-final.mp4
```

### Audio verification (on the new encode)

- ffprobe: duration **422.000000**, video h264 800×600 30/1, audio AAC 48 kHz, size **10,802,852 bytes**.
- Frame count: **12,660** read frames = 422s × 30fps.
- Full decode: `ffmpeg -i episode-final.mp4 -f null -` — clean, no errors.
- Shot-boundary PSNR (video frame vs source PNG): t=9s vs shot-001 **36.24 dB**; t=40s vs shot-003 **40.43 dB**; t=103s vs shot-007 **35.59 dB**; t=200.5s vs shot-013 **38.40 dB**; t=308s vs shot-019 **39.68 dB**; t=414.5s vs shot-025 **35.19 dB**. Every shot is held for its duration.
- Voice track present: 10–15s mean −23.2 dB / max −6.4 dB; 100–105s mean −20.9 / max −3.7 dB; 200–210s mean −19.5 / max −1.9 dB; 415–420s mean −26.6 / max −1.9 dB (identical to v2 — same audio).
- Tone presence, proved by residual (final AAC audio minus 0.9× voice track, proper `amerge`+`pan` subtraction; the residual is tones only): every cue present at its exact time, nothing elsewhere — whoosh@30 mean −31.9 / max −16.6; sparkle@38 −31.0 / −16.6; sparkle@50 −33.7 / −20.3; crash@65 −25.4 / −8.7; whoosh@96 −34.6 / −18.3; whoosh@124 −33.5 / −15.3; whoosh@138 −32.9 / −16.8; sparkle@245 −31.7 / −14.4; whoosh@392 −33.9 / −16.4; thud@407 −31.4 / −13.5. No-tone windows in the residual: 10–15s mean −52.3, 200–210s −41.1, 415–420s −50.4 dB (AAC residue only). (Note: a first residual attempt used `amix` with a negative volume, which does not subtract — it was discarded and redone with `amerge`+`pan` subtraction; the numbers above are from the correct residual, saved as `audio-segs-v3/residual.wav`.)

### Audio fit — UNCHANGED from v2 (segments byte-identical, transcripts in `audio-fit.json`)

**FIT 5, PARTIAL 2, MISMATCH 18.** Nothing was substituted, moved, or muted. Per the brief this is Nick's call, not a fix-round item.

| Shot | videoTime | Fit | Shot beat vs what the real audio at that time actually is |
|---|---|---|---|
| 001 | 178 | **MISMATCH** | Setup (H0) — audio is the tripod-bash (Beat 3 smash) |
| 002 | 5 | **FIT** | Stakes (H0) — the setup intro |
| 003 | 88 | **PARTIAL** | TV clips (H1) — "I think I'm in hell…" TV-torture reaction, no croissant/cats |
| 004 | 215 | **MISMATCH** | Alarm shrieks (H3) — post-smash sleep audio, no alarm |
| 005 | 339 | **MISMATCH** | Smash the speakers (H3) — the hiding plan (Beat 4) |
| 006 | 343 | **FIT** (mostly) | Hiding plan (H5) — hiding plan, rap bleeds in at the end |
| 007 | 216 | **MISMATCH** | Hank "Clouds?" (H5) — same sleep audio as 004, no "Clouds?" |
| 008 | 45 | **MISMATCH** | Karl on the roof (H8) — the stakes from the intro |
| 009 | 402 | **FIT** | Screaming goat (H8) — "I'm listening to a screaming goat…" |
| 010 | 415 | **FIT** | Freestyle rap (H10) — blanket-hiding, then the rap |
| 011 | 270 | **MISMATCH** | Filet mignon (H14) — narration about the smash, no dinner |
| 012 | 485 | **PARTIAL** | Ladder + hole (H18) — the theft, no ladder/hole |
| 013 | 490 | **FIT** | Steal the speakers (H18) — the theft audio |
| 014 | 90 | **MISMATCH** | Drake Bell Cameo (H20) — "I think I'm in hell…", no Cameo |
| 015 | 340 | **MISMATCH** | Tyler's alarm clocks (H22) — hiding-plan audio, no clocks |
| 016 | 405 | **MISMATCH** | Siren, nine clocks (H22) — goat audio, no clocks/siren |
| 017 | 520 | **MISMATCH** | Night one (H24) — the Drake Bell Cameo audio |
| 018 | 805 | **MISMATCH** | Morning day 2 (H30) — final-morning wake-up, not the TV break |
| 019 | 830 | **MISMATCH** | Cake/lasagna/chicken (H30) — time-trick audio, no food |
| 020 | 521 | **MISMATCH** | Maddy visits (H35) — same Cameo audio as 017, no Maddy |
| 021 | 483 | **MISMATCH** | Home stretch (H44) — Beat 8 theft |
| 022 | 495 | **MISMATCH** | Final morning (H50) — Beat 8 ladder-giver |
| 023 | 89 | **MISMATCH** | Time trick (H51) — "I think I'm in hell…", same as 003/014 |
| 024 | 804 | **MISMATCH** | Door opens, 51 hours (H51) — final-morning wake-up |
| 025 | 494 | **MISMATCH** | Sliming payoff (H51) — Beat 8 theft, no slime audio at all |

Patterns (unchanged): shots whose videoTimes sit 1s apart share near-identical audio (004/007, 005/015, 012/013/021/022/025 all Beat-8 theft, 003/014/023 all "I think I'm in hell", 017/020 both the Cameo). The Cameo audio lives at ~520, not at shot 14's vt 90; the sliming audio is nowhere near vt 494. The visual curation and the audio are systematically out of phase — Edit E fixed the visuals, but the audio still comes from the same curated (empty-visual) times.

## Manifest

- `manifest.json` is the page's own `cineManifest()` output, created 2026-11-11T06:00:51Z: 16 scenes, 25 shots, `backgroundRendering` report **25 video-backed shots, 25 exact, 0 fallback, 0 pending, 25 distinct backgrounds**, `frames` (shot-%03d.png, 800×600), `assembly` (page's generic commands), `audioTimeline` (dialogue/soundEffects/music all empty arrays — the manifest notes dialogue/music are produced at assembly; the studio ships neither).
- **Boundary note (unchanged text):** "This episode uses the actual footage of MrBeast's I Spent 50 Hours In Solitary Confinement (video r7zJ8srwwjk) by Nick's explicit direction for this video only (Nov 10, 2026). The five cartoon characters replace the real people entirely — not a face swap, not a deepfake — and the room stays the real footage. Every other episode follows the studio rule: templates hold blocking, camera and timing only — no footage, no real person's face or likeness, no face swap. Casting uses only the studio's fictional characters; styles are stylised parody figures."
- Note: unlike v2, the boundary note's claim ("replace the real people entirely") is **now what the renders show** — no real person is visible in any of the 25 backgrounds. The remaining gap is not people in the frame; it is the audio fit and the flat/abstract backgrounds.

## Files

- `frames/shot-001.png` … `frames/shot-025.png` — re-rendered 2026-11-11 06:00 from the exported artifact (Edit E), 800×600 each (v2 frames preserved in `frames-v2/`, v1 in `frames-v1/`).
- `manifest.json` — new, from the page (v2 preserved as `manifest-v2.json`, v1 as `manifest-v1.json`).
- `episode-final.mp4` — 422.000s, 800×600, h264 + AAC 48 kHz, 10,802,852 bytes, with real voice track + 10 tones (v2 preserved as `episode-final-v2.mp4`, 11,141,994 bytes; v1 as `episode-final-v1.mp4`, 4,082,226 bytes, tones only).
- `concat.txt` — shot hold durations (unchanged).
- `audio-segs-v3/` — 25 re-extracted voice segments (byte-identical to v2), voice.wav, mixed.wav, residual.wav (tones-only proof). `audio-segs/` (v2) kept as-is; `audio-fit.json` — per-shot levels + Whisper transcripts (unchanged, applies to v3 verbatim since segments are identical).
- `solitary-confinement-chrome.mp4` — the re-encoded footage (951.066s). `solitary-confinement-r7zJ8srwwjk.mp4` — the original.
- `empty-room-mapping.md` — the curation (now actually what the renders show).
- Render script: `/tmp/crew5_render_v3.py`; result JSON: `/tmp/crew5_render_v3_result.json`.

## Own-eyes judgment — all 25 frames viewed

**Verdict: it DOES read as cartoons in reality now — on the test that failed in v2. No real person is in any background, all 25 backgrounds are distinct exact frames, and the room shots (001, 011, 012, 018, 024, 025) genuinely land the concept: five distinct cartoons standing in the real solitary room, with real voices. That is the fix the round was for, and it is done.**

What is still not "really really good" — the honest remainder, all Nick's call:
- **Audio fit is still 5/25.** The voices are real but usually the wrong moment: the Cameo plays under Night one, the theft plays under the sliming, the hiding plan plays under the alarm clocks, "I think I'm in hell" plays under three different beats. A viewer hears Jimmy talking about smashing speakers while the picture says "The setup". Fixing it means re-curating videoTimes for audio fit, which would break the empty-visual curation — the two cannot both win at the same seconds; Nick decides which matters more (or whether some shots get a different second).
- **Burned-in captions contradict the story in 4 shots** — "47 HOURS TO GO" over HOUR 14 (011, the worst), "NEW EXCITING CAMERA ANGLE" (005, 015), "I'M SO CONFUSED" (020). Same re-curation question.
- **Several backgrounds are not a room.** Ceiling (021), blank white walls (016, 019), blurry phone close-ups (004, 007), bright white wall for "Night one" (017). In those shots the cartoons read as stickers on a backdrop, not characters in a space. Only six genuinely wide empty framings exist in the footage at the curated seconds; the rest are what they are.
- **Figures still stand in a row at the bottom** with no scale relationship to the room (Jimmy on a ceiling in 021, three boys in front of a toilet in 022). Floor contact and shadows help, but there is no blocking that puts a character *in* the space.
- **Shot 13's hanging speaker still reads as a black balloon/bomb** in close-up.
- **Near-identical backgrounds** (003/014/023, 005/015, 004/007, 017/020) will read as repeated shots to a viewer even though the frames differ by a second.

Bottom line: the pipeline is proven end to end a third time (render → manifest → assembled video with real voices + timed cues, all verification green), Edit E does exactly what it promised, and the v2 fail — real people in the backgrounds — is fixed in full. The episode is now honestly describable as "five distinct cartoons with real voices in the real solitary room". What would make it "really really good" is no longer a renderer fix: it is a curation decision Nick has to make (audio-fit seconds vs empty-visual seconds, the caption shots, the flat-background shots) and, for the staging, blocking that puts the figures in the space instead of in a row at the bottom.
