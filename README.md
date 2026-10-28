# Winter Survival

[한국어](README.ko.md)

A survival game about holding out for 15 minutes in a snowy forest. Body temperature and fullness keep dropping, so you have to stay by the campfire or carry a torch, pick up wood and stones to craft tools, and hunt animals to eat their meat. You pick one of four characters on the start screen, and the ending plays when time runs out. It was an entry in Winter Game Jam 2013, hosted by the GameMaker community CrazyGM.

<p>
  <img src="docs/screenshots/screenshot-1.png" width="420" alt="Start screen">
  <img src="docs/screenshots/screenshot-2.png" width="420" alt="In game">
</p>


## How to play

Download it from Releases and run it. Click a character on the start screen and you begin next to a campfire. The thermometer, stomach, and heart at the top left show body temperature, fullness, and health, and the top center shows the time left.

| Key | Action |
|---|---|
| Arrow keys | Move |
| Z | Pick up the item under your feet |
| 1 to 6 | Select an inventory slot. Selected slots combine with each other |
| X | Drop the selected item |
| A | Eat meat |
| Space | Attack with the tool you are holding |

Temperature and fullness drop by 1.5 every second, and you die when either reaches 0. Near the campfire your temperature holds, and closer still it rises. A campfire burns out in 5 minutes, and a torch keeps your temperature up for 30 seconds. Trees and animals are visible only around the character and the fires.

| Recipe | Result |
|---|---|
| Wood 3, big stone 1 | Campfire |
| Wood 2, small stone 1 | Torch |
| Wood 1, small stone 1 | Hammer |
| Wood 1, ice shard 1 | Knife |
| Wood 1, small stone 1, leather 1, ice shard 1 | Pistol |

| Animal | Health | Drops |
|---|---|---|
| Rabbit | 100 | Wood 0 or 1, small stone 0 or 1, meat 1 or 2 |
| Deer | 200 | Wood 1 or 2, big stone 0 or 1, meat 1 to 3 |
| Wolf | 250 | Ice shard 0 or 1, meat 2 or 3 |
| Tiger | 500 | Leather 0 or 1, meat 3 to 5 |

Wolves and tigers charge when you get close and take 5 health per hit. The hammer and knife deal 50 damage and the pistol 100. A killed animal respawns in the same spot after 5 minutes.


## How it works

**Temperature is set by the distance to fire.** Every second the character subtracts 1.5 from temperature and adds the `infire` value. Each step the campfire measures the distance and sets it to 2 within 150 or 4 within 70, and a torch sets it to 2 while it is held. So temperature rises right next to a campfire, rises very slowly with only a torch, and falls when you have neither.

```gml
temperature-=1.5
temperature+=infire
temperature=min(100, temperature)
infire=0
alarm[1] = room_speed*1
```

**Crafting is decided by counting the selected slots.** For the switched-on slots out of the six in the inventory, the game counts each item type into an array and makes the result only when the counts match a recipe exactly. Tools go into a single tool slot, so crafting a new one drops the one you were holding onto the ground.

**All animals run on one parent object.** Rabbits, deer, wolves, and tigers are child objects that differ only in sprite, and the parent's events branch on the sprite to set movement speed, health, whether the animal attacks, and what it drops on death. When one dies, it leaves a 300-second respawn object in the same spot.

**Darkness comes from a visibility check.** Each step every tree and animal measures its distance to the character, campfires, and torches and sets `image_alpha` to 0 when out of range. Near a fire it is tinted gray and near the character dark gray to show the difference in light. With 1494 trees in a 3000x3000 room, the trees and animals outside the view are deactivated every step and only the area around the view is activated.

```gml
with(obj_animal_parent){depth = -ceil(y); instance_deactivate_object(id)}
with(obj_tree){instance_deactivate_object(id)}
instance_activate_region(view_xview[0]-40, view_yview[0]-40, view_wview[0]+80, view_hview[0]+80, true)
```


## Files

| Path | Content |
|---|---|
| `source/The Winter Survival.project.gmx` | GameMaker: Studio project file |
| `source/objects/` | Character, animal, fire, item, and screen effect objects |
| `source/rooms/` | Start, game, and ending rooms |
| `source/sprites/`, `source/sound/`, `source/fonts/` | Graphics, sounds, and fonts |
| `docs/screenshots/` | Screenshots |
| Releases | Executable and manual |


## Credits

skesswsw and yesjinil helped with the graphics.


## License

CC BY-NC-ND 4.0. Unmodified copies may be shared for noncommercial purposes with attribution. Distributing modified versions and commercial use are not allowed. Bundled libraries, graphics, sounds, and maps made by other people keep their own rights. See [LICENSE](LICENSE).
