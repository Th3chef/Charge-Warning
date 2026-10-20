# Charge Warning - source (formerly Charge Warning & Auto Unsafe, Railgun Warning & Auto Unsafe and Railgun Overcharge Warning)

Builds the mod from the game's own weapon sound banks (RS-422 Railgun and PLAS-45 Epoch). Nothing in here comes from
another mod. Since 3.1.0 the mod is sound banks only: no Lua, no Bingus Shared Loader.

    tools/sound.py     the warning sound (synthesized; writes assets/warning.wav, the released take, and
                       assets/warning.json, the cue: when the first warning beep starts inside the sound)
    tools/art.py       thumbnail, Nexus header, gallery, GitHub social preview, AyakaMods cover and Arsenal option icons, drawn from the
                       sound and its cue (Playwright Chromium, Anton + Barlow Condensed in art/fonts)
    tools/wwise_bank.py  small reader/writer for the game's Wwise banks
    tools/build.py     adds a warning to each weapon's bank and writes build/Charge-Warning-<ver>.zip
    tools/discover.py  finds each bank's charge event, release event, shot event, charge value and mixer by what
                       they do (any game version)
    tools/verify.py    offline check of a built zip against the game banks, and that the zip holds nothing else
                       (build.py runs it on every zip)
    tools/update.py    after a game patch: re-extracts both banks and deps and rebuilds only if a weapon's audio changed

## The weapons (build.py WEAPONS)
| weapon | bank | first beep | volume gate (charge value) | new object ids |
|---|---|---|---|---|
| RS-422 Railgun | content/audio/wep_railgun | 2.5 s (90%, full damage in unsafe mode) | silent to 0.75, full from 0.76 (safe mode holds ~0.70) | chef_railgun_charge_warning_* |
| PLAS-45 Epoch | content/audio/wep_plasma_blaster | 2.6 s (full charge, 2x damage; overloads at 3.25 s) | silent to 0.55, full from 0.57 (a safety gate only) | chef_epoch_charge_warning_* |

Both banks use the same charge value (RTPC 0xBFDCF9F2, 0..1). Each bank gets one new sound, one Play on the
charge event (delayed so the sound's cue lands on the first-beep time) and Stops on the release event, the shot event
(found by its name, wep_<bank>_fire) and any other event that cuts the charge sounds (the Epoch has one).
Retune by hand only if the game changes a weapon's charge: end_at_s and the gate in WEAPONS.

## Builds
    python build.py            the release (live GUID kept from Railgun Overcharge Warning, so Arsenal updates in place)
    python build.py test N     numbered test build ("<version> Test N", its own GUID; installs next to the release)
No log, so no Tester zip.

## Auto unsafe mode (removed in 3.1.0)
2.0.0 to 3.0.1 had an "Auto unsafe mode" option (a Bingus Shared Loader Lua addon that switched every new Railgun to
Unsafe). Since the 7.2.0 game patch the game remembers each support weapon's fire mode across uses and sessions, so
the option was dropped; its code stays in the repo's history (tag v3.0.1).

## Volume options
Each weapon has its own Arsenal option (on/off) with pick-one SubOptions Loud / Medium / Quiet (folders
<Weapon>/<level>); the levels differ only in the Sound's voice volume (prop 0x05, VOICE_VOLUME_DB + offset). Each
level ships a full copy of that weapon's bank, because that is how the game loads audio.

## After a game patch (one command)
    cd tools
    python update.py --filediver <path to filediver> --gamedir <Helldivers 2 folder that contains "data">
    python update.py --hd2raw <path to hd2raw> --gamedir ...    (3.1.0; hd2raw is in Better Bubble Shields' kit,
                                                                 tools/hd2raw: faster, no stall over slow links)

- Unchanged audio for both weapons (bank and dep): "no update needed" (exit 0).
- A weapon's audio changed: it extracts the game's new bank(s), re-finds every hook by what it does, rebuilds
  build/Charge-Warning-<ver>.zip (checked by verify.py) and lists anything that moved (exit 2).
  `--version 3.1.1` sets the new version number.
- Exit 1: Filediver failed, or a bank changed too much to hook automatically (or the Wwise version changed).

What the mod adds is purely additive (one sound and its Play/Stop actions appended to existing events), so it never
edits or removes anything the game has; an outdated copy only lacks the game's newest sounds for that weapon.

## Building by hand
    cd tools && python sound.py && python art.py && python build.py
The game banks go in game/ (update.py fetches them; or extract content/audio/wep_railgun.wwise_* and
content/audio/wep_plasma_blaster.wwise_* with Filediver using --audio-format raw --raw-format separate).
game/built_from.json records which game banks and deps the build used (md5) and every hook it found. Set RCW_GAME_DIR
to use another folder for the game files (build.py and update.py both follow it). The zips are byte-reproducible: the
same game files, assets and art give the same zip, byte for byte.

Needs: python 3 (build/update/verify: standard library only; sound.py: numpy, scipy, pyloudnorm; art.py: pillow,
numpy, playwright).
