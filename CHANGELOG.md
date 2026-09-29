# Changelog

The current version is shown in the menu under Settings, and in the notification when the script loads.

## 1.0.6 - 2026-09-29

- Cast release: it could fire almost immediately (around 10%) when the estimate of the input delay was off. It now only releases when the bar is predicted to land within 1% of the target (about 97-99 for a target of 98), and never before the bar is within 12% of it.
- Shake: the click sometimes never happened. The circle is given time to stop animating again (20 ms), it only re-aims if the circle moves more than 6 px, and the click is held for 35 ms so it can't be shorter than a frame.

## 1.0.5 - 2026-09-29

- Removed the Reel and Monster tabs from 1.0.4. Fishfolk fights now happen automatically while Auto Fish is on (with return to the fishing spot). The only setting left for it is a Weapon slot key on the Fish tab.
- Reel tracking uses the left mouse button only.
- Shake clicking is much faster: no approach step, no cursor check, and shorter move/hold/gap delays.
- Cast release now fires as soon as the bar is predicted to land within 1% of the target, instead of waiting for the closest frame, which was often a frame late and overshot.
- The hold-back near the end of a reel can no longer wait 20 seconds when the game's progress text can't be read. It's capped at a few seconds.

## 1.0.4 - 2026-09-29

- New Reel tab: live status line (shows whether the fish is being locked or tracked), plus look-ahead, braking, fish lead and input settings that were hard-coded before. They save with the rest of the settings.
- Removed the UI size re-scan that ran at startup and every 20 seconds in 1.0.3. It was a guess at the shake problem, which turned out to be Windows display scaling (125%), and it could stall the reel for a moment.
- If Lock fish can't work on the current client you now get a notification and the status says it's tracking instead.

## 1.0.3 - 2026-09-29

- Shake aim: the script now measures where the game stores UI sizes every time it loads (and re-checks every 20 seconds) instead of only when the reading looked invalid. On some machines the stored default was wrong, which put the cursor at the top-left corner of the shake circle instead of its centre.

## 1.0.2 - 2026-09-29

- Shake aiming no longer gets thrown off when the game reports the cursor position late (seen on a slower laptop at about 44 fps). It now waits for the reading to settle before trusting it, and undoes any correction that makes the aim worse.

## 1.0.6 - 2026-09-29

- Cast release: it could fire almost immediately (around 10%) when the estimate of the input delay was off. It now only releases when the bar is predicted to land within 1% of the target (about 97-99 for a target of 98), and never before the bar is within 12% of it.
- Shake: the click sometimes never happened. The circle is given time to stop animating again (20 ms), it only re-aims if the circle moves more than 6 px, and the click is held for 35 ms so it can't be shorter than a frame.

## 1.0.5 - 2026-09-29

- Removed the Reel and Monster tabs from 1.0.4. Fishfolk fights now happen automatically while Auto Fish is on (with return to the fishing spot). The only setting left for it is a Weapon slot key on the Fish tab.
- Reel tracking uses the left mouse button only.
- Shake clicking is much faster: no approach step, no cursor check, and shorter move/hold/gap delays.
- Cast release now fires as soon as the bar is predicted to land within 1% of the target, instead of waiting for the closest frame, which was often a frame late and overshot.
- The hold-back near the end of a reel can no longer wait 20 seconds when the game's progress text can't be read. It's capped at a few seconds.

## 1.0.4 - 2026-09-29

- New Reel tab: live status line (shows whether the fish is being locked or tracked), plus look-ahead, braking, fish lead and input settings that were hard-coded before. They save with the rest of the settings.
- Removed the UI size re-scan that ran at startup and every 20 seconds in 1.0.3. It was a guess at the shake problem, which turned out to be Windows display scaling (125%), and it could stall the reel for a moment.
- If Lock fish can't work on the current client you now get a notification and the status says it's tracking instead.

## 1.0.3 - 2026-09-29

- Shake aim: the script now measures where the game stores UI sizes every time it loads (and re-checks every 20 seconds) instead of only when the reading looked invalid. On some machines the stored default was wrong, which put the cursor at the top-left corner of the shake circle instead of its centre.

## 1.0.2 - 2026-09-29

- Shake aiming no longer gets thrown off when the game reports the cursor position late (seen on a slower laptop at ~44 fps). It now waits for the reading to settle before trusting it, and undoes any correction that makes the aim worse.

## 1.0.1 - 2026-09-29

- Fixed shake clicks landing next to the icon on some screens. The aiming now learns the scale and offset between the mouse and the game window (display scaling, windowed mode) and waits for a fresh cursor reading before correcting.
- Added a version label in Settings and a load notification, so you can tell which update you're running.

## 1.0.0 - 2026-09-29

- First release.
