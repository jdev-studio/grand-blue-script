# grand-blue-script

Fishing, mining and chicken farming helper for Grand Blue, with its own menu.

## Running it

```lua
loadstring(game:HttpGet("https://raw.githubusercontent.com/jdev-studio/grand-blue-script/refs/heads/main/GBS"))()
```

You need an executor that supports the Drawing API (written against Matcha) plus `readfile`/`writefile` for saving settings.

## What's in it

- **Auto fish** – casts, handles the shake and reel minigames, and recasts. Release point is adjustable.
- **Auto mine** – swings the pickaxe and times the bar.
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

## Settings

Everything you change in the menu is saved to `grandblue_settings.json` in your executor's workspace folder and loaded next time. Delete the file to reset.

Turning the toggles on (fish, mine, farm) isn't saved on purpose, so nothing starts running by itself when you inject.

## Notes

- The fishing and mining timers read the game's UI bars directly, so a game update that moves things around can break them. Open an issue if that happens.
- Fishfolk detection matches on the entity name / NPCType. If they ever get renamed it will need updating.
