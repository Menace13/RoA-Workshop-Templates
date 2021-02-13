## **_(This is a legacy template - consider using the most up-to-date Command Grabs template [here](https://github.com/Menace13/RoA-Workshop-Templates/blob/master/Templates/Attacks/command_grabs_2.0.md).)_**


# COMMAND GRABS

## Contents

Note: This template is more trimmed down than the others, so looking at the previously made templates may be easier.
 - [x] Command Grab setup
 - [x] Command Grab usage
 - [x] How to move people in Command Grabs and keep them in the grab
 - [ ] Smooth moving vs teleporting
 - [x] Visibility vs Invisibility
 - [x] Optional Invincibility
 
## Command Grab setup
```
//init.gml
grabbedid = noone;
```
It's important to set up this variable, because otherwise it's likely to error for the first couple of frames.

```
//update.gml
if(grabbedid != noone){
	grabbedid.ungrab++;
	if(grabbedid.ungrab == 2){
		grabbedid.visible = true; //Feel free to remove this line if the grab does not make the opponent invisible.
		grabbedid.invincible = false; //Feel free to remove this line if the grab does not make the opponent invincible.
		grabbedid.state = PS_TUMBLE;
		grabbedid.ungrab = 0;
		grabbedid = noone;
	}
}
```
These are backup checks to make sure that the grabbed player isn't stuck in a grab forever. If the state of the grabbed player isn't constantly set to PS_WRAPPED, it will released the grabbed.

## Command Grab usage
```
//hit_player.gml
if (attack == AT_GRAB && hit_player_obj.state_cat == SC_HITSTUN) {
    if (window == GRAB_WINDOW && grabbedid == noone){
        hit_player_obj.grabbed = 1;
        grabbedid = hit_player_obj;
	grabbedid.ungrab = 0;
    }
}
```
NOTE: YOU NEED TO REPLACE AT_GRAB AND GRAB_WINDOW
AT_EXAMPLE needs to be a valid attack name, like AT_JAB
EXAMPLE_WINDOW needs to be the window with the hitbox you want to grab with, for example 2. NOTE: this does cause problems with multiple hitboxes in the same window, but there are other ways to tell hitboxes apart if that is the case.
NOTE: This does not necessarially grab the person closest to the hitbox, you have to do extra checks to make that happen. This tutorial may eventually be updated with those checks.

## How to move people in Command Grabs and keep them in the grab
#### EXAMPLE:
```
//attack_update.gml
if (attack == AT_GRAB && (state == PS_ATTACK_AIR || state == PS_ATTACK_GROUND)){
    if (window == GRAB_WINDOW && grabbedid != noone){
	grabbedid.ungrab = 0;
        window_timer = 1; //DELETE THIS LINE TO LIMIT HOW LONG THE GRAB IS TO THE WINDOW LENGTH
        grabbedid.invincible = true; //DELETE THIS LINE TO MAKE THE GRABBED PLAYER HITTABLE
        //grabbedid.visible = false; //UNCOMMENT THIS LINE TO MAKE THE GRABBED PLAYER INVISIBLE
        grabbedid.x = x + spr_dir * 70; //SET GRABBED PLAYER X TO BE RELATIVE TO PLAYER X
        grabbedid.y = y; //SET GRABBED PLAYER Y TO BE RELATIVE TO PLAYER Y
        grabbedid.spr_dir = -spr_dir; //TURN THE GRABBED PLAYER TO FACE THE GRABBING PLAYER
        grabbedid.wrap_time = 6000;
        grabbedid.state = PS_WRAPPED;
        if(special_pressed){ //REPLACE THIS IF CONDITION WITH WHAT YOU WANT TO RELEASE THE GRAB
            grabbedid.state = PS_TUMBLE;
            grabbedid.x = x;
            grabbedid.y = y;
            grabbedid = noone;
        }
    }
}
```
NOTE: AGAIN, REPLACE ALL INSTANCES OF AT_GRAB AND GRAB_WINDOW
NOTE: This code makes the grab release when special is pressed after the opponent is grabbed. You can change this condition to whatever you want. (the if statement with special_pressed; feel free to remove it as well)
