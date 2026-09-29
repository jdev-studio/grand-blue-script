# Changelog

The current version is shown in the menu under Settings, and in the notification when the script loads.

## 1.0.4 - 2026-09-29

- New Reel tab: live status line (shows whether the fish is being locked or tracked), plus look-ahead, braking, fish lead and input settings that were hard-coded before. They save with the rest of the settings.
- Removed the UI size re-scan that ran at startup and every 20 seconds in 1.0.3. It was a guess at the shake problem, which turned out to be Windows display scaling (125%), and it could stall the reel for a moment.
- If Lock fish can't work on the current client you now get a notification and the status says it's tracking instead.

## 1.0.3 - 2026-09-29

- Shake aim: the script now measures where the game stores UI sizes every time it loads (and re-checks every 20 seconds) instead of only when the reading looked invalid. On some machines the stored default was wrong, which put the cursor at the top-left corner of the shake circle instead of its centre.

## 1.0.2 - 2026-09-29

- Shake aiming no longer gets thrown off when the game reports the cursor position late (seen on a slower laptop at about 44 fps). It now waits for the reading to settle before trusting it, and undoes any correction that makes the aim worse.

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
