# datapack-py docs

Docs and examples for `datapack-py`.

`datapack-py` is a browser-based tool for writing Minecraft datapacks in Python-like code, then compiling that code into normal datapack files.

Beta site: https://datapack-py.pages.dev

This repo does not contain the compiler source code. It is just examples, notes, and public docs.

## Why use it?

Minecraft datapacks are powerful, but large packs get messy fast.

`datapack-py` helps with the parts that usually turn into repeated `.mcfunction` boilerplate:

- scoreboards
- custom item tools
- right-click triggers
- raycasts
- sidebar displays
- bossbars
- per-player and per-entity variables
- generated helper functions
- repeated `execute` commands

If your datapack is only a few commands, raw `.mcfunction` files are probably fine. `datapack-py` is for the point where the datapack starts feeling like a small program.

## Try an example

Open the beta site:

https://datapack-py.pages.dev

Copy one of the examples from this repo into the editor, then click **Run** to download the generated datapack zip.

## Examples

### Blink Wand

Source: [`examples/blink_wand/source.dpy`](examples/blink_wand/source.dpy)

A custom right-click wand that raycasts forward, leaves a particle trail, plays a sound, and teleports the player.

```py
on_right_click(blink_wand):
    with raycast_miss(
        max=10,
        step=1,
        through=[air, cave_air, void_air],
        __on_step=blink_trail
    ):
        particle(portal, pos=here, delta=vec3(0.2, 0.4, 0.2), speed=0.1, count=35, force=true)
        playsound("minecraft:entity.enderman.teleport", target=me, volume=0.8, pitch=1.2)
        tp(me, here)
```

The compiler turns this into a custom item, a right-click advancement, raycast functions, and the normal Minecraft commands needed to run it.

What the website generates:

```txt
13 datapack files
9 function files

data/my_pack/advancement/__click/right/blink_wand_1.json
data/my_pack/function/__click/right/blink_wand_1.mcfunction
data/my_pack/function/__click/right/blink_wand_1_body.mcfunction
data/my_pack/function/__ray/start_1.mcfunction
data/my_pack/function/__ray/step_1.mcfunction
data/my_pack/function/__ray/hit_1.mcfunction
```

### Ore Scanner

Source: [`examples/ore_scanner/source.dpy`](examples/ore_scanner/source.dpy)

A custom compass that shows mining stats while held and pings when the player is standing on ore.

```py
with as_players():
    if holding(me, ore_scanner):
        set_scoreboard_title(f"{gold}{bold}Ore Scanner")
        set_scoreboard_line(f"{aqua}Diamond ore: {stat(mined, diamond_ore)}")
        set_scoreboard_line(f"{red}Ancient debris: {stat(mined, ancient_debris)}")
        set_scoreboard_line(f"{green}Scans: {me.scans}")

on_right_click(ore_scanner):
    me.scans = me.scans + 1
    playsound("minecraft:block.note_block.pling", target=me, volume=0.6, pitch=1.8)
```

This uses custom item data, player statistics, sidebar text, and a per-player scan counter.

What the website generates:

```txt
13 datapack files
9 function files
4 generated helper functions

data/my_pack/function/__setup.mcfunction
data/my_pack/function/main.mcfunction
data/my_pack/function/generated_sub_1.mcfunction
data/my_pack/function/generated_sub_2.mcfunction
data/my_pack/function/generated_sub_3.mcfunction
data/my_pack/function/generated_sub_4.mcfunction
data/my_pack/advancement/__click/right/ore_scanner_1.json
```

### Arena Controller

Source: [`examples/arena_controller/source.dpy`](examples/arena_controller/source.dpy)

A small wave controller. Right-click the arena key to advance waves, spawn mobs, update the bossbar, and reset after the final wave.

```py
on_right_click(arena_key):
    current_wave = wave
    if current_wave == 1:
        call(wave_one)
    if current_wave == 2:
        call(wave_two)
    if current_wave == 3:
        call(final_wave)
    if current_wave >= 4:
        call(reset_arena)
```

This uses a persistent wave variable, custom item trigger, bossbar updates, generated functions, and sidebar output.

What the website generates:

```txt
14 datapack files
10 function files

data/my_pack/function/__setup.mcfunction
data/my_pack/function/init.mcfunction
data/my_pack/function/main.mcfunction
data/my_pack/function/wave_one.mcfunction
data/my_pack/function/wave_two.mcfunction
data/my_pack/function/final_wave.mcfunction
data/my_pack/function/reset_arena.mcfunction
data/my_pack/function/__click/right/arena_key_1_body.mcfunction
data/my_pack/advancement/__click/right/arena_key_1.json
```

## Contributing

Helpful contributions:

- clearer examples
- small demo datapacks
- before/after comparisons
- docs fixes
- bug reports from trying the beta site
- requests for missing Minecraft commands

Open an issue if something is confusing, broken, or missing.
