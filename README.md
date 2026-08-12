# Dual Wield Block VR - Blocking State Consistency Fix

This branch is the source for an **unofficial beta DLL patch** for
[Dual Wield Block VR 1.7.0](https://www.nexusmods.com/skyrimspecialedition/mods/28456).
It is based on FlyingParticle's GPL-3.0 source and the proposed upstream fix in
[pull request #1](https://github.com/adamhynek/dual_wield_block_vr/pull/1).

The original mod remains required. The distribution archive contains only a replacement
`DualWieldBlockVR.dll` (and matching debug symbols); it deliberately does not redistribute the
original INI or install as a standalone copy. Install the original mod first and give this patch
higher file priority.

FlyingParticle has acknowledged the bug report and intends to investigate it. This build is an
interim upstream candidate and should be replaced by an official release when one becomes
available.

## Problem addressed

Dual Wield Block VR can leave the player's animation graph in a persistent state where
`IsBlocking=true` while Skyrim's actor state reports `wantBlocking=false`. The mismatch can be
saved, survive a full restart, suppress right-hand spell casting, and interfere with stamina
regeneration or other actions that require the hand not to be blocking.

The patch adds state ownership and completion tracking around the existing `blockStart` and
`blockStop` animation events, retries incomplete stop transitions with bounded backoff, and
repairs only a stable post-load mismatch after a conservative observation period.

## Installation and migration

1. Install [Dual Wield Block VR 1.7.0](https://www.nexusmods.com/skyrimspecialedition/mods/28456).
2. Completely uninstall version 0.1.0 of **Blocking State Repair VR**, if present. In particular,
   ensure `BlockingStateRepairVR.dll`, `.ini`, and `.pdb` are no longer installed.
3. Install this patch after the original mod so its `DualWieldBlockVR.dll` wins the conflict.
4. Keep the original `DualWieldBlockVR.ini`; this patch does not supply or change it.

Do not run the old load-only repair DLL and the full replacement together.

## Status and testing

The known stale save was repaired successfully, right-hand casting resumed, and a save made after
repair remained healthy after a complete restart. Broader combinations such as shield mode,
left-handed mode, unarmed blocking, and interrupted menu/equipment/cell transitions still require
community testing. This is therefore published as a beta rather than as a final release.

Please report regressions with the SKSE log and, for crashes, the crash log. Reports should include
equipment in both hands, left-handed-mode state, shield option state, and the transition preceding
the problem.

## Requires

- Skyrim VR 1.4.15
- SKSE VR 2.0.12 or later
- Dual Wield Block VR 1.7.0

## Source and licence

Original source and mod: Copyright FlyingParticle / adamhynek.

State-consistency changes: Treatid2, 2026, developed from the documented diagnosis and submitted
upstream in pull request #1.

This modified build is distributed under the GNU General Public License, version 3. The complete
corresponding source is this branch and its tagged release. The unchanged licence text is in
[`LICENSE`](LICENSE).

## Credits

Thanks to FlyingParticle for Dual Wield Block VR and for maintaining PLANCK and related Skyrim VR
work, and to Shizof and frazaman for the original reverse-engineering assistance credited upstream.
