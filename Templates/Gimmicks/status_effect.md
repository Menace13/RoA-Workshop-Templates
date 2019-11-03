# Status Effect Template
### Created by Menace13

## Description
This template goes in depth into what you can do with your own custom status effect. The examples are 
- [x] Creating your status effect
- [x] The basics of applying and removing your status effect
- [ ] Displaying your status effect visually
- [ ] Various types of status effects
- - [ ] Dealing damage over time
- - [ ] Increasing the power of your moves that hit afflicted opponents
- - [ ] Status effects that wear off over time
- - [ ] Other undecided examples
- [ ] *[Your suggestion here]*

# Scripts

## Creating your status effect

### other_init.gml (REQUIRED)

The most important part of a status effect is that the game remembers it's there. Declaring your status effect and any related variables in `other_init.gml` will ensure the game remembers it between frames. Because it is declared for each other character, it also keeps track of it separately for each opponent. The `MY_STATUS_EFFECT_TIMER` variable is optional; it is used if you want the status effect to do things on a set timer or if you want it to wear off after some amount of time.

**You should always choose a unique name for your status effect. DO NOT use these default names.** For example, if I was making a Rivals version of Toxicroak with a poison status effect, I would name the status effect `toxicroak_poison`. If I only named it `poison`, it might conflict with someone else's poison effect, and then if either of us applied the status effect both of our characters would see `poison == true` even though it's not the same poison effect. Throughout this entire template, you should replace *any* code that says `MY_STATUS_EFFECT`, `MY_STATUS_EFFECT_TIMER`, or `MY_STATUS_EFFECT_TIMER` with variable names that are unique to your character.

#### Code
```
// other_init.gml

MY_STATUS_EFFECT = false;
MY_STATUS_EFFECT_TIMER = 0; // optional
```

### init.gml (REQUIRED)

Declaring the same status effect code in `init.gml` is important in case your status effect can be applied to yourself (e.g. parrying Zetterburn's fireball).

#### Code
```
// init.gml

MY_STATUS_EFFECT = false;
MY_STATUS_EFFECT_TIMER = 0; // optional
```

## The basics of applying and removing your status effect

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
    hit_player_obj.MY_STATUS_EFFECT_ID = id; // optional, but recommended
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

Most status effects are removed when the opponent parries your attack, but it's not mandatory. You can also choose to have your status effect only be removed if they parry a physical attack of yours, not a projectile attack. To do that, simply add `&& my_hitboxID.type == 1` inside the if-statement.

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

Work in progress

