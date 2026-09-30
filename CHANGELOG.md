# Changelog

The current version is shown in the menu under Settings, and in the notification when the script loads.

**Numbering:** each new feature or fix gets the next `1.0.x` number. If it takes more than one try to get right, the follow-ups get a letter: `1.0.3`, `1.0.3b`, `1.0.3c` and so on. The next feature moves on to `1.0.4`.

Versions up to 1.0.4b were renumbered to this scheme. Their commit messages still use the old numbers, shown in brackets below.

## 1.0.7b - 2026-09-30

- Fixed the menu not showing ("menu failed to start"). Matcha drops the value the JDUI loadstring returns, so the script now picks the menu up from `_G.JDUI` instead.

## 1.0.7 - 2026-09-30

- The menu is now loaded from [JDUI](https://github.com/jdev-studio/jdui) instead of being copied into the script, so menu fixes reach this script straight away. The script is about 700 lines shorter.
- JDUI is also a newer version of the menu than the old built-in copy: it only touches drawings that actually changed each frame, which is lighter on Matcha.
- If the menu can't be downloaded the script still runs; the F1 / F2 / F4 hotkeys work without it.

## 1.0.6b - 2026-09-29

- Performance: Matcha dropped to about 1 FPS while fishing. The 1.0.5c lock writer busy-looped for 10 ms between every yield, which starves an external executor. It's removed, along with the extra Stepped connection; the lock now writes from the main update and a single Heartbeat connection.
- The main update runs at most 100 times a second while working and 20 when idle (was 250 and about 70), hotkeys are polled 20 times a second, the Fishfolk scan runs once a second and reads names before anything else, reel stats read the screen every 0.15 s, and the rod lookup at the start of a fight only searches the hotbar GUI.

## 1.0.6 - 2026-09-29

- Fishfolk fights now carry on until the Fishfolk is dead (removed, marked Dead or at 0 health), with a 3 minute safety limit. They used the chicken farm's give-up rule, which ended the fight after 6 seconds without getting closer and then restarted it 30 seconds later.
- At the start of a fight the script notes the rod you're holding and finds its hotbar number, switches to the Weapon slot (or puts the rod away and fights with fists if no weapon is set), and switches back to the rod afterwards before carrying on fishing.
- The Auto equip rod toggle and Rod slot setting are removed.

## 1.0.5c - 2026-09-29

- Tracking is removed; Lock fish is the only reel method and is always on (the toggle is gone).
- The lock offsets are checked once per session instead of every reel. That check kept failing while the fish moved, so the fish ran free at the start of each reel; now later reels lock from the first frame.
- Between yields the lock rewrites the fish position continuously for about 10 ms, on top of every render, physics and heartbeat step, so the game's own animation is overwritten almost every frame.
- The fish is centred using its on-screen width, and the lock writer is lighter so it can run many more times per frame.

## 1.0.5b - 2026-09-29

- Lock fish is back (Fish tab, on by default) and works like the original script: no clicking during the reel, so the bar rests at the far left, and the fish is written onto the bar's position on every render, physics and heartbeat step plus its own loop. In 1.0.4 tracking kept running while locked, which kept the bar moving and made the lock look worse than it was. If the lock can't engage at all in a reel it says so and tracks for that reel.
- Tracking (Lock fish off) ignores readings that jump much further than normal for up to 3 frames and keeps steering on its prediction, and caps bar speed, fish speed and the learned bar acceleration, so a single bad frame can't send the bar off in the wrong direction.
- The reel summary shows how much of the reel the fish was on the bar.

## 1.0.5 - 2026-09-29

- New reel tracking. The old version worked from raw frame-to-frame speed readings, which are very noisy at low frame rates, and didn't account for its own input delay, so it overshot and swung around the fish. It now smooths the bar and fish positions using the input it knows it's sending, learns how fast the bar speeds up when held and when released, predicts where the bar will be once the next click takes effect, and brakes relative to the fish's own speed. In a simulation at 22 fps the fish stayed in the bar 78% of the time instead of 68%, and the worst case went from 38% to 64%.
- The re-press check during reeling is off. It read memory offsets that may not match the game and could release and re-press the mouse at random.
- Rod detection reads the game's HeldItemName instead of looking for a Roblox Tool, so the false "Wrong tool equipped (nothing)" message is gone.
- The console logging no longer touches the executor's notify function.

## 1.0.4e - 2026-09-29

- Lock fish is removed and every reel uses click tracking. Testing showed the game works out catch progress from its own values, not the on-screen positions: holding the bar on the fish lost 4 out of 4 reels at 1-3% progress, while tracking alone caught at 99%. Writing the positions only moved the picture and confused the tracking, which reads the bar and fish from the screen.
- Reloading the script no longer doubles every console line.

## 1.0.4d - 2026-09-29

- Lock fish test is more accurate. It used to take one reading some time after the nudge, which could land 300+ ms late while the bar or fish had already moved on its own. It now measures each part's speed first, then reads every frame for 0.15 s after the nudge and counts whether the game kept the change or put it back.
- It tests the bar first. If the game follows the bar, the bar is held on the fish every frame, which works however the fish is animated. Moving the fish is only used if the bar doesn't stick, since the game overwrites the fish while animating it (about 80% on the bar in testing).

## 1.0.4c - 2026-09-29

- Every message the script shows is also printed to the executor console (starting with `[Grand Blue]`) and saved to `grandblue_log.txt`, so it can be copied in full.
- The Lock fish test prints its raw numbers, and each reel ends with a one-line summary: caught or lost, final progress, lock mode and how much of the reel the fish stayed on the bar.

## 1.0.4b - 2026-09-29 (was 1.1.1)

- Lock fish now tests which position the game actually reads. Early in the first reel it nudges the fish, then the bar, and checks whether the game keeps the change. It then holds whichever one the game follows on top of the other, so both sit at the same spot. If neither sticks it falls back to tracking and tries the test again on the next two reels. A notification says which one it found.

## 1.0.4 - 2026-09-29 (was 1.1.0)

- Lock fish: the fish position was only written once per script update and the mouse was let go while locked, so when the game moved the fish in between, it slipped away and nothing brought the bar back. The position is now rewritten continuously (every render, physics and heartbeat step plus its own loop), and the bar keeps tracking the fish as a backup while locked.
- The check that the lock offsets are right is less strict and gets 40 tries per reel instead of 15.
- After each reel a notification shows how much of the reel the fish was on the bar.

## 1.0.3e - 2026-09-29 (was 1.0.9)

- Shake: at low frame rates (around 20 fps) the click was sent before the game had registered the cursor over the button, so nothing happened. It now moves slightly off-centre and onto the button so the game always sees movement, and waits until the game's own cursor position is over the button before clicking (up to 0.25 s, then it clicks anyway).

## 1.0.3d - 2026-09-29 (was 1.0.8)

- Cast: it often needed several up-and-down passes before releasing, because it only fired when a sampled frame landed inside a very narrow window. It now measures the bar speed and input delay and releases on the frame that lands closest to the target, normally on the first pass. If it ever misses both crossings the window widens to 3% on the next pass. Wait between a catch and the next cast is now 1.0 s (was 1.55 s).
- Shake: simplified to move, click on the next frame, release on the frame after, then move to the next circle. Removed the re-aim loop, the size filter and the size-stability wait that could leave it hovering without clicking.
- Reel: aims 0.08 s ahead of the fish instead of 0.3 s (capped to a third of the bar), picks up fish direction changes immediately and reacts sooner. The end-of-reel hold-back that moved the fish away from the bar is gone.

## 1.0.3c - 2026-09-29 (was 1.0.7)

- Shake: the click could occasionally be skipped, leaving the cursor hovering on the circle. If the script thought the mouse was already held from the cast or reel, the press was never sent. Any leftover hold is now cleared when a shake starts, and the shake click always sends the press.
- Reel: if the stored memory offsets for Lock fish stop matching (for example after a game update), the script now searches for the right ones while you reel, then remembers them. You get a notification when it finds them.
- Reel tracking no longer steers the bar away from the fish near the end of a reel, and it releases the click if the bar readings drop out for more than 0.12 seconds.

## 1.0.3b - 2026-09-29 (was 1.0.6)

- Cast release: it could fire almost immediately (around 10%) when the estimate of the input delay was off. It now only releases when the bar is predicted to land within 1% of the target (about 97-99 for a target of 98), and never before the bar is within 12% of it.
- Shake: the click sometimes never happened. The circle is given time to stop animating again (20 ms), it only re-aims if the circle moves more than 6 px, and the click is held for 35 ms so it can't be shorter than a frame.

## 1.0.3 - 2026-09-29 (was 1.0.5)

- Removed the Reel and Monster tabs. Fishfolk fights now happen automatically while Auto Fish is on (with return to the fishing spot). The only setting left for it is a Weapon slot key on the Fish tab.
- Reel tracking uses the left mouse button only.
- Shake clicking is much faster: no approach step, no cursor check, and shorter move/hold/gap delays.
- Cast release now fires as soon as the bar is predicted to land within 1% of the target, instead of waiting for the closest frame, which was often a frame late and overshot.
- The hold-back near the end of a reel can no longer wait 20 seconds when the game's progress text can't be read. It's capped at a few seconds.

## 1.0.2 - 2026-09-29 (was 1.0.4)

- New Reel tab: live status line (shows whether the fish is being locked or tracked), plus look-ahead, braking, fish lead and input settings that were hard-coded before. They save with the rest of the settings.
- Removed the UI size re-scan that ran at startup and every 20 seconds in 1.0.1c. It was a guess at the shake problem, which turned out to be Windows display scaling (125%), and it could stall the reel for a moment.
- If Lock fish can't work on the current client you now get a notification and the status says it's tracking instead.

## 1.0.1c - 2026-09-29 (was 1.0.3)

- Shake aim: the script now measures where the game stores UI sizes every time it loads (and re-checks every 20 seconds) instead of only when the reading looked invalid. On some machines the stored default was wrong, which put the cursor at the top-left corner of the shake circle instead of its centre.

## 1.0.1b - 2026-09-29 (was 1.0.2)

- Shake aiming no longer gets thrown off when the game reports the cursor position late (seen on a slower laptop at about 44 fps). It now waits for the reading to settle before trusting it, and undoes any correction that makes the aim worse.

## 1.0.1 - 2026-09-29

- Fixed shake clicks landing next to the icon on some screens. The aiming now learns the scale and offset between the mouse and the game window (display scaling, windowed mode) and waits for a fresh cursor reading before correcting.
- Added a version label in Settings and a load notification, so you can tell which update you're running.

## 1.0.0 - 2026-09-29

- First release.
