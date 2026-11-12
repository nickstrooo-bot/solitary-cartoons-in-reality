# I Spent 50 Hours In Solitary Confinement — Cartoons in Reality

MrBeast's "I Spent 50 Hours In Solitary Confinement" (May 30, 2020) re-staged with
cartoon characters standing in the real footage. Jimmy, Chris, Chandler, Karl, and
Tyler are drawn as cartoons and dropped into the actual room — the backgrounds are
real frames from the video, the voices are the real people speaking in the footage,
and the cartoon figures perform the real people's movements (the choreography of
each scene, matched to the drawn figures).

This is a parody / transformative re-staging. The cartoon characters replace the
real people entirely — no face swaps, no deepfakes. The video in this repo is the
current cut.

## What's in this repo

- `solitary-cartoons-in-reality.mp4` — the current cut (7m 2s, 800x600, 25 shots).
  This is the **stills version**: the figures are posed in each shot but do not move
  within the shot. It is 25/25 clean — no real people visible in any background.
- `manifest.json` — the episode: 16 scenes, 25 shots, timings, cast, props, sound cues.
- `choreography.md` — the per-shot choreography: what each real person does in the
  footage at each shot's moment, and how it maps onto the cartoon figures, plus the
  workarounds built for movements the figure system could not do at first.
- `NOTES.md` — the build log (v1 through v7): what was built, what was verified,
  and what is still open.
- `audio-fit.json` — how well the real voice audio at each shot's background time
  matches the story beat (FIT 5 / PARTIAL 1 / MISMATCH 19).

## What's done

- 25/25 backgrounds are verified empty-room frames — no real people visible.
- Choreography: 185 moves across 25 shots (arm raises, leans, crouches, lying down).
- Workarounds built for the hard movements: walking, a head poking upside-down
  through a ceiling hole, a sheet over a lying figure, a tongue-out close-up,
  laughing, hand-held phone/speaker/bag/ball props, and a door that swings open
  by itself.
- The figures' motion (bob, talking mouth, choreography, workarounds) is built and
  verified in the tool that renders the shots.

## Help wanted

The moving version — figures actually performing the choreography on screen — is
blocked. If you can help with any of these, please open an issue or a pull request.

1. **Clip download (the blocker).** The tool records each shot as a WebM clip in
   the browser (MediaRecorder + canvas.captureStream). The recording works — the
   blob is real data (~2.4 MB for an 18s clip). But the file never reaches the
   disk. The download is triggered by a programmatic anchor click on a blob URL.
   PNG stills download fine through the same helper; WebM clips do not, even
   after: a 30-second revoke delay (was 400 ms), an application/octet-stream
   MIME type (was video/webm), and a two-step flow (record stores the blob,
   a separate Download button triggers the download in a user-gesture context).
   The download never surfaces — no download bar, shelf, notification, or file.
   If you know why a blob-URL anchor download works for a PNG but not for a
   WebM in a cloud/headless Chromium, or you know a reliable way to get a
   MediaRecorder blob out of the page, that unblocks the whole moving version.
2. **TV prop (shot 14).** Jimmy carries a TV in shot 14 (t=9–12). He holds the
   carrying pose but the TV object itself is not drawn. A hand-held TV prop
   would close it.
3. **White object (shot 09).** Karl holds a white object while laughing
   (t=12–14). It is not rendered because nobody has identified what it is.
   Identifying it from the footage, or drawing a reasonable prop, would close it.
4. **Dangling leg (shot 09).** A leg dangles beside Karl's head while it pokes
   through the ceiling hole (t=6–9). Only the head is rendered. Drawing the leg
   would close it.
5. **Hatch head identity (shot 16).** A head peers down through the hatch at
   t=3, staged as Karl. That is a guess, not a confirmed identification — Karl
   is the roof person in this beat but is not in shot 16's cast. Confirming who
   it is from the footage would settle it.
6. **Audio fit (19 of 25 shots).** The background times were picked for empty
   rooms, so the real voice audio at those seconds often does not match the
   story beat (FIT 5 / PARTIAL 1 / MISMATCH 19). Re-curating times for visual
   emptiness AND audio fit, or taking the audio from the beat's actual moment,
   would close it. See `audio-fit.json`.
7. **Burned-in captions.** The footage has burned-in captions that contradict
   the story in 4 shots (worst: shot 011 reads "47 HOURS TO GO" over a HOUR 14
   timer). Covering or working around them would help.

## Background

Built with a hand-drawing studio tool (not in this repo). The episode data and
choreography here are the portable part — the tool that renders the shots is a
separate private project. The five cartoon characters (Jimmy, Chris, Chandler,
Karl, Tyler) are original stylised caricatures. The voices are the real audio
from MrBeast's video at each shot's moment, used under the creator's direction
for this parody re-staging only.

## License

The episode data (manifest, choreography, notes) is shared for collaboration.
The video is a parody re-staging of MrBeast's footage; the footage itself is
not included in this repo and remains MrBeast's.
