# Multi-Hit Projectile Template (WIP)
### Created by Mawral
*Currently in testing. Code has yet to be tidied and stress-tested. Not recommended for use in your released characters just yet.
# Description

This advanced template can be used to create a projectile that hits opponents multiple times.

This template uses zero articles, is Parry-safe and Bash-safe, and can be customized from just the attack's grid-indexes file.

In this template, `AT_NSPECIAL` is used for the projectile attack, but it can be changed to any other attack index.


# Creating the Projectile attack

If you just need a template to copy and paste, look no further:

<details>
  <summary>Example 'Multihit Projectile' example attack - nspecial.gml</summary>
 
  ```GML
//attacks/nspecial.gml
//a minimal 'multihit projectile' attack example. It fires a projectile that hits 5 times, then spawns a second projectile for its final hit. 
//You can copy-paste this into your project to quickly test this template.

//attacks/nspecial.gml
//a minimal 'multihit projectile' attack example. 
//You can copy-paste this into your project to quickly test this template.

set_attack_value(AT_NSPECIAL, AG_CATEGORY, 2);
set_attack_value(AT_NSPECIAL, AG_SPRITE, sprite_get("nspecial"));
set_attack_value(AT_NSPECIAL, AG_HURTBOX_SPRITE, sprite_get("nspecial_hurt"));
set_attack_value(AT_NSPECIAL, AG_NUM_WINDOWS, 5);

//startup
set_window_value(AT_NSPECIAL, 1, AG_WINDOW_LENGTH, 18);
set_window_value(AT_NSPECIAL, 1, AG_WINDOW_ANIM_FRAMES, 1);
set_window_value(AT_NSPECIAL, 2, AG_WINDOW_ANIM_FRAME_START, 0);
//shoot projectile.
set_window_value(AT_NSPECIAL, 2, AG_WINDOW_LENGTH, 4); 
set_window_value(AT_NSPECIAL, 2, AG_WINDOW_ANIM_FRAMES, 1);
set_window_value(AT_NSPECIAL, 2, AG_WINDOW_ANIM_FRAME_START, 1);
//recovery
set_window_value(AT_NSPECIAL, 3, AG_WINDOW_LENGTH, 20);
set_window_value(AT_NSPECIAL, 3, AG_WINDOW_ANIM_FRAMES, 1);
set_window_value(AT_NSPECIAL, 3, AG_WINDOW_ANIM_FRAME_START, 2);

set_num_hitboxes(AT_NSPECIAL, 2);

//define some custom hitbox grid indexes for multihit attacks.
#macro HG_MULTIHIT_COUNT 70
#macro HG_MULTIHIT_DELAY 71
#macro HG_MULTIHIT_MAGNET_STRENGTH 72
#macro HG_MULTIHIT_FINAL_HITBOX_NUM 73
#macro HG_MULTIHIT_FINAL_HITBOX_EFFECT 74
#macro HG_MULTIHIT_PERSISTENT 75
#macro HG_MULTIHIT_CAP_SPEED_ON_HIT 76

//multi-hit hitbox
set_hitbox_value(AT_NSPECIAL, 1, HG_HITBOX_TYPE, 2);
set_hitbox_value(AT_NSPECIAL, 1, HG_WINDOW, 2);
set_hitbox_value(AT_NSPECIAL, 1, HG_LIFETIME, 60);
set_hitbox_value(AT_NSPECIAL, 1, HG_HITBOX_X, 0);
set_hitbox_value(AT_NSPECIAL, 1, HG_HITBOX_Y, -20);
set_hitbox_value(AT_NSPECIAL, 1, HG_WIDTH, 48);
set_hitbox_value(AT_NSPECIAL, 1, HG_HEIGHT, 32);
set_hitbox_value(AT_NSPECIAL, 1, HG_PRIORITY, 5); //must not be set to '1'.

set_hitbox_value(AT_NSPECIAL, 1, HG_DAMAGE, 1);
set_hitbox_value(AT_NSPECIAL, 1, HG_BASE_HITPAUSE, 3);
set_hitbox_value(AT_NSPECIAL, 1, HG_EXTRA_HITPAUSE, 0); 
set_hitbox_value(AT_NSPECIAL, 1, HG_BASE_KNOCKBACK, 5);
set_hitbox_value(AT_NSPECIAL, 1, HG_KNOCKBACK_SCALING, 0);
set_hitbox_value(AT_NSPECIAL, 1, HG_ANGLE, 45);
set_hitbox_value(AT_NSPECIAL, 1, HG_ANGLE_FLIPPER, 9); //angle flipper 9 hits the opponent towards the center of the projectile.

set_hitbox_value(AT_NSPECIAL, 1, HG_HIT_SFX, asset_get("sfx_blow_weak2"));
set_hitbox_value(AT_NSPECIAL, 1, HG_PROJECTILE_SPRITE, sprite_get("nspecial_proj"));
set_hitbox_value(AT_NSPECIAL, 1, HG_PROJECTILE_MASK, -1); 
set_hitbox_value(AT_NSPECIAL, 1, HG_PROJECTILE_ANIM_SPEED, 0.25); 
set_hitbox_value(AT_NSPECIAL, 1, HG_PROJECTILE_HSPEED, 6); 
set_hitbox_value(AT_NSPECIAL, 1, HG_PROJECTILE_VSPEED, 0); 

//this grid index is important - the multihit won't work if the projectile is destroyed on player contact.
set_hitbox_value(AT_NSPECIAL, 1, HG_PROJECTILE_ENEMY_BEHAVIOR, 1); 
//you may or may not want the opponent to be able to tech out of the middle of the multihit.
set_hitbox_value(AT_NSPECIAL, 1, HG_TECHABLE, 1); 
//sometimes you may want it to force-flinch instead.
set_hitbox_value(AT_NSPECIAL, 1, HG_FORCE_FLINCH, 1); 
//reduced SDI can help lessen the chance of opponents escaping the multihit projectile.
set_hitbox_value(AT_NSPECIAL, 1, HG_SDI_MULTIPLIER, 0.5);
//most projectiles have lower hitstun than normal. It's also a good idea for multihits.
set_hitbox_value(AT_NSPECIAL, 1, HG_HITSTUN_MULTIPLIER, 0.75); 

//custom grid indexes for multi-hits.
set_hitbox_value(AT_NSPECIAL, 1, HG_MULTIHIT_COUNT, 5);                 //hit 5 times.
set_hitbox_value(AT_NSPECIAL, 1, HG_MULTIHIT_DELAY, 3);                 //3 frame delay (plus hitpause) between each hit.
set_hitbox_value(AT_NSPECIAL, 1, HG_MULTIHIT_MAGNET_STRENGTH, 0.25);    //25% 'magnet' strength. adjust based on preference.

set_hitbox_value(AT_NSPECIAL, 1, HG_MULTIHIT_FINAL_HITBOX_NUM, 2);      //spawn 'final' hitbox #2, after the maximum number of hits.
set_hitbox_value(AT_NSPECIAL, 1, HG_MULTIHIT_FINAL_HITBOX_EFFECT, HFX_GEN_BIG); //spawn a big vfx after the max number of hits.


//'final' hitbox.
//this is an optional projectile, that spawns after the multihit projectile deals its maximum number of hits.
set_hitbox_value(AT_NSPECIAL, 2, HG_HITBOX_TYPE, 2);
set_hitbox_value(AT_NSPECIAL, 2, HG_WINDOW, 99); //this hitbox will never spawn naturally; it will only spawn at the end of the multi-hit projectile. 
set_hitbox_value(AT_NSPECIAL, 2, HG_WINDOW_CREATION_FRAME, 0);
set_hitbox_value(AT_NSPECIAL, 2, HG_LIFETIME, 2);
set_hitbox_value(AT_NSPECIAL, 2, HG_HITBOX_X, 0);
set_hitbox_value(AT_NSPECIAL, 2, HG_HITBOX_Y, -30);
set_hitbox_value(AT_NSPECIAL, 2, HG_WIDTH, 64);
set_hitbox_value(AT_NSPECIAL, 2, HG_HEIGHT, 64);
set_hitbox_value(AT_NSPECIAL, 2, HG_SHAPE, 0);
set_hitbox_value(AT_NSPECIAL, 2, HG_PRIORITY, 8); 

set_hitbox_value(AT_NSPECIAL, 2, HG_DAMAGE, 5);
set_hitbox_value(AT_NSPECIAL, 2, HG_BASE_HITPAUSE, 9);
set_hitbox_value(AT_NSPECIAL, 2, HG_HITPAUSE_SCALING, 0.5);
set_hitbox_value(AT_NSPECIAL, 2, HG_BASE_KNOCKBACK, 8); 
set_hitbox_value(AT_NSPECIAL, 2, HG_KNOCKBACK_SCALING, 0.75); 
set_hitbox_value(AT_NSPECIAL, 2, HG_ANGLE, 50);

set_hitbox_value(AT_NSPECIAL, 2, HG_HIT_SFX, asset_get("sfx_blow_medium2"));
set_hitbox_value(AT_NSPECIAL, 2, HG_VISUAL_EFFECT, 0);
set_hitbox_value(AT_NSPECIAL, 2, HG_HIT_LOCKOUT, 1);

set_hitbox_value(AT_NSPECIAL, 2, HG_PROJECTILE_SPRITE, asset_get("empty_sprite"));
set_hitbox_value(AT_NSPECIAL, 2, HG_PROJECTILE_ANIM_SPEED, 1); 
set_hitbox_value(AT_NSPECIAL, 2, HG_PROJECTILE_MASK, -1);

set_hitbox_value(AT_NSPECIAL, 2, HG_PROJECTILE_IS_TRANSCENDENT, 1); //you usually want the final hitbox to go through other hitboxes.
set_hitbox_value(AT_NSPECIAL, 2, HG_HITSTUN_MULTIPLIER, 0.75); 
  ```
</details>

For a more explained walkthrough of how to add multihits to your projectile code, read on from here.

<details>
  <summary>Multi-hit projectile - Grid Indexes Setup</summary>

## Set up a normal Projectile attack

Set up an attack like normal, with a single projectile hitbox. The only requirements for this hitbox are that `HG_PROJECTILE_ENEMY_BEHAVIOR` is set to `1` (so that the projectile does not disappear when hitting a player once), and that `HG_PRIORITY` is larger than 1 (to avoid any code interactions with Kragg rock shards). It is also recommended to give the attack a lower `HG_SDI_MULTIPLIER` and `HG_HITSTUN_MULTIPLIER`.

## Define 'custom' Grid Indexes
This template uses 'custom-made' Grid Indexes for easy editing. Add the following indexes to your attack script.

 ```GML
//attacks/nspecial.gml
#macro HG_MULTIHIT_COUNT 70
#macro HG_MULTIHIT_DELAY 71
#macro HG_MULTIHIT_MAGNET_STRENGTH 72
#macro HG_MULTIHIT_FINAL_HITBOX_NUM 73
#macro HG_MULTIHIT_FINAL_HITBOX_EFFECT 74
#macro HG_MULTIHIT_PERSISTENT 75
#macro HG_MULTIHIT_CAP_SPEED_ON_HIT 76
```
The values in these Grid Indexes will be accessed in `hitbox_init.gml` and `hitbox_update.gml` to make the multi-hit function.

## Define 'custom' Grid Indexes
This template requires two custom grid indexes to be set. Add these to your hitbox.
`HG_MULTIHIT_COUNT` defines the number of times the projectile will hit. If set to zero, the projectile will hit repeatedly until its Lifetime ends.
`HG_MULTIHIT_DELAY` defines the amount of time (in frames) between each hit. This number is *in addition* to the amount of Hitpause the projectile has. I recommend setting both `HG_HITPAUSE` and `HG_MULTIHIT_DELAY` to a small value.

```GML
//attacks/nspecial.gml
//for example
set_hitbox_value(AT_NSPECIAL, 1, HG_MULTIHIT_COUNT, 5); 
set_hitbox_value(AT_NSPECIAL, 1, HG_MULTIHIT_DELAY, 3); 
set_hitbox_value(AT_NSPECIAL, 1, HG_MULTIHIT_MAGNET_STRENGTH, 0.25); 
```

## Optional: Add a second, 'final' hitbox
If desired, this template supports having a second hitbox spawn after the multi-hit projectile hits a maximum number of times. You can use this to make a projectile that has a series of weak, repeating hits, leading into a much stronger projectile hit at the end.

Add a second projectile hitbox to your attack. Then, have your first hitbox refer to it using the custom grid index `HG_MULTIHIT_FINAL_HITBOX_NUM`.
You can also have a Visual Effect spawn at the same time as the 'final' hitbox. Specify it using `HG_MULTIHIT_FINAL_HITBOX_EFFECT` if desired.
```GML
//attacks/nspecial.gml
//for example
set_hitbox_value(AT_NSPECIAL, 1, HG_MULTIHIT_FINAL_HITBOX_NUM, 2); 
set_hitbox_value(AT_NSPECIAL, 1, HG_MULTIHIT_FINAL_HITBOX_EFFECT, HFX_GEN_BIG); 
```
## Optional: Use the remaining Grid Indexes
For the sake of being a versatile template, there are three more Grid Indexes that you may wish to use.

`HG_MULTIHIT_MAGNET_STRENGTH` defines how much the opponent should 'stick' to the projectile. The value ranges between 0 and 1. Increase this value if the projectile's hits don't connect well into each other. Decrease it if the attack looks choppy and unnatural.
`HG_MULTIHIT_PERSISTENT`, if set to `true`, will prevent the projectile from disappearing if the opponent escapes some hits of the attack. This is useful for 'crowd control' style projectiles that linger on-screen for a long period of time.
Finally, `HG_MULTIHIT_CAP_SPEED_ON_HIT` will cap the projectile's speed to a given value when it hits a target. This can be used to further help the multi-hits connect together, especially in cases where it would be sped up by Ori's Down-Special or other effects.
```GML
//attacks/nspecial.gml
//for example
set_hitbox_value(AT_NSPECIAL, 1, HG_MULTIHIT_MAGNET_STRENGTH, 0.25);
```

</details>



# Scripts

## `hitbox_init.gml` - 'Initialize Multi-Hit Variables'
Edit the attack index in the top line of code if necessary - `AT_NSPECIAL` is used as an example. If the character has several multi-hit projectiles, just copy the line of code for each attack index.
Functions using the `#define` keyword must be placed at the bottom of the .gml file.

<details>
  <summary>hitbox_init.gml</summary>
	
```GML
//hitbox_init.gml
if (attack == AT_NSPECIAL && hbox_num == 1) multihit_init_script();


#define multihit_init_script
//multi-hit projectile script by Mawral

//ignore kragg rock shards.
if (hit_priority == 1) return; 

//define custom hitbox grid indexes for multihit attacks. 
#macro HG_MULTIHIT_COUNT 70
#macro HG_MULTIHIT_DELAY 71
#macro HG_MULTIHIT_MAGNET_STRENGTH 72
#macro HG_MULTIHIT_FINAL_HITBOX_NUM 73
#macro HG_MULTIHIT_FINAL_HITBOX_EFFECT 74
#macro HG_MULTIHIT_PERSISTENT 75
#macro HG_MULTIHIT_CAP_SPEED_ON_HIT 76

//load into variables.
var atk = attack;
var num = hbox_num;
with (player_id) {
    other.time_between_hits         = get_hitbox_value(atk, num, HG_MULTIHIT_DELAY); 
    other.final_hit_hbox_num        = get_hitbox_value(atk, num, HG_MULTIHIT_FINAL_HITBOX_NUM);
    other.final_hit_vfx             = get_hitbox_value(atk, num, HG_MULTIHIT_FINAL_HITBOX_EFFECT);
    other.proj_magnet_strength      = get_hitbox_value(atk, num, HG_MULTIHIT_MAGNET_STRENGTH);  
    other.maximum_number_of_hits    = get_hitbox_value(atk, num, HG_MULTIHIT_COUNT); 
    other.proj_persist              = get_hitbox_value(atk, num, HG_MULTIHIT_PERSISTENT);
    other.proj_speed_cap            = get_hitbox_value(atk, num, HG_MULTIHIT_CAP_SPEED_ON_HIT);
}

//establish multihit variables.
hit_counter = 0;
reset_can_hit_timer = 0;
hitpause_inflicted = 0;
proj_old_hitpause = hitpause;

//establish hitstop and hitpause variables.
proj_hitstop = 0;
proj_hitpause = 0;
proj_old_hsp = 0;
proj_old_vsp = 0;
proj_old_img_spd = 0;


//record the 'player' variable. if it changes, this indicates that the projectile has been parried.
proj_old_player = player;
   
//save a clone of the 'can_hit' array. the update script uses this to detect when a hit has been registered, and resets it to enable the projectile to hit again. 
initial_can_hit = array_clone(can_hit); 
return;
```
	
</details>


## `hitbox_update.gml` - Multi-Hit Logic
Edit the attack index in the top line of code if necessary - `AT_NSPECIAL` is used as an example.

<details>
  <summary> hitbox_update.gml </summary>
	
```GML
//hitbox_update.gml
if (attack == AT_NSPECIAL && hbox_num == 1) multihit_update_script();

#define multihit_update_script
//projectile multihit script by Mawral.

//ignore kragg rock shards.
if (hit_priority == 1) return; 

//check if Ori has bashed this projectile. if so, end the script.
if (getting_bashed) return;

//handle hitpause.
if (proj_hitpause) {
	proj_hitstop--;
	if (proj_hitstop <= 0) {
		//hitpause has ended. reset all of the movement and animation variables.
		hsp = proj_old_hsp;
		vsp = proj_old_vsp;
		img_spd = proj_old_img_spd;
		proj_hitpause = false;
		
		//if this projectile has hit its maximum number of times, destroy it.
		if (hit_counter >= maximum_number_of_hits) destroyed = true;

	}
	else {
		//stop movement and exit here if the projectile is still in hitpause.
		hsp = 0;
		vsp = 0;
		return;
	}
}

//handle multihits.
if (!array_equals(initial_can_hit, can_hit) || (maximum_number_of_hits == 0 && hit_counter > 0)) {
	
	//even if the projectile hits multiple players, it only enters hitpause once.
	if (!hitpause_inflicted) {
		//give the projectile hitpause, then exit the script.
		hitpause_inflicted	= true;
		proj_hitpause		= true;
		proj_hitstop		= hitpause + max(0, -extra_hitpause);
		proj_old_hsp		= hsp;
		proj_old_vsp		= vsp;
		proj_old_img_spd	= img_spd;
		hsp 				= 0;
		vsp 				= 0;
		img_spd 			= 0;
		
		//if necessary, extend the lifetime of the projectile so that all of the hits can land.
		length = max(length, length - (length - hitbox_timer) + proj_hitstop + time_between_hits);
		
		return;
	}
	
	reset_can_hit_timer++;
	if (reset_can_hit_timer >= time_between_hits) {
		//increase the hit counter.
		hit_counter++;
		
		//if this projectile's 'player' has since changed (due to being parried or bashed), update the 'initial' can_hit array.
		if (player != proj_old_player) {
		    proj_old_player = player;
		    initial_can_hit = array_create(20, 1);
		    initial_can_hit[player] = 0;
		}
		
		//reset the can_hit array, allowing the projectile to hit opponents multiple times.
		can_hit = array_clone(initial_can_hit);
		
		//reset variables that detect hits.
		reset_can_hit_timer = 0;
		hitpause_inflicted = false;
		
		//parrying a projectile increases the amount of hitpause it inflicts... which is bad for multihits that need to be consistent.
		//fix that by simply overwriting it every reset.
		hitpause = proj_old_hitpause;
		
		//reduce this projectile's speed to its maximum cap when it hits something.
		if (proj_speed_cap != 0) {
			var speed_factor = point_distance(0, 0, hsp, vsp) * proj_speed_cap;
			if (speed_factor > 1) {
				hsp /= speed_factor;
				vsp /= speed_factor;
			}
		}
		
		//if this is the final hit, and a 'final hitbox' has been specified, destroy this hitbox and spawn the 'final hitbox'.
		if (maximum_number_of_hits > 0 && hit_counter >= maximum_number_of_hits) {
			destroyed = true;
			
			//spawn a 'final hit' hitbox, if specified.
			if (final_hit_hbox_num == 0) return;
			var final_hitbox = create_hitbox(attack, final_hit_hbox_num, x, y).spr_dir = spr_dir;
			final_hitbox.player = player;
			final_hitbox.can_hit = array_clone(initial_can_hit);
			if (final_hit_vfx != 0 && instance_exists(player_id)) with (player_id) spawn_hit_fx(other.x, other.y, other.final_hit_vfx);
			return;
		}
	}
}
```

</details>

## `hit_player.gml` - 'Magnet' Code and Visual Fixes
Optional, but recommended. Edit the attack index in the top line of code if necessary - `AT_NSPECIAL` is used as an example.

<details>
  <summary> hit_player.gml </summary>
	
```GML
if (my_hitboxID.attack == AT_NSPECIAL && my_hitboxID.hbox_num == 1) multihit_hit_player();


#define multihit_hit_player

//ignore kragg shards.
if (my_hitboxID.hit_priority == 1) return;
//ignore if the opponent is not stunned.
if (hit_player_obj.state_cat != SC_HITSTUN) return;

//magnet the opponent into the multihit projectile.
var x_dest = (my_hitboxID.x + 0.5 * my_hitboxID.hsp);
hit_player_obj.x = round( lerp(hit_player_obj.x, x_dest, my_hitboxID.proj_magnet_strength) );

//magnet along the y axis too if the opponent is not flinching.
var land_state = hit_player_obj.state == PS_HITSTUN_LAND;
if (!land_state) {
    var y_dest = my_hitboxID.y + round(0.5 * (my_hitboxID.vsp + hit_player_obj.char_height));
    hit_player_obj.y += round( lerp(hit_player_obj.y, y_dest, my_hitboxID.proj_magnet_strength);
    
    //if the hitbox has an angle flipper, prevent the opponent from rapidly flipping direction.
    if hit_player_obj.hurt_img > 1 return;
}

if (my_hitboxID.hit_flipper == 9 || (my_hitboxID.hit_flipper == 7 && my_hitboxID.kb_angle ^ 180 != 90)) {
    hit_player_obj.spr_dir = -my_hitboxID.spr_dir;
}
return;
```
</details>
