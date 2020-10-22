# True Teleportation Template
### Created by Deor

## Description
This template can be used to create a 'true teleportation' move — which here means a move that directly changes a character's location without the use of the built in speed variables — that takes the stage into account. This means that stage collision bugs (such as popping up to the top when one shouldn't) don't occur, but the player can still teleport *through* the stage and walls if the destination is on the other side, but will only teleport up to the edge if the destination inside the wall.

The example here uses `AT_USPECIAL`, as that is the most common teleportation move. It can be replaced with any other attack index as needed. You can see a list of the most commonly used indexes here: https://rivalsofaether.com/attack-indexes/.

# Scripts
## `uspecial.gml` — Setting up the Attack Script
This example is a simple teleporting up special that does not create a hitbox.
```GML
//attacks/uspecial.gml
//Simple teleport move setup.
//Feel free to copy-paste this into your project to test it.

set_attack_value(AT_USPECIAL, AG_CATEGORY, 2);
set_attack_value(AT_USPECIAL, AG_SPRITE, sprite_get("uspecial"));
set_attack_value(AT_USPECIAL, AG_HURTBOX_SPRITE, sprite_get("uspecial_hurt"));
set_attack_value(AT_USPECIAL, AG_NUM_WINDOWS, 3);

//Startup
set_window_value(AT_USPECIAL, 1, AG_WINDOW_TYPE, 1);
set_window_value(AT_USPECIAL, 1, AG_WINDOW_LENGTH, 10);
set_window_value(AT_USPECIAL, 1, AG_WINDOW_ANIM_FRAMES, 1);

//Active
set_window_value(AT_USPECIAL, 2, AG_WINDOW_TYPE, 1);
set_window_value(AT_USPECIAL, 2, AG_WINDOW_LENGTH, 1);
set_window_value(AT_USPECIAL, 2, AG_WINDOW_ANIM_FRAMES, 1);
set_window_value(AT_USPECIAL, 2, AG_WINDOW_ANIM_FRAME_START, 1);

//Endlag
set_window_value(AT_USPECIAL, 3, AG_WINDOW_TYPE, 1);
set_window_value(AT_USPECIAL, 3, AG_WINDOW_LENGTH, 14);
set_window_value(AT_USPECIAL, 3, AG_WINDOW_ANIM_FRAMES, 1);
set_window_value(AT_USPECIAL, 3, AG_WINDOW_ANIM_FRAME_START, 2);
```

## `init.gml` — Initialize Teleport Variables
```GML
//init.gml
tp_dist = 100;  //Distance the teleport will travel.
tp_prec = 8; //The precision of the teleportation algorithm. Larger number will be more accurate, but slower to calculate. 8 is a good balance of speed and precision.
```
## `attack_update.gml` — Teleport the Character
```GML
//attack_update.gml

//The main teleportation logic. You can place this anywhere in the script.
if(attack == AT_USPECIAL) {
    can_fast_fall = 0; //Prevent the player from accidentally fast falling during the move.
    
    //Startup Logic
    if(window == 1) {
        if(window_timer == get_window_value(attack, window, AG_WINDOW_LENGTH)) {    //Check to see if the window is at its end.
            var tp_angle = 0;   //The angle at which the teleport will go.
            if(!joy_pad_idle) { //Set the angle to the direction held on the joystick if a direction is being held.
                tp_angle = joy_dir;
            }
        
            var tp_dist_x = lengthdir_x(tp_dist, tp_angle); //The x and y components of the vector that will be added to
            var tp_dist_y = lengthdir_y(tp_dist, tp_angle); //the player's location.
            
            x_ = x + tp_dist_x; //The coordinates of the desired teleportation destination.
            y_ = y + tp_dist_y;
        }
    }
    
    //Teleportation Logic
    if(window == 2) {
        if(window_timer == 1) { //Teleport during the first frame of the active window.
            if(place_meeting(x_, y_, asset_get("par_block"))) { //Check to see if destination is colliding with the stage.
                var tp_dest = stage_collide_alt(x, y, x_, y_, tp_prec); //Use alternate collision function to determine the new teleportation destination.
                x_ = tp_dest[0];
                y_ = tp_dest[1];
            }
            
            //Change the player's location to the teleport destination.
            x = x_;
            y = y_;
        }
    }
}


//-------------------------------------------------------------------------------------------------------------
//Alternate stage collide function. This must go at the end of attack_update.
#define stage_collide_alt {
    ///stage_collide_alt(x1, y1, x2, y2, prec)
    //This recursive script checks from (x1, y1) to (x2, y2) for the location of coordinates
    //of the edge of the stage along that line, returning it as an array formated: [x, y].
    //
    //This script assumes that there is a collision at (x2,y2) and would need to be modified
    //if this is not assumed in order to function properly.
    //
    //  Arguments:
    //      x1      The x cooridnate of the starting point, real.
    //      y1      The y coordinate of the starting point, real.
    //      x2      The x coordinate of the ending point, real.
    //      y2      The y coordinate of the ending point, real.
    //      prec    The number of samples taken for the measurement, real.
    //
    //Script by Deor
    
    var x1 = argument[0];
    var y1 = argument[1];
    var x2 = argument[2];
    var y2 = argument[3];
    var prec = argument[4];
    
    var qpu_x = (x2 - x1) / prec;  //One nth the length of the x component of the input vector, where n is prec.
    var qpu_y = (y2 - y1) / prec;  //One nth the length of the y component of the input vector.
    var x_ = x2;    //The location of the closest detection point that is colliding with the
    var y_ = y2;    //stage, the default being the destination point.
    
    //Check each detection point to find which is the closest to (x1,y1) while still colliding with the stage.
    for(var i = 1; i < prec; i++) {
        if(place_meeting(x2 - qpu_x * i, y2 - qpu_y * i, asset_get("par_block"))) {
            x_ = x2 - qpu_x * i;
            y_ = y2 - qpu_y * i;
        }
    }
    
    //If the closest colliding detection point is not the destination, recurse with the new destination being the closest colliding point.
    if(x_ != x2 && y_ != y2) {
        return stage_collide_alt(x1, y1, x_, y_, prec);
    }
        
    //Otherwise, return the closest free detection point.
    else {
        return [x2 - qpu_x, y2 - qpu_y];
    }
}
```