# grand-blue-script

Fishing, mining and chicken farming helper for Grand Blue. The menu is [JDUI](https://github.com/jdev-studio/jdui).

## Running it

```lua
loadstring(game:HttpGet("https://raw.githubusercontent.com/jdev-studio/grand-blue-script/refs/heads/main/GBS"))()
```

You need an executor that supports the Drawing API (written against Matcha) plus `readfile`/`writefile` for saving settings.

## What's in it

- **Auto fish** – casts, handles the shake and reel minigames, and recasts. Release point is adjustable.
- **Auto mine** – swings the pickaxe and times the bar. In the Anchor Town mining area it can also go round a fixed route of ores (see Mining below).
- **Autofarm chickens** – walks to the nearest chicken and hits it.
- **ESP** – fruits, chests and the treasure map dig spot.
- **Monster fights** – if a Fishfolk shows up while you're fishing, it switches to your weapon, kills it, teleports back to where you started and goes back to fishing.
- **Auto equip** – optional per-tool. Pick the hotbar key for your rod, pickaxe and weapon and it makes sure the right tool is out when you start.

## Controls

| Key | Action |
| --- | --- |
| F1 | Toggle auto fish |
| F2 | Toggle auto mine |
| F4 | Toggle chicken farm |
| End | Show / hide menu |

The menu key can be changed in Settings.

## Mining

The Mine tab has two ways of getting round the Anchor Town ores. Only one can be on at a time:

- **Walk route** walks to each ore on the route in turn, swings, then walks to the next one, and loops.
- **Teleport to ores** does the same, but teleports to each ore instead of walking. If the game won't let you teleport, or keeps moving you back, it switches to Walk route and tells you.

**Swings per ore** sets how many swings it takes at each ore before moving on (2 by default). It moves on sooner if the ore breaks. Ores that are already broken are skipped, and if every ore on the route is broken it waits until one comes back.

With both off, or outside Anchor Town, Auto Mine just mines wherever you're standing.

## Settings

Everything you change in the menu is saved to `grandblue_settings.json` in your executor's workspace folder and loaded next time. Delete the file to reset.

Turning the toggles on (fish, mine, farm) isn't saved on purpose, so nothing starts running by itself when you inject.

## Notes

- The fishing and mining timers read the game's UI bars directly, so a game update that moves things around can break them. Open an issue if that happens.
- Fishfolk detection matches on the entity name / NPCType. If they ever get renamed it will need updating.
