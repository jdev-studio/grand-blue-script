# Changelog

The current version is shown in the menu under Settings, and in the notification when the script loads.

**Numbering:** the script is still in testing, so versions start with `0`. Version 1 is kept for the full release. Each new feature or fix gets the next number: `0.2.5`, then `0.2.6` and so on. If it takes more than one try to get right, the follow ups get a letter: `0.2.6b`, `0.2.6c`.

Everything up to 1.1.5 was renumbered on 2026-10-02: `1.0.x` became `0.1.x` and `1.1.x` became `0.2.x`. Commit messages before that still use the old numbers.

Versions up to 0.1.4b were renumbered to this scheme. Their commit messages use an even older numbering, shown in brackets below.

## 0.2.7 - 2026-10-03

- Fixed the rod not coming back after a Fishfolk fight, which left you punching the air for hours. The switch back to the rod ran in a background thread, and on an external executor that thread can stall after its first wait, so the rod key was pressed and never let go (or never pressed) and no warning showed. Now, before every cast, the script checks you're holding the rod. If not, it presses the rod's slot (and lets go 50 ms later) every 1.5 s until the rod is out, instead of casting with your fists.

## 0.2.6 - 2026-10-02

- Fixed the lag when fishing starts. Before every cast (and every mining or fight click) the script checks for game buttons under the cursor, and that check walked the whole PlayerGui in one go, up to 2500 objects with three reads each. On an external executor every read is slow, so each check froze the game, and since 0.2.3 a refused click was retried every 50 ms, so it froze over and over and never cast. The walk now runs a few objects per frame in the background while a feature is on (a full pass takes about a second and a half, then repeats every 2 seconds), and a click only looks at the last finished list, so checking costs nothing.
- When a game button is in the way of a cast, the script now moves the cursor to a clear spot and casts there, instead of counting it as a cast and charging an empty bar.

## 0.2.5 - 2026-10-02

- Fixed the lag and Auto Fish not casting since 0.2.3. Before each cast the script moved the cursor to the middle of the screen and waited until the game reported it there, but Roblox reports the mouse without the top bar (and the window border if it isn't fullscreen), so it never matched. It kept moving the cursor every frame instead of casting, which also pinned your mouse to the middle. It now moves the cursor once per cast, waits 80 ms and casts.

## 0.2.4 - 2026-10-02

- The menu remembers everything you set in it (saved to grandblue_menu.json and loaded next time): Random casts, the weapon and pickaxe slots, Auto equip pickaxe, Walk route / Teleport to ores, Swings per ore, the three ESP toggles, the theme and the menu key. Needs JDUI 1.0.5b.
- Auto Fish, Auto Mine and Autofarm Chickens are deliberately not remembered, so loading the script never starts farming by itself.

## 0.2.3 - 2026-10-02

- Fixed bad (erm) casts. Before the cast bar showed up, the script let go and pressed again every 0.25 s in case the click hadn't registered. It waited for the game's QTEEvent, which comes from the server and can arrive later than that, so on a slower connection it let go while the bar was already charging and the cast went out at around 24%. It now reads the cast bar on screen as soon as it appears, and waits 0.7 s before deciding a click didn't register.
- The cursor no longer gets stuck on the bait panel. After shaking, the cursor was left wherever the last shake button was, often on the bait panel, and casting clicked the panel instead of casting. Every cast now moves the cursor to the middle of the screen over the water first. The spots it moves to when a game button is in the way also stay clear of the bait panel now.
- Clicking the menu no longer makes you swing or cast while nothing is running (needs JDUI 1.0.4). While Auto Fish, Auto Mine or the chicken farm is on, the game still gets clicks over the menu so the script's own clicks aren't lost.
- The log (grandblue_log.txt) now has a line for every cast: where it let go, what it aimed for, where the bar actually stopped, and casts that ended before the script let go.

## 0.2.2 - 2026-10-01

- Mining no longer gets stuck when a game button is under the mouse. The popup guard skipped the click but still counted it as a swing, so every ore said "No mining bar" and the mouse was never moved. It now moves the mouse to a clear spot first, like casting does.
- The "No mining bar" message says what you're holding, and if it isn't the pickaxe, tells you to set Pickaxe slot and Auto equip pickaxe in the Mine tab (those settings are saved per computer).
- With Auto equip pickaxe on, the pickaxe is checked at every ore instead of only when Auto Mine starts, so it switches back if fishing or a fight changed your tool.

## 0.2.1 - 2026-10-01

- New Anchor Town mining route that goes to all 9 ores you can actually reach, in one loop round the mine. The paths were worked out from a map of every solid part in the mine (rocks, walls, ledges), so it walks round obstacles instead of into them.
- Ores you can't get to are left out: the one up on the hill, and the two on the west ledge that are walled in by cliffs.
- It jumps at the two spots where the route steps up onto higher ground, aimed at the landing spot.
- Every mine spot is on the ground beside the ore, close enough to swing and facing the nearest crystal. If the character ends up on top of a rock, it steps back off before swinging.
- It doesn't stop at the points along the way, so it keeps full running speed between ores.

## 0.2.0 - 2026-10-01

- Mining skips broken ores. The ore scan ran in a background thread that paused between batches, and in Matcha those paused threads never carry on, so the scan never finished and the script didn't know which ores were broken. It swung at every spot on the route, even ores already marked Mined. The scan now runs a batch every frame from the main loop.
- The log file (grandblue_log.txt) is written again. In 0.1.12 it stopped after the first line for the same reason. It's now saved from the main loop at most once a second, and straight away when the script unloads.
- Settings save again. Saving used the same kind of paused thread, so on Matcha your menu key, theme, mining mode and other settings weren't being saved. They're now saved from the main loop 0.4 seconds after a change.

## 0.1.12 - 2026-10-01

- Shake clicks are never blocked by other game screens again. 0.1.9 made the shake wait whenever another button was under the cursor, and before every shake click it searched the whole game UI, which slowed shaking down. Shake now clicks as fast as it did before 0.1.9. Casting, mining and fighting still avoid clicking game popups.
- Faster script overall, with the same behaviour:
  - While fishing with no fight going on, the fishfolk check does almost nothing each frame instead of reading your position every frame.
  - Treasure and item ESP are skipped completely when they're off. When item ESP is on with nothing found, it doesn't read your position, and labels are only rebuilt when the distance changes.
  - Treasure ESP only reads the camera when the dig spot is off screen.
  - The fishfolk search checks the cheap things (chicken, distance) before checking whether a mob is alive.
  - Mining only reads the mining bar when it's needed, not on every frame of the cooldown or while walking between ores.
  - The ore scan skips most objects with a quick name check and remembers each ore's type, so later scans read far less.
  - Walking the route no longer records your position twice per frame.
  - Chicken farm: blacklisted chickens are skipped before any game reads, your character is only looked up when walking, and the obstacle rays are switched off once they're shown never to hit (they never do in Matcha).
  - The log file is written at most once a second instead of on every line, and straight away when the script unloads.
  - Hotkey checks no longer create new functions 20 times a second.

## 0.1.11 - 2026-10-01

- Replaced the free-roaming ore walker with a fixed route of 5 Anchor Town ores, recorded standing next to each one. It stands where the route was recorded and faces the same way, so it no longer has to work out where the ore is or how to get next to it.
- New Mine tab toggles, only one on at a time: **Walk route** walks the route in a loop, and **Teleport to ores** teleports to each ore instead. If the game refuses the teleport or moves you back, it switches to Walk route and says so. With both off, Auto Mine mines where you stand.
- New **Swings per ore** slider (1 to 10, default 2). It moves on early if the ore breaks, skips ores that are already broken, and waits if the whole route is broken until one comes back.
- If the character can't be turned directly, it stops just short of each spot and steps onto it facing the ore.
- Mining errors now go in the log.

## 0.1.10i - 2026-10-01

- Walks round obstacles instead of digging through them. With no raycasts it feels its way: when it's blocked it follows the edge of whatever is in the way (keeping it on one side) and turns back towards the ore as soon as the way opens. If it can't get round on either side it gives up on that ore instead of jumping or digging. It only tries a single jump, for a small step, when it's away from every ore rock.
- Stays level with the ore: it only mines a crystal that's between 3 studs below and about 2 studs above its middle. If no crystal is at the right height from where it is, it walks round the ore until one is.
- Faces the crystal exactly before swinging: it lines itself up so the crystal is straight along one of the 8 directions it can face, faces it, and checks it's within 12 degrees before the swing; if not, it lines up again. Nothing but the crystal is in front of it when it swings.
- Leaves ores other players are mining. Ores already showing a health bar are picked last, and if the target's HP goes down while it isn't swinging, someone else is on it, so it picks another ore.

## 0.1.10h - 2026-10-01

- Stops climbing on the ore. 0.1.10g relied on raycasts to see what's in front, but in Matcha a raycast never hits anything (not even the ground), so it always thought the way was clear, walked into the rock, jumped when it got stuck and ended up on top, spinning between getting on and off. Raycasts are no longer used at all.
- Goes for the ore crystals instead of the middle of the rock. The rock's size came from its mesh boxes, which are far bigger than the rock (it measured one at 23.5 studs wide), so every distance was off. Now it walks to the nearest crystal at a height it can reach and counts as touching when it's within about 3 studs of it, or pressed against the rock within 6 studs of it. A missed swing marks that crystal and it tries the next one.
- Never jumps near the ore. Pressed against the rock but not at a crystal, it walks round the rock towards one. Jumping is only for being stuck away from the ore (an edge), and if two jumps don't get it past, it digs. If it still hasn't reached a crystal 6 seconds after getting to the ore, it digs straight towards one.
- On the way to an ore it now jumps twice before it starts digging, so it hops over edges instead of mining them.
- The camera in Grand Blue doesn't turn with the arrow keys, so it faces the ore by walking at the crystal.

## 0.1.10g - 2026-09-30

- Checks what's in front before every swing. Near the ore it casts short rays straight at it and only swings when the ore's rock is right in front of it (within about 1.8 studs); otherwise it keeps walking forward. It used to decide it was close from distance maths and swing at the air.
- Jumps small edges instead of mining them: if something low is in front and the way is clear at chest height, it jumps and carries on. A wall too tall to jump gets dug through, and it keeps digging and walking until it reaches the ore.
- Faces the ore properly: it turns the camera with the arrow keys until the ore is straight ahead, then walks at it, so the character faces it exactly. Walking with WASD alone can only face 8 directions, up to 22 degrees off, which made swings miss. If the camera doesn't respond to the arrow keys it notices and falls back to walking only.
- A swing that misses while touching the ore pushes it a little further in before the next one; after 5 misses it moves on.
- If raycasts ever stop working, it falls back to treating "pushing and not getting closer" as touching the ore.

## 0.1.10f - 2026-09-30

- Leaves out ores it can't reach: any ore whose rock sits more than 8 studs above you (like the Iron Ore up on the cliff) is never picked. As a backstop, an ore it hasn't reached within 30 seconds is skipped for 3 minutes, so it can't run in circles.
- Tunnels properly: once it has dug once on the way to an ore, the next time it's blocked it digs again after 0.7 seconds instead of 2, so it goes hit, walk, hit, walk until it reaches the ore.
- No extra hit after the ore breaks: the server's `Mined` flag can arrive a moment after the last hit, by which time the next swing had already started. When the ore is down to 1 HP it now waits up to 1.2 seconds for the break before swinging again.
- Hugs the ore: stops right at the rock's edge (or wherever it bumps into it) instead of a stud or two outside it.
- It no longer drops the ore it's walking to when a detour takes it a little past the edge of the mining area (it used to go back to swinging on the spot).

## 0.1.10e - 2026-09-30

- Stops swinging once the ore is broken. The miner starts its next swing by itself when a cooldown ends, and the walker kept letting it, so after an ore broke it kept hitting it (a broken ore still starts a mining bar). The miner now only swings while the walker is actually mining or digging.
- Stands beside the ore instead of on it or in the middle of it. It measures each rock (its width and the height of its top and bottom) and stops about a stud outside the edge, or wherever it bumps into the rock first. It counts as on top of the ore when it's above the rock's top inside its footprint (it used to compare against the crystals, which sit on the rock, so standing on the rock looked level) and steps off.
- Turning to face the ore is a short tap instead of a 0.18 s walk, and the ore is no longer left out of the obstacle checks, so it doesn't walk into or up the rock.
- A missed swing moves it 1.5 studs closer (never past halfway into the rock); after 4 misses it moves on. This now actually triggers: the check used to wait for the miner to be idle, which never happens because it goes straight into the next swing.

## 0.1.10d - 2026-09-30

- Moves on to the next ore straight away. A respawned ore comes back as a new object with a new name, so the list of ores went stale and after a break it could sit for 10+ seconds until the next rescan. It now rescans as soon as it has nothing to go to (at most every 3 seconds) and every 20 seconds otherwise.
- The height check allows for the ore crystals sitting up on the rock: it mines from up to 8 studs below the crystals (was 4), so it stops walking and digging when it's already hitting the ore.
- Counts an ore as finished (and says so) even when it broke during a dig swing.

## 0.1.10c - 2026-09-30

- Gets right up to the ore. It used to stop about 10 studs from the ore's first part, which on some rocks is too far to hit. Now it aims at the middle of the ore crystals and walks straight in until it bumps into the rock. If a swing still doesn't start a mining bar it steps closer and tries again, and after 4 misses it moves on to another ore.
- The broken-ore check (health bar gone after it was showing) now works whatever the walker is doing, not only while it's swinging.
- The ore is also checked straight after every swing, as soon as the swing or mining bar ends, on top of the regular check every 0.4 seconds.
- Walk to ores writes `[mine]` lines to the log: which ore it picked, each state change, and what it reads from the ore (`Mined` and its type, health bar, HP). Useful for tracking down why it doesn't move on.

## 0.1.10b - 2026-09-30

- Notices when an ore breaks. A broken ore keeps all its pieces and even still starts a mining bar when you hit it; what changes is that it gets `Mined=true` and its health bar disappears. Walk to ores now watches for both, then moves on to the next ore. The mining status shows the ore's HP while it works.
- Stays level with the ore. Near an ore it walks straight in without jumping (it used to jump onto the rock and swing above it), and if it ends up on top it steps off and comes back in. It only mines when it's within about 4 studs below to 5 above the ore.
- Digs through walls. If it stops getting closer for 2 seconds it faces the ore and swings the pickaxe, then carries on walking. After 6 digs with no progress it skips that ore for 2 minutes.
- Rescans the area straight away when it can't find any ore, instead of waiting up to a minute.

## 0.1.10 - 2026-09-30

- Auto Mine walks between ores in Anchor Town. Inside the mining area (a border around the island's ores) it goes to the nearest ore, mines it with the normal mining code, and when the ore is gone, marked mined, or out of ore pieces it walks to the next one. Ores it can't reach within 8 seconds are skipped for 2 minutes, and an ore that won't start a mining bar after 3 swings is skipped for a minute. Outside Anchor Town, or with Walk to ores switched off on the Mine tab, mining works exactly as before.
- Only the Anchor Town island folder is scanned, spread over several frames, once a minute; in between it only re-checks the ores it already knows about.

## 0.1.9 - 2026-09-30

- The script no longer clicks game popups. Before every click it checks whether a visible button from another game screen (like the world boss "join?" prompt) is under the cursor. Shake clicks wait until the shake button is clear, casts move the cursor to a clear spot first, and mining and fight clicks are skipped while something is in the way. A notification says what it avoided (at most once every 30 seconds per button).
- Safer cast timing. The script lets go slightly early to make up for the game's reaction time, which it measures itself. If that measurement went wrong it could let go too early on every cast (around 85%) until you reloaded. The measured delay is now reset whenever Auto Fish is turned on and capped at 35 ms (was 120), and casts aim for 99.5% (was 98%), right where the bar turns around, so an early or late release still lands in the 96-100% perfect zone.

## 0.1.8b - 2026-09-30

- Fixed random casts. In 0.1.8 the cast code called the target picker through a name that was reused for the cast bar inside that function, so it errored on every cast and let go at the wrong moment instead of aiming. Normal casts now aim for 97-99% again.
- Misses are properly random now: each cast has about a 1 in 4 chance of landing between 80% and 92% (1 in 10 straight after a miss), and there are never more than 6 perfects in a row.
- Removed the Release at slider. Casts always aim for the perfect zone, with the random misses mixed in.

## 0.1.8 - 2026-09-30

- Random casts (Fish tab, on by default): every 3 to 6 casts one is released somewhere between 80% and 92% instead of the perfect zone, and the rest aim within 1% either side of Release at. Lots of perfect casts in a row can get you banned. The log marks the deliberate ones with "off target on purpose".

## 0.1.7b - 2026-09-30

- Fixed the menu not showing ("menu failed to start"). Matcha drops the value the JDUI loadstring returns, so the script now picks the menu up from `_G.JDUI` instead.

## 0.1.7 - 2026-09-30

- The menu is now loaded from [JDUI](https://github.com/jdev-studio/jdui) instead of being copied into the script, so menu fixes reach this script straight away. The script is about 700 lines shorter.
- JDUI is also a newer version of the menu than the old built-in copy: it only touches drawings that actually changed each frame, which is lighter on Matcha.
- If the menu can't be downloaded the script still runs; the F1 / F2 / F4 hotkeys work without it.

## 0.1.6b - 2026-09-29

- Performance: Matcha dropped to about 1 FPS while fishing. The 0.1.5c lock writer busy-looped for 10 ms between every yield, which starves an external executor. It's removed, along with the extra Stepped connection; the lock now writes from the main update and a single Heartbeat connection.
- The main update runs at most 100 times a second while working and 20 when idle (was 250 and about 70), hotkeys are polled 20 times a second, the Fishfolk scan runs once a second and reads names before anything else, reel stats read the screen every 0.15 s, and the rod lookup at the start of a fight only searches the hotbar GUI.

## 0.1.6 - 2026-09-29

- Fishfolk fights now carry on until the Fishfolk is dead (removed, marked Dead or at 0 health), with a 3 minute safety limit. They used the chicken farm's give-up rule, which ended the fight after 6 seconds without getting closer and then restarted it 30 seconds later.
- At the start of a fight the script notes the rod you're holding and finds its hotbar number, switches to the Weapon slot (or puts the rod away and fights with fists if no weapon is set), and switches back to the rod afterwards before carrying on fishing.
- The Auto equip rod toggle and Rod slot setting are removed.

## 0.1.5c - 2026-09-29

- Tracking is removed; Lock fish is the only reel method and is always on (the toggle is gone).
- The lock offsets are checked once per session instead of every reel. That check kept failing while the fish moved, so the fish ran free at the start of each reel; now later reels lock from the first frame.
- Between yields the lock rewrites the fish position continuously for about 10 ms, on top of every render, physics and heartbeat step, so the game's own animation is overwritten almost every frame.
- The fish is centred using its on-screen width, and the lock writer is lighter so it can run many more times per frame.

## 0.1.5b - 2026-09-29

- Lock fish is back (Fish tab, on by default) and works like the original script: no clicking during the reel, so the bar rests at the far left, and the fish is written onto the bar's position on every render, physics and heartbeat step plus its own loop. In 0.1.4 tracking kept running while locked, which kept the bar moving and made the lock look worse than it was. If the lock can't engage at all in a reel it says so and tracks for that reel.
- Tracking (Lock fish off) ignores readings that jump much further than normal for up to 3 frames and keeps steering on its prediction, and caps bar speed, fish speed and the learned bar acceleration, so a single bad frame can't send the bar off in the wrong direction.
- The reel summary shows how much of the reel the fish was on the bar.

## 0.1.5 - 2026-09-29

- New reel tracking. The old version worked from raw frame-to-frame speed readings, which are very noisy at low frame rates, and didn't account for its own input delay, so it overshot and swung around the fish. It now smooths the bar and fish positions using the input it knows it's sending, learns how fast the bar speeds up when held and when released, predicts where the bar will be once the next click takes effect, and brakes relative to the fish's own speed. In a simulation at 22 fps the fish stayed in the bar 78% of the time instead of 68%, and the worst case went from 38% to 64%.
- The re-press check during reeling is off. It read memory offsets that may not match the game and could release and re-press the mouse at random.
- Rod detection reads the game's HeldItemName instead of looking for a Roblox Tool, so the false "Wrong tool equipped (nothing)" message is gone.
- The console logging no longer touches the executor's notify function.

## 0.1.4e - 2026-09-29

- Lock fish is removed and every reel uses click tracking. Testing showed the game works out catch progress from its own values, not the on-screen positions: holding the bar on the fish lost 4 out of 4 reels at 1-3% progress, while tracking alone caught at 99%. Writing the positions only moved the picture and confused the tracking, which reads the bar and fish from the screen.
- Reloading the script no longer doubles every console line.

## 0.1.4d - 2026-09-29

- Lock fish test is more accurate. It used to take one reading some time after the nudge, which could land 300+ ms late while the bar or fish had already moved on its own. It now measures each part's speed first, then reads every frame for 0.15 s after the nudge and counts whether the game kept the change or put it back.
- It tests the bar first. If the game follows the bar, the bar is held on the fish every frame, which works however the fish is animated. Moving the fish is only used if the bar doesn't stick, since the game overwrites the fish while animating it (about 80% on the bar in testing).

## 0.1.4c - 2026-09-29

- Every message the script shows is also printed to the executor console (starting with `[Grand Blue]`) and saved to `grandblue_log.txt`, so it can be copied in full.
- The Lock fish test prints its raw numbers, and each reel ends with a one-line summary: caught or lost, final progress, lock mode and how much of the reel the fish stayed on the bar.

## 0.1.4b - 2026-09-29 (was 1.1.1)

- Lock fish now tests which position the game actually reads. Early in the first reel it nudges the fish, then the bar, and checks whether the game keeps the change. It then holds whichever one the game follows on top of the other, so both sit at the same spot. If neither sticks it falls back to tracking and tries the test again on the next two reels. A notification says which one it found.

## 0.1.4 - 2026-09-29 (was 1.1.0)

- Lock fish: the fish position was only written once per script update and the mouse was let go while locked, so when the game moved the fish in between, it slipped away and nothing brought the bar back. The position is now rewritten continuously (every render, physics and heartbeat step plus its own loop), and the bar keeps tracking the fish as a backup while locked.
- The check that the lock offsets are right is less strict and gets 40 tries per reel instead of 15.
- After each reel a notification shows how much of the reel the fish was on the bar.

## 0.1.3e - 2026-09-29 (was 1.0.9)

- Shake: at low frame rates (around 20 fps) the click was sent before the game had registered the cursor over the button, so nothing happened. It now moves slightly off-centre and onto the button so the game always sees movement, and waits until the game's own cursor position is over the button before clicking (up to 0.25 s, then it clicks anyway).

## 0.1.3d - 2026-09-29 (was 1.0.8)

- Cast: it often needed several up-and-down passes before releasing, because it only fired when a sampled frame landed inside a very narrow window. It now measures the bar speed and input delay and releases on the frame that lands closest to the target, normally on the first pass. If it ever misses both crossings the window widens to 3% on the next pass. Wait between a catch and the next cast is now 1.0 s (was 1.55 s).
- Shake: simplified to move, click on the next frame, release on the frame after, then move to the next circle. Removed the re-aim loop, the size filter and the size-stability wait that could leave it hovering without clicking.
- Reel: aims 0.08 s ahead of the fish instead of 0.3 s (capped to a third of the bar), picks up fish direction changes immediately and reacts sooner. The end-of-reel hold-back that moved the fish away from the bar is gone.

## 0.1.3c - 2026-09-29 (was 1.0.7)

- Shake: the click could occasionally be skipped, leaving the cursor hovering on the circle. If the script thought the mouse was already held from the cast or reel, the press was never sent. Any leftover hold is now cleared when a shake starts, and the shake click always sends the press.
- Reel: if the stored memory offsets for Lock fish stop matching (for example after a game update), the script now searches for the right ones while you reel, then remembers them. You get a notification when it finds them.
- Reel tracking no longer steers the bar away from the fish near the end of a reel, and it releases the click if the bar readings drop out for more than 0.12 seconds.

## 0.1.3b - 2026-09-29 (was 1.0.6)

- Cast release: it could fire almost immediately (around 10%) when the estimate of the input delay was off. It now only releases when the bar is predicted to land within 1% of the target (about 97-99 for a target of 98), and never before the bar is within 12% of it.
- Shake: the click sometimes never happened. The circle is given time to stop animating again (20 ms), it only re-aims if the circle moves more than 6 px, and the click is held for 35 ms so it can't be shorter than a frame.

## 0.1.3 - 2026-09-29 (was 1.0.5)

- Removed the Reel and Monster tabs. Fishfolk fights now happen automatically while Auto Fish is on (with return to the fishing spot). The only setting left for it is a Weapon slot key on the Fish tab.
- Reel tracking uses the left mouse button only.
- Shake clicking is much faster: no approach step, no cursor check, and shorter move/hold/gap delays.
- Cast release now fires as soon as the bar is predicted to land within 1% of the target, instead of waiting for the closest frame, which was often a frame late and overshot.
- The hold-back near the end of a reel can no longer wait 20 seconds when the game's progress text can't be read. It's capped at a few seconds.

## 0.1.2 - 2026-09-29 (was 1.0.4)

- New Reel tab: live status line (shows whether the fish is being locked or tracked), plus look-ahead, braking, fish lead and input settings that were hard-coded before. They save with the rest of the settings.
- Removed the UI size re-scan that ran at startup and every 20 seconds in 0.1.1c. It was a guess at the shake problem, which turned out to be Windows display scaling (125%), and it could stall the reel for a moment.
- If Lock fish can't work on the current client you now get a notification and the status says it's tracking instead.

## 0.1.1c - 2026-09-29 (was 1.0.3)

- Shake aim: the script now measures where the game stores UI sizes every time it loads (and re-checks every 20 seconds) instead of only when the reading looked invalid. On some machines the stored default was wrong, which put the cursor at the top-left corner of the shake circle instead of its centre.

## 0.1.1b - 2026-09-29 (was 1.0.2)

- Shake aiming no longer gets thrown off when the game reports the cursor position late (seen on a slower laptop at about 44 fps). It now waits for the reading to settle before trusting it, and undoes any correction that makes the aim worse.

## 0.1.1 - 2026-09-29

- Fixed shake clicks landing next to the icon on some screens. The aiming now learns the scale and offset between the mouse and the game window (display scaling, windowed mode) and waits for a fresh cursor reading before correcting.
- Added a version label in Settings and a load notification, so you can tell which update you're running.

## 0.1.0 - 2026-09-29

- First release.
