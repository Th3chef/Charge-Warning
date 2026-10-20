# Charge Warning

*(formerly Charge Warning & Auto Unsafe, before that Railgun Warning & Auto Unsafe and Railgun Overcharge Warning)* - a Helldivers 2 mod for the RS-422 Railgun and the PLAS-45 Epoch.

![Charge Warning](media/header.png)

Charging the Railgun in unsafe mode or the Epoch is a guessing game: fire too early and you lose damage, hold too long and it explodes in your hands. This mod tells you exactly when the charge does full damage. It is sound only: no scripts, nothing to install besides the mod itself.

## Features

- While you charge, a low, growling electric surge with building crackle plays over the charge-up.
- Fast **warning beeps start exactly when the charge does full damage**: at 90% on the **RS-422 Railgun** (2.5 s into an unsafe charge) and at full charge on the **PLAS-45 Epoch** (2.6 s, 2x damage, before it overloads at 3.25 s). **Fire on the beeps.** A sharp discharge snap after them tells you you're past the sweet spot.
- **Silent on quick shots**: it stops as soon as you fire or let go. The Railgun's warning stays **silent in safe mode** (it follows the game's own charge value).
- **Each weapon's warning is its own option**: turn either off, and pick Loud (default), Medium (-8 dB) or Quiet (-16 dB) for each.
- Mixed to stay clear over each weapon's own charge-up. Every other sound stays exactly as the game has it.

![Gallery](media/gallery.png)

## Options

In your mod manager (Arsenal / HD2 Mod Manager), open the mod's options (Arsenal: the sliders button on the mod's row):

| Option | | What it does |
|---|---|---|
| ![](media/options/railgun.png) | **Railgun warning** (on by default) | The RS-422 Railgun's warning in unsafe mode. Pick Loud (default), Medium (-8 dB) or Quiet (-16 dB); untick it to turn it off. |
| ![](media/options/epoch.png) | **Epoch warning** (on by default) | The PLAS-45 Epoch's warning at full charge. Pick Loud (default), Medium (-8 dB) or Quiet (-16 dB); untick it to turn it off. |

## Requirements

None. No Bingus Shared Loader or other script loader needed.

## Download

Get **Charge-Warning-3.1.0.zip** from the [latest release](https://github.com/Th3chef/Charge-Warning/releases/latest) (not the "Source code" archives). Also on [Nexus Mods](https://www.nexusmods.com/helldivers2/mods/16754) and AyakaMods.

## Install / update

1. Mod manager (Arsenal / HD2 Mod Manager): add the zip (it installs over Charge Warning & Auto Unsafe and Railgun Warning & Auto Unsafe) and enable it, then **Purge** and **Deploy**. Updating from a version with Auto unsafe mode: Purge and Deploy removes it; nothing else to do.
2. Manual: copy the three `9ba626afa44a3aa3.patch_0` files from one `Railgun\<level>` folder and/or one `Epoch\<level>` folder into `Helldivers 2\data`, renaming each set to the next free patch number if you already have patch files with that name.

## Uninstall

Disable it in your mod manager, then Purge and Deploy, or delete the patch files you copied.

## Compatibility

- Conflicts with any other mod that replaces the Railgun's sounds (`content/audio/wep_railgun`) or the Epoch's sounds (`content/audio/wep_plasma_blaster`), including other sound replacers and the older "Railgun Max charge warning sound". Only one of them can be active per weapon; turn this mod's warning off for that weapon instead.
- Built on the game's current sounds for both weapons (Devoid of Liberty 7.2.0, October 2026). Patch-proof by design: it only adds to each weapon's own sounds, so after a game patch it keeps working; if a patch changes a weapon's audio, an update is one rebuild.
- Client-side, sound only. Works online and offline, hosting or joining. It contains no scripts and changes no game code or memory.

## Known limitations

- The timing assumes each weapon's normal charge (Railgun: 90% after 2.5 seconds in unsafe mode; Epoch: full charge after 2.6 seconds). If the game changes a charge speed, the beeps will be early or late until an update.
- The Epoch has its own fast beeping near full charge; this mod's warning plays on top of it and marks the full-damage moment.
- The warnings are part of each weapon's own sounds, so you may also hear them from other players' Railguns and Epochs.
- The Railgun's surge stays silent until the charge is past what safe mode can reach, so its first moments may be quiet or cut.

## How it works

Each warning is one sound added to that weapon's own sound bank. It starts with the charge-up, delayed so its first warning beep lands on the full-damage moment, and its volume follows the game's charge value. Releasing the trigger or firing stops it. It doesn't change any stats, damage or gameplay, only what you hear.

Auto unsafe mode (2.0.0 to 3.0.1) was removed in 3.1.0: since the 7.2.0 game patch the game remembers each support weapon's fire mode, so your Railgun keeps Unsafe by itself. Its code is in the [v3.0.1 tag](https://github.com/Th3chef/Charge-Warning/tree/v3.0.1).

## Troubleshooting

- No warning at all: check that no other sound mod for that weapon is enabled, that the weapon's warning option is ticked, that you Purged and Deployed after installing, and (Railgun) that you are in unsafe mode (safe mode is silent on purpose).
- Weapon sounds missing after a game patch: disable the mod and check for an update.
- The mod writes no log. For a [bug report](https://github.com/Th3chef/Charge-Warning/issues/new/choose), say which options you use, your game version and any other sound mods you have.

## Building from source

Everything in `src/` builds the release zip from your own game install (see [src/BUILDING.md](src/BUILDING.md)):

```
cd src/tools
python sound.py      # the warning sound (numpy, scipy, pyloudnorm)
python art.py        # art and option icons (pillow, playwright)
python update.py --hd2raw <hd2raw> --gamedir <Helldivers 2 folder>      # extracts both weapons' sound banks and builds
python update.py --filediver <filediver> --gamedir <Helldivers 2 folder>   # the same with the Filediver CLI
python build.py      # the mod zip, checked by verify.py; needs the game's sound banks in src/game
```

Nothing from the game is stored here: the build reads each weapon's sound bank from your install.

## Credits

Inspired by [Railgun Max charge warning sound](https://www.nexusmods.com/helldivers2/mods/481) by uskummel. This is a new mod built from scratch: new sound, new timing logic, no files from the original.

## License

Copyright (c) 2026 Th3chef. All rights reserved. You may read the source, report bugs and suggest fixes; ask before reusing it or reuploading the mod. See [LICENSE](LICENSE).
