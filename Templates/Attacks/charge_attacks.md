# Charge Attacks Template
### Created by Mawral

## Description

This template demonstrates how to make an attack charge, how to scale up an attack's stats based on charge time, and how to have multiple attack stages based on charge thresholds.

In this example, `AT_NSPECIAL` is used as the 'Charging Attack' attack index. You can change this to any attack index you want. For reference, see https://rivalsofaether.com/attack-indexes/ .
There is also an optional 'Max Charge Attack', set as `AT_NSPECIAL_2` in this template. You can change this to any additional attack index.

# Creating the 'Charge Attack'
A charge attack needs to feature a long 'charging' window before the attack. When the player lets go of the attack button, the attack will end the 'charging' window early and go to the next window. 
The other properties of the windows and hitboxes can be changed freely to suit your character. 
<details>
  <summary>Example 'Charge Attack' setup</summary>
 
  ```GML
//attacks/nspecial.gml
//a minimal 'charge attack' attack example. 
//You can copy-paste this into your project to quickly test this template.

set_attack_value(AT_NSPECIAL, AG_CATEGORY, 2);
set_attack_value(AT_NSPECIAL, AG_SPRITE, sprite_get("nspecial"));
set_attack_value(AT_NSPECIAL, AG_HURTBOX_SPRITE, sprite_get("nspecial_hurt"));
set_attack_value(AT_NSPECIAL, AG_NUM_WINDOWS, 5);

//startup
set_window_value(AT_NSPECIAL, 1, AG_WINDOW_LENGTH, 10);
set_window_value(AT_NSPECIAL, 1, AG_WINDOW_ANIM_FRAMES, 1);
set_window_value(AT_NSPECIAL, 2, AG_WINDOW_ANIM_FRAME_START, 0);

//charging window. The length of this window = the maximum charge time.
set_window_value(AT_NSPECIAL, 2, AG_WINDOW_LENGTH, 60); 
set_window_value(AT_NSPECIAL, 2, AG_WINDOW_ANIM_FRAMES, 1);
set_window_value(AT_NSPECIAL, 2, AG_WINDOW_ANIM_FRAME_START, 1);

//charge release window. This window should be at least 2 frames long.
set_window_value(AT_NSPECIAL, 3, AG_WINDOW_LENGTH, 2);
set_window_value(AT_NSPECIAL, 3, AG_WINDOW_ANIM_FRAMES, 1);
set_window_value(AT_NSPECIAL, 3, AG_WINDOW_ANIM_FRAME_START, 2);

//active
set_window_value(AT_NSPECIAL, 4, AG_WINDOW_LENGTH, 3);
set_window_value(AT_NSPECIAL, 4, AG_WINDOW_ANIM_FRAMES, 1);
set_window_value(AT_NSPECIAL, 4, AG_WINDOW_ANIM_FRAME_START, 3);
set_window_value(AT_NSPECIAL, 4, AG_WINDOW_HSPEED, 5);
set_window_value(AT_NSPECIAL, 4, AG_WINDOW_HSPEED_TYPE, 1);

//recovery
set_window_value(AT_NSPECIAL, 5, AG_WINDOW_LENGTH, 10);
set_window_value(AT_NSPECIAL, 5, AG_WINDOW_ANIM_FRAMES, 1);
set_window_value(AT_NSPECIAL, 5, AG_WINDOW_ANIM_FRAME_START, 4);
set_window_value(AT_NSPECIAL, 5, AG_WINDOW_HAS_WHIFFLAG, 1);

set_num_hitboxes(AT_NSPECIAL, 1);

//attack hitbox
set_hitbox_value(AT_NSPECIAL, 1, HG_WINDOW, 4);
set_hitbox_value(AT_NSPECIAL, 1, HG_LIFETIME, get_window_value(AT_NSPECIAL, 4, AG_WINDOW_LENGTH));
set_hitbox_value(AT_NSPECIAL, 1, HG_HITBOX_X, 30);
set_hitbox_value(AT_NSPECIAL, 1, HG_HITBOX_Y, -30);
set_hitbox_value(AT_NSPECIAL, 1, HG_PRIORITY, 3);
set_hitbox_value(AT_NSPECIAL, 1, HG_WIDTH, 60);
set_hitbox_value(AT_NSPECIAL, 1, HG_HEIGHT, 50);
set_hitbox_value(AT_NSPECIAL, 1, HG_DAMAGE, 2);
set_hitbox_value(AT_NSPECIAL, 1, HG_ANGLE, 45);
set_hitbox_value(AT_NSPECIAL, 1, HG_BASE_KNOCKBACK, 8);
set_hitbox_value(AT_NSPECIAL, 1, HG_KNOCKBACK_SCALING, 0.5);
set_hitbox_value(AT_NSPECIAL, 1, HG_HITSTUN_MULTIPLIER, 1);
set_hitbox_value(AT_NSPECIAL, 1, HG_BASE_HITPAUSE, 7);
set_hitbox_value(AT_NSPECIAL, 1, HG_HITPAUSE_SCALING, 0.5);
set_hitbox_value(AT_NSPECIAL, 1, HG_HIT_SFX, asset_get("sfx_blow_medium1"));
```

</details>

# Optional - Creating a 'Max Charge Attack'
There isn't anything special here - you can make your charge window transition to any kind of attack you wish. A short startup is recommended so that the attack feels responsive after charging.
An attack can also have several 'tiers' of charge attacks if desired, e.g. a megaman blaster with a small, medium and large projectile. Simply add a new attack for each charge stage.

<details>
  <summary>Example 'Max Charge' attack setup</summary>
 
  ```GML
//attacks/nspecial_2.gml
//a minimal 'max charge attack' example with a single hitbox. 
//You can copy-paste this into your project to quickly test this template.

set_attack_value(AT_NSPECIAL_2, AG_CATEGORY, 2);
set_attack_value(AT_NSPECIAL_2, AG_SPRITE, sprite_get("nspecial"));
set_attack_value(AT_NSPECIAL_2, AG_HURTBOX_SPRITE, sprite_get("nspecial_hurt"));
set_attack_value(AT_NSPECIAL_2, AG_NUM_WINDOWS, 3);

//startup (in most cases, this should = your charging attack's 'post-charge' length.)
set_window_value(AT_NSPECIAL_2, 1, AG_WINDOW_LENGTH, 2);
set_window_value(AT_NSPECIAL_2, 1, AG_WINDOW_ANIM_FRAMES, 1);
set_window_value(AT_NSPECIAL_2, 2, AG_WINDOW_ANIM_FRAME_START, 2);

//active
set_window_value(AT_NSPECIAL_2, 2, AG_WINDOW_LENGTH, 10);
set_window_value(AT_NSPECIAL_2, 2, AG_WINDOW_ANIM_FRAMES, 1);
set_window_value(AT_NSPECIAL_2, 2, AG_WINDOW_ANIM_FRAME_START, 3);
set_window_value(AT_NSPECIAL_2, 2, AG_WINDOW_HSPEED, 8);
set_window_value(AT_NSPECIAL_2, 2, AG_WINDOW_HSPEED_TYPE, 1);

//recovery
set_window_value(AT_NSPECIAL_2, 3, AG_WINDOW_LENGTH, 10);
set_window_value(AT_NSPECIAL_2, 3, AG_WINDOW_ANIM_FRAMES, 1);
set_window_value(AT_NSPECIAL_2, 3, AG_WINDOW_ANIM_FRAME_START, 4);
set_window_value(AT_NSPECIAL_2, 3, AG_WINDOW_HAS_WHIFFLAG, 1);

set_num_hitboxes(AT_NSPECIAL_2, 1);

//throw hitbox
set_hitbox_value(AT_NSPECIAL_2, 1, HG_WINDOW, 2);
set_hitbox_value(AT_NSPECIAL_2, 1, HG_LIFETIME, get_window_value(AT_NSPECIAL_2, 2, AG_WINDOW_LENGTH));
set_hitbox_value(AT_NSPECIAL_2, 1, HG_HITBOX_X, 30);
set_hitbox_value(AT_NSPECIAL_2, 1, HG_HITBOX_Y, -30);
set_hitbox_value(AT_NSPECIAL_2, 1, HG_PRIORITY, 3);
set_hitbox_value(AT_NSPECIAL_2, 1, HG_WIDTH, 60);
set_hitbox_value(AT_NSPECIAL_2, 1, HG_HEIGHT, 50);
set_hitbox_value(AT_NSPECIAL_2, 1, HG_DAMAGE, 14);
set_hitbox_value(AT_NSPECIAL_2, 1, HG_ANGLE, 45);
set_hitbox_value(AT_NSPECIAL_2, 1, HG_BASE_KNOCKBACK, 9);
set_hitbox_value(AT_NSPECIAL_2, 1, HG_KNOCKBACK_SCALING, 0.75);
set_hitbox_value(AT_NSPECIAL_2, 1, HG_BASE_HITPAUSE, 10);
set_hitbox_value(AT_NSPECIAL_2, 1, HG_HITPAUSE_SCALING, 0.9);
set_hitbox_value(AT_NSPECIAL_2, 1, HG_HIT_SFX, asset_get("sfx_blow_heavy2"));
  ```

</details>

# Scripts
 
## `init.gml` - Initialize Charge Timer Variable
```GML
//init.gml
charge_timer = 0;    //this variable will keep track of how long our charge-attack has been charged for.
```

## `attack_update.gml` - Charge, Release and Transition Code
```GML
if (attack == AT_NSPECIAL && !hitpause) {
	switch (window) {
		case 1: //startup window.
    
			//we only want to perform this code on the first frame of this window.
			if (window_timer != 1) break;
			//reset the charge timer variable.
			charge_timer = 0;
      
			break;
		
		case 2: //charging window.
    
			//if the special button is held down (change if needed), charge this attack.
			if (special_down) {
				charge_timer += 1;
			}
			//when the button is released, transition to the next window.
			else {
				//skip to the next window
				window = 3;
				window_timer = 0;
			}
      
			break;
		
		case 3: //charge release window.
    
			//we only want to perform this code on the first frame of this window.
			if (window_timer != 1) break;
			
			//make this attack's damage scale by how long it has been charged for.
			//we'll do this by directly updating the attack's hitbox grid indexes.
			var new_damage_value = 5 + round( charge_timer / 10 );
			set_hitbox_value(AT_NSPECIAL, 1, HG_DAMAGE, new_damage_value);
			//you can change other properties besides damage of course, or change none at all.

			//alternatively: if we charged 60 frames or more, switch to a more powerful attack.
			//if you don't need an additional attack, you can remove this section.
			if (charge_timer >= 60) {
				attack_end();
				set_attack(AT_NSPECIAL_2);
			}
      
			break;
	}
}
```
