# Snap! on Tofu

Snap! ("Build Your Own Blocks") is the visual, block-based programming language and environment by Jens Mönig and Brian Harvey, maintained at UC Berkeley. You program sprites on a stage by snapping blocks together, and you can build your own blocks, use first-class lists, procedures and continuations, and load libraries for music, 3D, data science and more. The whole IDE runs in the browser. This copy keeps the upstream name, logo, favicon and credits.

## Hosting

- Framework preset: `static`.
- Site root: the repository root (`.`). `index.html` redirects to `snap.html`, which is the IDE.
- Install command: none. There is no root `package.json`. The `package.json` files under `libraries/` belong to vendored libraries and declare nothing for the host to run.
- Build command: none. The upstream source is served as it is.
- Output directory: `.`; serve the repository root unchanged.
- Start command for a local check: `python3 -I -m http.server <port> --bind 127.0.0.1 --directory <checkout>`.
- Environment variables: none.
- Database: none.
- External services and accounts: none required. The upstream Matomo tracker is removed and the Snap!Cloud is switched off (see below), so opening the IDE sends no request to any other site. A few blocks and optional libraries reach other sites only when a user runs them. Examples are the map blocks' tiles from OpenStreetMap or Wikimedia, and a web-service block pointed at a URL the user types. None of them needs a key.
- Saving: with the cloud off, **Save** downloads the project to the computer as an XML file, and **Open…** opens one from the computer or the bundled examples. Snap! also keeps a backup of the unsaved project and the user's settings in this site's browser storage (File menu, "Restore unsaved project").
- Size: GitHub's archive of the pinned commit is about 34 MB with 1,125 entries. After Tofu's import drops the `build/` and `dist/` folders (35 files of the vendored Tone.js MIDI package), 1,090 files remain, about 76 MB unpacked. That is more than Tofu's shared static hosting takes (500 files, 50 MiB), so a copy is served with the `static` preset on Tofu's regular hosting.
- Offline cache: upstream's service worker (`sw.js`) precaches 803 files, about 56 MB, the first time a browser opens the IDE, so the IDE then works offline. About 18 MB of that is `libraries/beetle/babylon.js.map`, a source map only browser developer tools read.

## Adaptations and provenance

- Upstream: https://github.com/jmoenig/Snap
- Upstream branch: `master`.
- Base upstream commit: `9f0770850a74542298bc5d89202d72c31769695c` ("prepared v12.3.5 patch", 2026-10-21), Snap! 12.3.5.
- Fork: https://github.com/tofu-gallery/snap
- `main` mirrors that upstream commit unchanged; `tofu` holds the adaptations, one commit per change with its reason.
- The verified adapted revision is tagged `pin/2026-10-23`.

What changed and why:

1. **`snap.html`: analytics removed, cloud off.** Upstream's page loads a Matomo tracker from `eop8xl.stackhero-network.com` on every visit; gallery copies carry no analytics, so the snippet is gone. The Snap!Cloud at `snap.berkeley.edu` only answers browsers on its own allowed sites: a CORS preflight from another origin gets no `Access-Control-Allow-Origin` header, while `https://snap.berkeley.edu` gets one with credentials. On a Tofu address the cloud's startup request would fail on every load and its sign-in could never work, so the IDE starts with Snap!'s documented `{noCloud: true}` option. A notice at the top of `snap.html` records the change.
2. **Two demo pages removed: `imbw/` and `pyret/`.** `imbw/` is a German school microworld and `pyret/` an "embed Snap! on your page" example. Neither is part of the IDE (nothing in `snap.html`, `src/`, `sw.js` or the library and media indexes loads them), and both carry the same Matomo tracker. `docs/README.md` still links to the Pyret example, which does not exist in this copy; it is in the upstream repository.
3. **Printable manual removed from `help/`.** Tofu's import refuses any file over 25 MiB, and `help/SnapManual.docx` (53 MB) and `help/SnapManual.pdf` (26.6 MB) are both over it. The IDE never loads them: its "Reference manual" menu item opens https://docs.snap.berkeley.edu/, and the PDF stays online at https://snap.berkeley.edu/snap/help/SnapManual.pdf. Removed: `help/SnapManual.docx`, `help/SnapManual.pdf`, `help/SnapManual.md`, `help/manual-LaTeX/` (153 files), `help/sources/` (3 image sources), `help/snap-manual-meta.workflow/`, `help/Frobnicate Snap! Manual.sequ` and `help/Snap Manual Link Dictionary.ald`. `help/README.md` and all 215 block help pictures (`help/*.png`) stay.
4. **Unlisted backgrounds and sounds removed.** After step 3 the archive was still 54.5 MB, over Tofu's 50 MB import limit. The Backgrounds… and Sounds… dialogs offer exactly the files named in `Backgrounds/BACKGROUNDS.json` and `Sounds/SOUNDS.json`, 12 each. The other 91 files in `Backgrounds/` and 229 in `Sounds/` (23.6 MB) are named nowhere else either: not in `sw.js`, `src/`, `snap.html`, the libraries, the examples or the translations. Projects carry their own media, so no saved project needs them. The indexes, every listed file and the whole `Costumes/` library (546 costumes) stay. The removed files are listed at the end of this document.
5. **TuneScope's MIDI helper moved out of a `build/` folder.** Tofu's import drops every folder named `build` or `dist`, at any depth. TuneScope's setup loads `libraries/TuneScope/tonejs/package/build/Midi.js` with `src_load`, which waits for the script to load and has no error path, so in a Tofu copy TuneScope's blocks would wait forever. `Midi.js` (Tone.js MIDI 2.0.28, MIT, licence in that folder's `LICENSE.md`) is copied byte for byte to `libraries/TuneScope/tonejs/package/Midi.js`, and the one `src_load` in `libraries/TuneScope.xml` points there. The upstream `build/` copy stays in the repository.
6. **This `TOFU.md`** was added.

What a copy loses compared with https://snap.berkeley.edu/snap/: the Snap!Cloud (signing in, saving to and opening from the cloud, sharing, and the community site's features), which could not work from another site anyway; the printable manual, which stays online; and the two demo pages. The media removal takes away nothing a user can reach in the IDE.

### Keeping up with upstream

Merging upstream into `tofu` is a plain merge except in these places:

- `snap.html`: a conflict appears only if upstream edits the `new IDE_Morph()` line, the Matomo block or the first two lines. Keep the tracker out and `{noCloud: true}` in.
- Removed files that upstream later edits show as modify/delete conflicts: keep them deleted, unless an index now lists them.
- If upstream adds an entry to `Backgrounds/BACKGROUNDS.json` or `Sounds/SOUNDS.json` that names a removed file, restore that file from `main`.
- If `libraries/TuneScope.xml` changes upstream, keep its `src_load` of `Midi.js` pointing at `libraries/TuneScope/tonejs/package/Midi.js`. Check that no other file the IDE loads sits under a folder named `build` or `dist`.
- Before pinning again, check that GitHub's archive of the commit is at most 50 MB with no file over 25 MiB.

## Verification

The following local checks passed on 2026-10-23 against the `tofu` branch, without deploying anything:

```sh
git -C /private/tmp/gallery-batch2/snap archive --format=zip HEAD | wc -c
```

The ZIP was about 34 MB (cap 50,000,000 bytes). The largest file is `libraries/beetle/babylon.js.map` at 18,160,845 bytes (cap 25 MiB), and 1,090 files (about 76 MB) remain after applying Tofu's import filters (caps 20,000 files and 250 MiB). A script also checked Snap!'s own indexes and references. All 803 entries of the `sw.js` precache list, every file named in `COSTUMES.json`, `BACKGROUNDS.json`, `SOUNDS.json`, `EXAMPLES.json` and `LIBRARIES.json`, and the 59 local `libraries/…` paths named inside libraries and examples exist and survive the import filters. No served page contains an analytics snippet.

To reproduce what a Tofu copy receives, the archive was unpacked into a scratch folder with the `build/` and `dist/` folders removed, then served:

```sh
python3 -I -m http.server 54802 --bind 127.0.0.1 --directory <unpacked archive without build/ and dist/>
curl -s -o /dev/null -w '%{http_code}\n' http://127.0.0.1:54802/            # 200 (index.html)
curl -s -o /dev/null -w '%{http_code}\n' http://127.0.0.1:54802/snap.html   # 200
curl -s -o /dev/null -w '%{http_code}\n' http://127.0.0.1:54802/libraries/TuneScope/tonejs/package/Midi.js   # 200
```

Every URL in the `sw.js` precache list (803) and every TuneScope `src_load` URL (39) answered 200.

A headless Chrome run, driven over the DevTools protocol, then opened `http://127.0.0.1:54802/` and checked the following:

- The root redirected to `/snap.html`, titled "Snap! 12.3.5".
- The IDE opened with the cloud disabled and no cloud button.
- A `round (3.7)` block evaluated to 4 through Snap!'s interpreter.
- The media and library dialogs' lists loaded: 12 backgrounds, 12 sounds, 546 costumes, 10 examples and 88 libraries.
- The service worker installed and took control.
- All 39 TuneScope setup scripts loaded in order, including the moved `Midi.js`. Afterwards the `Midi` global and TuneScope's 14 extension primitives existed.
- All 75 requests went to the local server, none failed, and no request went to another site.
- The only console output was Chrome's performance hint about `getImageData`, raised by upstream's unchanged `src/morphic.js`.

## License

Snap! is free software under the GNU Affero General Public License v3.0, copyright (C) 2008-2026 Jens Mönig and Brian Harvey, with contributions credited in the source files. The upstream `LICENSE` file and all copyright notices are preserved; libraries and assets keep their own licences and notices.

Modification notices (AGPL-3.0 section 5(a)), dated 2026-10-23:

- `snap.html` was modified as described in step 1. It carries its own notice at the top.
- `libraries/TuneScope.xml` was modified as described in step 5: one `src_load` path changed. Snap!'s XML reader has no comment syntax, so this notice stands here instead of in the file.
- Added: `TOFU.md`, and `libraries/TuneScope/tonejs/package/Midi.js`, an unchanged copy of the MIT-licensed `build/Midi.js`.
- Removed: the files listed in steps 2 to 4.

A copy serves Snap!'s unminified source files as they are. The complete corresponding source of this modified version is this fork at the tag `pin/2026-10-23`. The IDE's "Download source" menu item still opens upstream's latest release.

## Files removed in step 4

Backgrounds (91, none listed in `Backgrounds/BACKGROUNDS.json`): `baseball-field.png`, `basketball-court1-a.png`, `basketball-court1-b.png`, `beach_malibu.jpg`, `beach_rio.png`, `bench_with_view.jpg`, `blue_sky.svg`, `blue_sky2.svg`, `blue_sky3.svg`, `boardwalk.jpg`, `brick_wall1.jpg`, `brick_wall2.jpg`, `building_at_mit.jpg`, `canyon.jpg`, `castle1.png`, `castle2.png`, `castle3.png`, `castle4.png`, `castle5.jpg`, `chalkboard.jpg`, `circles.svg`, `city_with_water.jpg`, `city_with_water2.jpg`, `clothing_store.jpg`, `doily.svg`, `driveway.jpg`, `flower_bed.jpg`, `flowers.gif`, `forest.jpg`, `garden_rock.jpg`, `gingerbread.png`, `goal1.png`, `goal2.png`, `graffiti.jpg`, `grand_canyon.jpg`, `gravel_desert.jpg`, `greek_theater.png`, `hall.jpg`, `hallway_outdoors.jpg`, `hay_field.jpg`, `hearts1.svg`, `hearts2.svg`, `hill.jpg`, `houses.jpg`, `kitchen.jpg`, `lake.jpg`, `light.svg`, `metro1.png`, `moon.jpg`, `neon_tunnel.svg`, `night_city.gif`, `parking-ramp.jpg`, `party.svg`, `playing-field.png`, `pool.jpg`, `purple.svg`, `rays.svg`, `room1.jpg`, `room2.jpg`, `room3.jpg`, `route66.jpg`, `school1.gif`, `school2.jpg`, `slopes.svg`, `space.svg`, `sparkling.svg`, `spotlight-stage.jpg`, `spotlight-stage2.svg`, `stage1.png`, `stage2.png`, `stars.gif`, `stripes.svg`, `the_movies_inside.jpg`, `the_movies_outside.jpg`, `track.svg`, `train_tracks1.jpg`, `train_tracks2.gif`, `tree-gifts.png`, `tree.jpg`, `underwater1.png`, `underwater2.png`, `underwater3.gif`, `urban1.png`, `urban2.png`, `village.jpg`, `water_and_rocks.jpg`, `wave.jpg`, `winter-lights.svg`, `winter.svg`, `woods.gif`, `woods_and_bench.jpg`

Sounds (229, none listed in `Sounds/SOUNDS.json`): `A_bass.wav`, `A_elec_bass.wav`, `A_elec_guitar.wav`, `A_elec_piano.wav`, `A_guitar.wav`, `A_minor_ukulele.wav`, `A_piano.wav`, `A_sax.wav`, `A_trombone.wav`, `A_trumpet.wav`, `B_bass.wav`, `B_elec_bass.wav`, `B_elec_guitar.wav`, `B_elec_piano.wav`, `B_guitar.wav`, `B_piano.wav`, `B_sax.wav`, `B_trombone.wav`, `B_trumpet.wav`, `C2_bass.wav`, `C2_elec_bass.wav`, `C2_elec_guitar.wav`, `C2_elec_piano.wav`, `C2_guitar.wav`, `C2_piano.wav`, `C2_sax.wav`, `C2_trombone.wav`, `C2_trumpet.wav`, `C_bass.wav`, `C_elec_bass.wav`, `C_elec_guitar.wav`, `C_elec_piano.wav`, `C_guitar.wav`, `C_major_ukulele.wav`, `C_piano.wav`, `C_sax.wav`, `C_trombone.wav`, `C_trumpet.wav`, `D_bass.wav`, `D_elec_bass.wav`, `D_elec_guitar.wav`, `D_elec_piano.wav`, `D_guitar.wav`, `D_piano.wav`, `D_sax.wav`, `D_trombone.wav`, `D_trumpet.wav`, `E_bass.wav`, `E_elec_bass.wav`, `E_elec_guitar.wav`, `E_elec_piano.wav`, `E_guitar.wav`, `E_piano.wav`, `E_sax.wav`, `E_trombone.wav`, `E_trumpet.wav`, `F_bass.wav`, `F_elec_bass.wav`, `F_elec_guitar.wav`, `F_elec_piano.wav`, `F_guitar.wav`, `F_major_ukulele.wav`, `F_piano.wav`, `F_sax.wav`, `F_trombone.wav`, `F_trumpet.wav`, `G_bass.wav`, `G_elec_bass.wav`, `G_elec_guitar.wav`, `G_elec_piano.wav`, `G_guitar.wav`, `G_piano.wav`, `G_sax.wav`, `G_trombone.wav`, `G_trumpet.wav`, `G_ukulele.wav`, `afro_string.wav`, `alien_creak1.wav`, `alien_creak2.wav`, `bass_beatbox.wav`, `beat_box1.wav`, `beat_box2.wav`, `bell_cymbal.wav`, `bell_toll.wav`, `bird.wav`, `birthday.wav`, `birthday_bells.wav`, `boing.wav`, `bubbles.wav`, `buzz_whir.wav`, `car_passing.wav`, `cave.wav`, `chee_chee.wav`, `cheer.wav`, `chomp.wav`, `clap_beatbox.wav`, `clapping.wav`, `computer_beeps1.wav`, `computer_beeps2.wav`, `cough-female.wav`, `cough-male.wav`, `crash_beatbox.wav`, `crash_cymbal.wav`, `cricket.wav`, `crickets.wav`, `cymbal.wav`, `cymbal_crash.wav`, `cymbal_echo.wav`, `dance_around.wav`, `dance_celebrate.wav`, `dance_chill_out.wav`, `dance_funky.wav`, `dance_head_nod.wav`, `dance_magic.wav`, `dance_slow_mo.wav`, `dance_snare_beat.wav`, `dance_space.wav`, `door_creak.wav`, `drip_drop.wav`, `drive_around.wav`, `drum.wav`, `drum_bass1.wav`, `drum_bass2.wav`, `drum_bass3.wav`, `drum_buzz.wav`, `drum_funky.wav`, `drum_jam.wav`, `drum_machine.wav`, `drum_satellite.wav`, `drum_set1.wav`, `drum_set2.wav`, `duck.wav`, `eggs.wav`, `elec_piano_A_minor.wav`, `elec_piano_C_major.wav`, `elec_piano_F_major.wav`, `elec_piano_G_major.wav`, `elec_piano_loop.wav`, `fairydust.wav`, `finger_snap.wav`, `flam_snare.wav`, `footsteps-1.wav`, `garden.wav`, `gong.wav`, `goose.wav`, `guitar_chords1.wav`, `guitar_chords2.wav`, `guitar_strum.wav`, `hand_clap.wav`, `hey.wav`, `hi_beatbox.wav`, `hi_na_tabla.wav`, `hi_tun_tabla.wav`, `high_conga.wav`, `high_hat.wav`, `high_tom.wav`, `hihat_cymbal.wav`, `hip_hop.wav`, `horse.wav`, `horse_gallop.wav`, `human_beatbox1.wav`, `human_beatbox2.wav`, `jungle.wav`, `kick_back.wav`, `large_cowbell.wav`, `laser1.wav`, `laser2.wav`, `lo_geh_tabla.wav`, `lo_gliss_tabla.wav`, `low_conga.wav`, `low_tom.wav`, `medieval1.wav`, `medieval2.wav`, `meow2.wav`, `motorcycle_passing.wav`, `muted_conga.wav`, `odesong-b.wav`, `owl.wav`, `party_noise.wav`, `plunge.wav`, `rattle.wav`, `ride_cymbal.wav`, `ripples.wav`, `roll_cymbal.wav`, `rooster.wav`, `scratch_beatbox.wav`, `scratchy_beat.wav`, `scream-female.wav`, `scream-male1.wav`, `screech.wav`, `shaker.wav`, `sidestick_snare.wav`, `singer1.wav`, `singer2.wav`, `small_cowbell.wav`, `snap.wav`, `snare_beatbox.wav`, `snare_beatbox2.wav`, `snare_drum.wav`, `sneeze-female.wav`, `sneeze-male.wav`, `space_ripple.wav`, `spiral.wav`, `splash_cymbal.wav`, `spooky_string.wav`, `squawk.wav`, `string_accent.wav`, `string_pluck.wav`, `suspense.wav`, `tambura.wav`, `tap_conga.wav`, `tap_snare.wav`, `techno.wav`, `techno2.wav`, `tom_drum.wav`, `triumph.wav`, `trumpet1.wav`, `trumpet2.wav`, `wah_beatbox.wav`, `water_drop.wav`, `whoop.wav`, `wolf_howl.wav`, `wub_beatbox.wav`, `xylo1.wav`, `xylo2.wav`, `xylo3.wav`, `xylo4.wav`, `ya.wav`, `zoop.wav`
