# Double Up Special (with Orcahop) Template
### Created by Menace13

## Description

This template allows up special to be used twice before going into pratfall, and includes code to prevent your character from being able to double jump -> up special repeatedly, e.g. how Orcane can only Orcahop once per airtime.

Placeholders you will need to replace:

Placeholder       | Example | Description
----------------- | ------- | -----------
[FINAL_WINDOW]    | 2       | The window number of the final window for the attack you are editing. If there is no hitbox, this will probably be 2. If there is a hitbox, this will probably be 3 or higher.
[VSP_DELAY]       | 8       | The number of frames to delay the speed boost that up special gives / the length of window 1.

# Scripts

## uspecial.gml

These lines set up the base requirements for the up special.
- The first window is set to 8 frames long, so that double jump + up special doesn't go *too* high (it gives double jump 8 frames to lose speed because of gravity before the up special height boost happens).
- The second window is set to `AG_WINDOW_TYPE` 0 as a base, which means it doesn't put the player in pratfall, but we will change that later in `attack_update.gml`.
- The second window also is given `AG_WINDOW_VSPEED` of -15 and `AG_WINDOW_VSPEED_TYPE` of 0, which says "on the first frame of the second window of up special, subtract 15 from the player's vsp" (negative vsp = going up, positive vsp = going down).

**NOTE: If you used a full character as a base, this file already exists, and the following lines probably also already exist, you just have to edit them.**

#### Code

Placeholders to replace (2): `[VSP_DELAY]`, `[FINAL_WINDOW]`

```
// uspecial.gml

set_window_value(AT_USPECIAL, 1, AG_WINDOW_LENGTH, [VSP_DELAY]);

set_window_value(AT_USPECIAL, 2, AG_WINDOW_TYPE, 0);
set_window_value(AT_USPECIAL, 2, AG_WINDOW_VSPEED, -15); // can be changed to your desired speed boost

set_window_value(AT_USPECIAL, [FINAL_WINDOW], AG_WINDOW_VSPEED_TYPE, 0);
```

## init.gml

Creating two boolean variables to remember whether we already used our up special / double jump.
- We need to remember if up special was used so we can put the player in pratfall after the next one.
- We need to remember if double jump was used because normally the player gets their double jump back if they up special immediately after a double jump. We don't want that, because then they can Orcahop multiple times in one recovery, which lets them go really high really safely.

#### Code

```
// init.gml

used_uspecial = false;
used_djump = false;
```

## attack_update.gml

Here's where most of the magic happens.
- First, we ensure that the player isn't falling too fast (in our case, falling faster than 1 pixel per frame) during the startup.
- Second, on the first frame of the second window (where the speed boost happens, as set up in `uspecial.gml`).

#### Code

Placeholders to replace (2): `[FINAL_WINDOW]`, `[FINAL_WINDOW]`

```
// attack_update.gml

if (attack == AT_USPECIAL) {
	if (window == 1) {   // checked each frame during window 1 to prevent falling but not prevent rising
		if(vsp > 1) {    // can be changed to your desired max fall speed
			vsp = 1;     // should be equal to the number in the line above
		}
	}
	if (window == 2 && window_timer == 1) {
		if (used_djump) {
			djumps = 1;  // remove double jump if they have entered PS_DOUBLE_JUMP since touching the ground
		}
		if (used_uspecial){
			set_window_value(AT_USPECIAL, [FINAL_WINDOW], AG_WINDOW_TYPE, 7);
		} else {
			set_window_value(AT_USPECIAL, [FINAL_WINDOW], AG_WINDOW_TYPE, 0);
			used_uspecial = true;
		}
	}
}
```

## update.gml

This code is necessary to set the values of `used_uspecial` and `used_djump` when in specific states

#### Code

```
// update.gml

// allow the player to use two up specials again when wall jumping / in hitstun
if (state == PS_WALL_JUMP || state == PS_HITSTUN) {
	used_uspecial = false;
}

// allow the player to use two up specials and Orcahop again when on the ground again
if (!free) {
	used_djump = false;
	used_uspecial = false;
}

// remember when the player entered the double jump state
if (state == PS_DOUBLE_JUMP) {
	used_djump = true;
}
```

## debug_draw.gml

In case you have issues / want to see the values of the affected variables in-game. Either delete this file or comment out all statements in the file before publishing your character.

#### Code

```
// debug_draw.gml

draw_debug_text(x,y,"used_uspecial: " + string(used_uspecial));
draw_debug_text(x,y+12,"used_djump: " + string(used_djump));
draw_debug_text(x,y+24,"djumps: " + string(djumps));
```
