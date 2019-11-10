# Status Effect Template
### Created by Menace13

## Description
This template goes in depth into what you can do with your own custom status effect.

##Table of Contents
- [x] [Creating your status effect](#creating-your-status-effect)
- [x] [Applying and removing your status effect](#applying-and-removing-your-status-effect)
- [ ] [Displaying your status effect visually](#displaying-your-status-effect-visually)
- [x] [Various types of status effects](#various-types-of-status-effects)
- - [x] [Damage over time and status effects that wear off over time](#damage-over-time-and-status-effects-that-wear-off-over-time)
- - [x] [Increasing the power of your moves that hit afflicted opponents](#increasing-the-power-of-your-moves-that-hit-afflicted-opponents)
- - [ ] Other undecided examples
- [ ] *[Your suggestion here]*

# Scripts

## Creating your status effect

### other_init.gml (REQUIRED)

The most important part of a status effect is that the game remembers it's there. Declaring your status effect and any related variables in `other_init.gml` will ensure the game remembers it between frames. Because it is declared for each other character, it also keeps track of it separately for each opponent.

**You should always choose a unique name for your status effect. DO NOT use these default names.** For example, if I was making a Rivals version of Toxicroak with a poison status effect, I would name the status effect `toxicroak_poison`. If I only named it `poison`, it might conflict with someone else's poison effect, and then if either of us applied the status effect both of our characters would see `poison == true` even though it's not the same poison effect. Throughout this entire template, you should replace *any* code that says `MY_STATUS_EFFECT`, `MY_STATUS_EFFECT_ID`, or `MY_STATUS_EFFECT_TIMER` with variable names that are unique to your character.

#### Code
```
// other_init.gml

MY_STATUS_EFFECT = false;
```

### init.gml (REQUIRED)

Declaring the same status effect code in `init.gml` is important in case your status effect can be applied to yourself (e.g. parrying Zetterburn's fireball).

#### Code
```
// init.gml

MY_STATUS_EFFECT = false;
```

## Applying and removing your status effect

### hit_player.gml (REQUIRED) 

There are a few options for how you want to afflict your opponent with status effects, but the most common one is by hitting the opponent. The example code here (and in most of this template) is based on Guadua's `bambood` status effect. Let's break it down. First, it's important to remember that `hit_player.gml` only runs when your hitbox hits an opponent. The moves this code checks for are, in order:
 - Up-special, but only if it is type 2 (i.e. a projectile-type hitbox; Guadua's tether hitbox does not apply `bambood` to the opponent)
 - Down-strong
 - Down-special on the ground
 - Down-special in the air

Next, if the move you hit with was one of those four, it does these actions:
 - First, it checks if the opponent already has `MY_STATUS_EFFECT`. If they do not, it plays a sound effect to make it clear that they got the status effect. In this case, it plays the `mark.ogg` file from this character's `sounds` folder. If you wanted to use a built-in sound effect, replace the indicated line of code with `sound_play(asset_get("sfx_burnapplied"))` (and replace "sfx_burnapplied" with whatever sound effect you want from [this list](https://www.rivalsofaether.com/workshop/sfx-list/)).
 - Second, it sets `MY_STATUS_EFFECT` to true for the opponent that was hit.
 - Third, it creates a new instance variable for the opponent that was hit, `MY_STATUS_EFFECT_ID`, that remembers the ID of the player who afflicted them with the status effect. This isn't required, but it's recommended so you can remove your status effect from opponents when you die, remove the status effect from an opponent who parried you, draw effects to show that they are afflicted with the status, etc.
 - Finally, if you are using a timer, whether to make the status effect wear off or to deal damage over time, you should initialize the `MY_STATUS_EFFECT_TIMER` value now. My example sets it to 600, meaning it will last 10 seconds. If you want it to last forever, but still need a timer, I recommend setting the timer to 0 and having it count up instead of down.

#### Code
```
// hit_player.gml

/* change the if-statement as desired for the moves you want to apply the
 * status effect to the opponent - add more specific requirements, such as
 * the first example here, if the move has hitboxes you don't want to include
 */
if ((my_hitboxID.attack == AT_USPECIAL && my_hitboxID.type == 2)
|| my_hitboxID.attack == AT_DSTRONG || my_hitboxID.attack == AT_DSPECIAL
|| my_hitboxID.attack == AT_DSPECIAL_AIR){
    if (!hit_player_obj.MY_STATUS_EFFECT) {
        sound_play(sound_get("mark")); // replace if using built-in SFX
    }
	hit_player_obj.MY_STATUS_EFFECT = true;
    hit_player_obj.MY_STATUS_EFFECT_ID = id; // optional, but HIGHLY recommended
	
	// the next variable is used for status effects that wear off or that do something based on a timer (e.g. Zetterburn fire)
	// replace 600 with the duration of your status effect before it wears off
	// replace 600 with 0 if you want the timer to count up from 0 and never wear off
	// (see "Various types of status effects" for more information)
	hit_player_obj.MY_STATUS_EFFECT_TIMER = 600;
}
```

### update.gml (HIGHLY recommended)

The `update.gml` script has enough separate snippets of code that I have to break it into separate pieces. This first code snippet is for removing your status effect from opponents when they die and respawn.

#### Code
```
// update.gml

with (asset_get("oPlayer")){
    if (state == PS_RESPAWN){
        MY_STATUS_EFFECT = false;
    }
}
```

### death.gml (recommended)

This code simply removes the status effect from any player who received it from the player who just died. For an example, there are two Maypuls, Maypul A and Maypul B. Maypul A marked Zetterburn, while Maypul B marked Forsburn. If Maypul A dies, Zetterburn's mark will be removed, but Forsburn's mark will not be removed.

#### Code
```
// death.gml

with (asset_get("oPlayer")) {
	if (MY_STATUS_EFFECT && MY_STATUS_EFFECT_ID == other.id) {
		MY_STATUS_EFFECT = false;
	}
}
```

### got_parried.gml (recommended)

Most status effects are removed when the opponent parries your attack, but it's not mandatory. You can also choose to have your status effect only be removed if they parry a physical attack of yours, not a projectile attack. To do that, simply add `&& my_hitboxID.type == 1` inside the if-statement. *Note: This same code can be used in `got_hit.gml` to remove the status effect from any opponent who hits you.*

#### Code
```
// got_parried.gml

if (hit_player_obj.MY_STATUS_EFFECT && hit_player_obj.MY_STATUS_EFFECT_ID == id){
    hit_player_obj.MY_STATUS_EFFECT = false;
}
```

## Displaying your status effect visually

Work in progress

## Various types of status effects

### Damage over time and status effects that wear off over time

Status effects that deal damage over time and status effects that wear off over time have a lot of similarities, most notably that they fit within the same statements in `update.gml` and both require the use of a timer. Here's what this code does:
 - With each opponent, it checks if they have the status effect, then makes sure the status effect was applied by you, then makes sure they are not in hitpause. Zetterburn fire sets the precedent here, because it does *not* count down while the opponent is in hitpause, although any Zetterburn can consume another Zetterburn's fire.
 - After succeeding the if-statement, it subtracts 1 from the timer. It subtracts 1 first because we don't want to deal damage when the timer is at 600 (i.e. frame 1 of them being burned). If you don't want the status effect to be removed upon the timer reaching 0, and you initialize the timer to 0 in `hit_player.gml` earlier, change `-=` to `+=`.
 - Second, if the `MY_STATUS_EFFECT_TIMER` modulus 60 is zero (meaning, if the remainder of dividing `MY_STATUS_EFFECT_TIMER` by 60 is zero), we deal 1 damage to the opponent. Increasing 60 will make the status effect deal damage less often; decreasing 60 will make the status effect deal damage more often. Next, upon evaluating true for that if-statement, it will deal 1 damage to the opponent. You can change the damage value, but I highly recommend not using a decimal value here. If you don't want to deal damage over time, you can just delete this line of code.
 - Finally, if `MY_STATUS_EFFECT_TIMER` equals zero, it removes the status effect from the opponent. If you don't want the status effect to be removed after a certain duration, you can just delete this line of code.

#### update.gml
```
// update.gml

with (asset_get("oPlayer")) {
	if (MY_STATUS_EFFECT && MY_STATUS_EFFECT_ID == other.id && !hitpause) {
		// decreases the timer by 1 each frame
		// replace -= with += if you don't want the status effect to wear off so it counts up repeatedly instead of counting down
		MY_STATUS_EFFECT_TIMER -= 1;
		
		// delete this line if you don't want the status effect to do damage
		// replace the 60 with how many frames there should be between ticks of damage, e.g. 60 = 1 damage per second
		// replace the 1 with how many damage you want the player to take each tick of damage
		if (MY_STATUS_EFFECT_TIMER % 60 == 0) take_damage(player, other.player, 1);
		
		// delete this line if you don't want the status effect to wear off when the timer reaches zero
		if (MY_STATUS_EFFECT_TIMER == 0) MY_STATUS_EFFECT = false;
	}
}
```

### Increasing the power of your moves that hit afflicted opponents

There are two common methods of increasing a move's power. The first, lazier method is to simply change the damage and knockback the opponent takes when you hit an opponent with the status effect. *Please don't do this method unless you really know what you're doing and know why you want to. I ***will not*** help you with it.* The second, smarter method is to have the move have extra windows with extra hitboxes and making the move only enter those windows if the opponent has the status effect. That's what I'll show in this template, taken almost entirely from Guadua's neutral special's code.

#### nspecial.gml

First we have to create the extra windows / extra hitbox for the strong hit. Here is an edited version of Guadua's `nspecial.gml` (edited to remove the window that is only used when using neutral special to eat a bamboo stick that is stuck in the ground (i.e. Guadua's article1)), along with a breakdown of the important parts:
 - `set_attack_value(AT_NSPECIAL, AG_NUM_WINDOWS, 3);` - this is one of the most important lines in the file. Because we tell the game that the move only has three windows, it will end the move after window 3 instead of going to window 4. Because of this, we will have to manually move to windows 4 and 5 using `attack_update.gml`, but that's still preferable to having to manually end the move after window 3.
 - Window 1 is the startup
 - Window 2 is first hitbox
 - Window 3 is the standard endlag, for when not hitting a marked opponent
 - Window 4 is the second hitbox, which we have to manually move to
 - Window 5 is the second endlag, which we need because it has a different animation (and, in Guadua's case, it also plays a special sound effect)

```
// nspecial.gml

set_attack_value(AT_NSPECIAL, AG_CATEGORY, 2);
set_attack_value(AT_NSPECIAL, AG_SPRITE, sprite_get("nspecial"));
set_attack_value(AT_NSPECIAL, AG_NUM_WINDOWS, 3);
set_attack_value(AT_NSPECIAL, AG_AIR_SPRITE, sprite_get("nspecial_air"));
set_attack_value(AT_NSPECIAL, AG_HURTBOX_SPRITE, sprite_get("nspecial_hurt"));

set_window_value(AT_NSPECIAL, 1, AG_WINDOW_LENGTH, 6);
set_window_value(AT_NSPECIAL, 1, AG_WINDOW_ANIM_FRAMES, 2);
set_window_value(AT_NSPECIAL, 1, AG_WINDOW_HAS_SFX, 1);
set_window_value(AT_NSPECIAL, 1, AG_WINDOW_SFX, asset_get("sfx_bite"));
set_window_value(AT_NSPECIAL, 1, AG_WINDOW_SFX_FRAME, 4);

set_window_value(AT_NSPECIAL, 2, AG_WINDOW_LENGTH, 4);
set_window_value(AT_NSPECIAL, 2, AG_WINDOW_ANIM_FRAMES, 1);
set_window_value(AT_NSPECIAL, 2, AG_WINDOW_ANIM_FRAME_START, 2);

set_window_value(AT_NSPECIAL, 3, AG_WINDOW_LENGTH, 15);
set_window_value(AT_NSPECIAL, 3, AG_WINDOW_ANIM_FRAMES, 3);
set_window_value(AT_NSPECIAL, 3, AG_WINDOW_ANIM_FRAME_START, 3);

set_window_value(AT_NSPECIAL, 4, AG_WINDOW_LENGTH, 2);
set_window_value(AT_NSPECIAL, 4, AG_WINDOW_ANIM_FRAMES, 1);
set_window_value(AT_NSPECIAL, 4, AG_WINDOW_ANIM_FRAME_START, 2);

set_window_value(AT_NSPECIAL, 5, AG_WINDOW_LENGTH, 15);
set_window_value(AT_NSPECIAL, 5, AG_WINDOW_ANIM_FRAMES, 7);
set_window_value(AT_NSPECIAL, 5, AG_WINDOW_ANIM_FRAME_START, 6);
set_window_value(AT_NSPECIAL, 5, AG_WINDOW_HAS_SFX, 1);
set_window_value(AT_NSPECIAL, 5, AG_WINDOW_SFX, sound_get("heal"));
set_window_value(AT_NSPECIAL, 5, AG_WINDOW_SFX_FRAME, 7);

set_num_hitboxes(AT_NSPECIAL, 2);

set_hitbox_value(AT_NSPECIAL, 1, HG_HITBOX_TYPE, 1);
set_hitbox_value(AT_NSPECIAL, 1, HG_WINDOW, 2);
set_hitbox_value(AT_NSPECIAL, 1, HG_LIFETIME, 4);
set_hitbox_value(AT_NSPECIAL, 1, HG_HITBOX_X, 39);
set_hitbox_value(AT_NSPECIAL, 1, HG_HITBOX_Y, -39);
set_hitbox_value(AT_NSPECIAL, 1, HG_WIDTH, 58);
set_hitbox_value(AT_NSPECIAL, 1, HG_HEIGHT, 50);
set_hitbox_value(AT_NSPECIAL, 1, HG_PRIORITY, 3);
set_hitbox_value(AT_NSPECIAL, 1, HG_DAMAGE, 8);
set_hitbox_value(AT_NSPECIAL, 1, HG_ANGLE, 361);
set_hitbox_value(AT_NSPECIAL, 1, HG_BASE_KNOCKBACK, 5);
set_hitbox_value(AT_NSPECIAL, 1, HG_KNOCKBACK_SCALING, 0.9);
set_hitbox_value(AT_NSPECIAL, 1, HG_BASE_HITPAUSE, 5);
set_hitbox_value(AT_NSPECIAL, 1, HG_HITPAUSE_SCALING, 1.0);
set_hitbox_value(AT_NSPECIAL, 1, HG_EXTRA_HITPAUSE, 1.0);
set_hitbox_value(AT_NSPECIAL, 1, HG_HIT_SFX, asset_get("sfx_crunch"));

set_hitbox_value(AT_NSPECIAL, 2, HG_HITBOX_TYPE, 1);
set_hitbox_value(AT_NSPECIAL, 2, HG_WINDOW, 4);
set_hitbox_value(AT_NSPECIAL, 2, HG_LIFETIME, 2);
set_hitbox_value(AT_NSPECIAL, 2, HG_HITBOX_X, 39);
set_hitbox_value(AT_NSPECIAL, 2, HG_HITBOX_Y, -39);
set_hitbox_value(AT_NSPECIAL, 2, HG_WIDTH, 58);
set_hitbox_value(AT_NSPECIAL, 2, HG_HEIGHT, 50);
set_hitbox_value(AT_NSPECIAL, 2, HG_PRIORITY, 3);
set_hitbox_value(AT_NSPECIAL, 2, HG_DAMAGE, 4);
set_hitbox_value(AT_NSPECIAL, 2, HG_ANGLE, 361);
set_hitbox_value(AT_NSPECIAL, 2, HG_BASE_KNOCKBACK, 6);
set_hitbox_value(AT_NSPECIAL, 2, HG_KNOCKBACK_SCALING, 1.0);
set_hitbox_value(AT_NSPECIAL, 2, HG_BASE_HITPAUSE, 16);
set_hitbox_value(AT_NSPECIAL, 2, HG_HITPAUSE_SCALING, 0);
set_hitbox_value(AT_NSPECIAL, 2, HG_VISUAL_EFFECT, 1);
set_hitbox_value(AT_NSPECIAL, 2, HG_HITBOX_GROUP, 1);
set_hitbox_value(AT_NSPECIAL, 2, HG_HIT_SFX, asset_get("sfx_syl_ustrong_part2"));
```

#### init.gml

We need our character to have an instance variable that remembers if we have consumed our status effect so we can remember that information when we move between scripts. We will set this to true in `hit_player.gml` then read it later in `attack_update.gml`.

```
// init.gml

MY_STATUS_EFFECT_CONSUMED = false;
```

#### hit_player.gml

This is the code to check if the opponent has your status effect when hitting them with your neutral special. First, if you did in fact previously apply your status effect to them, and you are hitting them with hitbox number 1 of your neutral special, it will set `MY_STATUS_EFFECT_CONSUMED` to true, so that you can send that information to `attack_update.gml` later. Then it sets `hit_player_obj.should_make_shockwave` to false, preventing the first hitbox from ever causing a galaxy effect on an opponent who has your status effect so the second hitbox can do it instead. Finally, if they are being hit by hitbox number 2 instead of hitbox number 1, it removes the status effect from them (because they are already being hit by the second hitbox).

```
// hit_player.gml

if (my_hitboxID.attack == AT_NSPECIAL && hit_player_obj.MY_STATUS_EFFECT && hit_player_obj.MY_STATUS_EFFECT_ID == id){
    if (my_hitboxID.hbox_num == 1){
        MY_STATUS_EFFECT_CONSUMED = true;
        hit_player_obj.should_make_shockwave = false;
    } else {
		hit_player_obj.MY_STATUS_EFFECT = false;
	}
}
```

#### attack_update.gml

Here's where we ensure the second hitbox successfully hits a marked opponent and manually force the move to move between windows beyond its `AG_NUM_WINDOWS` value. A breakdown of what this does by window:
 - Window 1: On the first frame, it sets `MY_STATUS_EFFECT_CONSUMED` to false, so it will never incorrectly think it hit a player who had the status effect.
 - Window 2: The first part of this is special code that I directly copied from Guadua that will move a hit, marked opponent towards where the second hitbox spawns during hitpause, but only if Guadua is not in hitstun (i.e. the move traded) and the opponent is not frozen. You may have to edit this some depending on what else you change and where your hitbox spawns, but a lot of it can be done with trial and error if you can't figure it out using math. The second part of window 2 checks for when you are out of hitpause, and as soon as you are, if you consumed your status effect from an opponent, it will immediately move to window 4, and reset the window_timer to 0.
 - Window 4: On the last frame of the window, we manually move to window 5 and reset the window_timer to 0 again, so the move doesn't automatically end (because, as mentioned in the part about `nspecial.gml`, it thinks the move should automatically end after window 3).
 - Windows 3 and 5 don't need special code here because they are simple endlag windows.

```
// attack_update.gml

if (attack == AT_NSPECIAL){
    if (window == 1 && window_timer == 1)
        MY_STATUS_EFFECT_CONSUMED = false;
    if (window == 2){
        // this code moves the player toward the 2nd hitbox, guaranteeing that it hits
        with (asset_get("oPlayer")) {
            if (hitpause && MY_STATUS_EFFECT && MY_STATUS_EFFECT_ID == other.id && state_cat == SC_HITSTUN && hit_player_obj == other.id
            && last_attack == other.attack && state != PS_FROZEN){
                x += (other.x+40*other.spr_dir - x)/5; // pulls the opponent to 40 pixels in front of you
                if (free){
                    y += (other.y - y) / 5; // pulls an airborne to your height
                }
            }
        }
        if (!hitpause && MY_STATUS_EFFECT_CONSUMED){
            window = 4;
            window_timer = 0;
        }
    }
    if (window == 4 && window_timer == get_window_value(attack, 4, AG_WINDOW_LENGTH)){
        window = 5;
        window_timer = 0;
    }
}
```
