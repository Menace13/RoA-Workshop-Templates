# Peach Float Template
### Created by Golden Epsilon
## Description
This recreates Peach's floating mechanic from smash, with some suggestions for how to change it up as well.
More specifically this makes a character hover for a limited time when they either hold down and jump or hold jump when falling, and release early if jump and up are both released at the same time.

Included is:
- [x] How to set up the variables for floating, with a bunch of modifiable variables for customization
- [x] How to actually make the character float
- [ ] Cookies
# Scripts
## init.gml (REQUIRED)
As always you can change the names of the variables, but CHANGE IT EVERYWHERE. Changed variable names can sneak up on you really easily.

#### Code
```
// init.gml

floating = 0;
floatTimer = 0;
jumpTimer = 0;
floatMax = 60; //MODIFIABLE
jumpMax = 5; //MODIFIABLE
gravSet = 0; //MODIFIABLE
vspSet = 0; //MODIFIABLE
hspSet = -1; //MODIFIABLE
```


#### Details
Variable             | Description
-------------------- | -----------
`floating`           | Whether you are currently floating. 0 means your float is ready, 1 means you are floating, and -1 means that you have to touch the ground before floating again.
`floatTimer`         | How long you have been floating.
`jumpTimer`          | How long you have been in the air, as far as the float is concerned.
`floatMax`           | How many frames you want to float for. RoA runs at 60 frames per second, so if this is set to 60 you will be able to float for one second.
`jumpMax`            | How long you have to be in the air before you can float. Used to stop people from failing to float because they are too close to the ground.
`gravSet`            | What the gravity is set to when you hover. Peach would be 0, but changing this can make your character float up and down. Setting to -1 disables setting gravity.
`vspSet`             | When you hover your vertical momentum is set to this value. This lets you increase or decrease your height at a constant rate. Setting to -1 disables.
`hspSet`             | When you hover your horizontal momentum is set to this value. This moves you forward/backward when you hover, or just locks you in place. Speed is based on the direction you are facing, and setting to -1 disables.
## update.gml (REQUIRED)
This script is where the magic happens. Most of this is changed by the variables you defined in init already, but you can probably still make some neat modifications, for example changing the inputs to activate.

#### Code
```
//update.gml

var air = (state == PS_FIRST_JUMP || state == PS_DOUBLE_JUMP || state == PS_WALL_JUMP || state == PS_IDLE_AIR);
if(air || state == PS_ATTACK_AIR){
	jumpTimer++;
}
if((state == PS_IDLE_AIR || (air && vsp > 0)) && jump_down && floating == 0 || (state == PS_IDLE_AIR || (air && jumpTimer > jumpMax)) && (jump_down && down_down) && floating == 0){
	floating = 1;
	floatTimer = floatMax
}
if(floating){
	if(gravSet != -1){grav = gravSet;}
	if(vspSet != -1){vsp = vspSet;}
	if(hspSet != -1){hsp = hspSet * spr_dir;}
	if(!((jump_down || up_down) && (state == PS_ATTACK_AIR || air))){
		floating = -1;
	}
	if(floatTimer > 0){
		floatTimer--;
	}else{
		floating = -1;
	}
}
if(state == PS_IDLE || state == PS_CROUCH || state == PS_JUMPSQUAT || state == PS_WALK || state == PS_DASH){
	floating = 0;
	jumpTimer = 0;
}
```
