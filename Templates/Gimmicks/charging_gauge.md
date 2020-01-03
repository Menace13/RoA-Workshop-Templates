# Charging Gauge Template
### Created by Menace13

## Description
This charging gauge template includes code for making a functional gauge, including code for:
- [x] Creating variables to track the gauge
- [x] Displaying debug text to show your exact gauge value
- [x] Displaying the gauge visually in the HUD
- [ ] Displaying the gauge below the character sprite
- [x] Changing the value of the gauge when hitting attacks
- [x] Changing the value of the gauge when parrying the opponent
- [x] Changing the value of the gauge when being hit by attacks
- [x] Changing the value of the gauge when being parried by the opponent
- [x] Changing the value of the gauge when being killed by an opponent
- [x] Spending gauge when using an attack to alter the attack's hitboxes, with an option to not spend gauge
- [ ] Spending gauge when using an attack to alter the attack's window
- [ ] Spending gauge only when hitting an attack (this would use `GAUGE_NAME_USED`)
- [ ] *[Your suggestion here]*

# Scripts

## init.gml (REQUIRED)

These variables' names can be changed as you want, as long as you change them *everywhere* and not just in the initialization. You can also change the starting value and maximum value of the gauge here. An example name would be `heat_gauge = 0;` and `heat_gauge_max = 1200;` for Elliana. Declaring your variables is REQUIRED, all other code is optional depending on how you want to implement your gauge.

#### Code
```
// init.gml

GAUGE_NAME_CURRENT = 0;
GAUGE_NAME_MAX = 100;
GAUGE_NAME_USED = false;	// optional
```

#### Details
Variable             | Description
-------------------- | -----------
`GAUGE_NAME_CURRENT` | Contains the current value of the gauge. This will be what you increase and decrease as necessary. You can also change the starting value of the gauge here.
`GAUGE_NAME_MAX`     | The maximum value for the gauge. Code is included to ensure that `GAUGE_NAME_CURRENT` never goes above this value.
`GAUGE_NAME_USED`    | A boolean used in `attack_update.gml` to remember when gauge is spent on a move after spending it. Unnecessary if you don't need to remember whether or not you already spend gauge on an attack. An example of when this is necessary is Guadua's neutral special - it sets `ate_player_bamboo` in `attack_update.gml` to false when you start neutral special, sets it to true in `hit_player.gml` if the hit player was `bambood`, and then checks if `ate_player_bamboo` is true again in window 2, in which case it will skip to window 5. If you don't know if you need this variable, you can probably ignore it.

## draw_hud.gml

This code allows you to show your gauge visually in the HUD, in the exact same way Elliana's heat gauge is shown in the HUD. You will need to make a sprite strip similar to the one below for this code to work:

#### Example: gauge_strip11.png

![Image of an example gauge sprite strip](https://raw.githubusercontent.com/Menace13/RoA-Workshop-Templates/master/images/gauge_strip11.png)

#### Code

```
// draw_hud.gml

draw_sprite(sprite_get("gauge"), floor(GAUGE_NAME_CURRENT/10), temp_x + 24, temp_y + 6);
```

#### Details

This specific example makes a lot of assumptions:
- The gauge is in a file named `gauge_stripNUM.png`, where `NUM` is the number of frames in the strip
- Each frame of the gauge is 10 pixels wide and 36 pixels tall
- The gauge sprite contains `GAUGE_NAME_MAX / 10 + 1` frames, ranging from empty to full
  - For example, if `GAUGE_NAME_MAX = 100`, then the file should be `gauge_strip11.png`
  
Given these assumptions, it will display the correct frame of the sprite strip depending on the value of `GAUGE_NAME_CURRENT`. For the example of a gauge ranging from 0 to 100 with 11 sprite frames, it will show the first sprite frame if the gauge is between 0 and 9, the second sprite frame if the gauge is between 10 and 19, ..., and the final sprite frame if the gauge is exactly 100.

**NOTE:** This location is the same location as Elliana's meter. **You may need to change your `hud.png` and `hurt.png` so the meter does not cover your character's face in the HUD.** Most likely you will only need to shift your art to the right by about 6-8 pixels.

## debug_draw.gml

This code is only for testing the character. It will show debug text below your character with your exact current gauge value, your max gauge value, and whether your current attack used your gauge.

#### Code

```
//debug_draw.gml

draw_debug_text(x,y,"GAUGE = " + string(GAUGE_NAME_CURRENT) + "/" + string(GAUGE_NAME_MAX));
draw_debug_text(x,y+12,"GAUGE_USED: " + string(GAUGE_NAME_USED));
```

## hit_player.gml

This code changes the value of the gauge when hitting an opponent. Note that the code shown increases the gauge for **each hitbox** hitting the opponent, so multihit moves will increase it faster than single hit moves. In this example, strong attacks increase the gauge by 5 while jabs increase the gauge by 1; if the jab combo is a standard three hit combo, then hitting all three will increase the gauge by 3.

#### Code

```
// hit_player.gml

var hitmove = my_hitboxID.attack;

if(GAUGE_NAME_CURRENT < GAUGE_NAME_MAX) {
	if (hitmove == AT_FSTRONG || hitmove == AT_DSTRONG || hitmove == AT_USTRONG) {
		GAUGE_NAME_CURRENT += 5;
	} else if (hitmove == AT_JAB) {
		GAUGE_NAME_CURRENT += 1;
	}
	if (GAUGE_NAME_CURRENT > GAUGE_NAME_MAX) { GAUGE_NAME_CURRENT = GAUGE_NAME_MAX; }
}
```

## got_hit.gml

This code changes the value of the gauge when being hit by an opponent. In this example, being hit by a projectile decreases the gauge by 1, while being hit by a normal attack decreases the gauge by 2. Again, this is for each individual hitbox, so a three hit jab combo will reduce the gauge by 6. The code only runs if the gauge is above 0, and will not decrease the gauge below 0.

#### Code

```
// got_hit.gml

if(GAUGE_NAME_CURRENT > 0) {
	if (enemy_hitboxID.type == 1) {
		GAUGE_NAME_CURRENT -= 1;
	} else {
		GAUGE_NAME_CURRENT -= 2;
	}
	if (GAUGE_NAME_CURRENT < 0) { GAUGE_NAME_CURRENT = 0; }
}
```

## parry.gml

Here are two different code examples. The first changes the value of the gauge when parrying anything, while the second changes the value of the gauge only when parrying an opponent's non-projectile hitbox.

#### Code for parrying anything

```
// parry.gml

GAUGE_NAME_CURRENT += 10;
if (GAUGE_NAME_CURRENT > GAUGE_NAME_MAX) { GAUGE_NAME_CURRENT = GAUGE_NAME_MAX; }
```

#### Code for parrying a non-projectile hitbox

```
// parry.gml

if (enemy_hitboxID.type == 1) { GAUGE_NAME_CURRENT += 10; }
if (GAUGE_NAME_CURRENT > GAUGE_NAME_MAX) { GAUGE_NAME_CURRENT = GAUGE_NAME_MAX; }
```

## got_parried.gml

This code example is the equivalent of the `parry.gml` code for parrying a non-projectile hitbox, but is instead for when your non-projectile hitbox is parried .

#### Code

```
// got_parried.gml

if (my_hitboxID.type == 1) { GAUGE_NAME_CURRENT -= 5; }
if (GAUGE_NAME_CURRENT < 0) { GAUGE_NAME_CURRENT = 0; }
```

## death.gml

Here's two code examples, one for cutting your gauge in half (rounded down) when you die, and one for emptying your entire gauge when you die.

#### Code to lose half of gauge when you die

```
// death.gml

GAUGE_NAME_CURRENT = floor(GAUGE_NAME_CURRENT / 2);
```

#### Code to lose all of gauge when you die

```
// death.gml

GAUGE_NAME_CURRENT = 0;
```

## attack_update.gml

Finally we're at the payoff - the code that lets you spend your gauge to make your attacks stronger. The first example here is based on Guadua's code and affects two moves: it will make Guadua's grounded down-special have a different angle and far more knockback scaling, and it will make the aerial down-special bamboo projectile spike opponents.

#### Code to spend gauge to make a hitbox stronger

```
// attack_update.gml

if (attack == AT_DSPECIAL){
	if (window == 1 && window_timer == 1) {
		reset_hitbox_value(AT_DSPECIAL, 1, HG_KNOCKBACK_SCALING);
		reset_hitbox_value(AT_DSPECIAL, 1, HG_ANGLE);
	}
    if (window == 2 && window_timer == 1){
		if(!shield_down && GAUGE_NAME_CURRENT >= 10) {
			GAUGE_NAME_CURRENT -= 10;
			set_hitbox_value(AT_DSPECIAL, 1, HG_KNOCKBACK_SCALING, 1.2);
			set_hitbox_value(AT_DSPECIAL, 1, HG_ANGLE, 90);
		}
    }
}

if (attack == AT_DSPECIAL_AIR){
	if (window == 1 && window_timer == 1) {
		reset_hitbox_value(AT_DSPECIAL_AIR, 1, HG_ANGLE);
	}
    if (window == 2 && window_timer == 1){
		if(!shield_down && GAUGE_NAME_CURRENT >= 10) {
			GAUGE_NAME_CURRENT -= 10;
			set_hitbox_value(AT_DSPECIAL_AIR, 1, HG_ANGLE, -70);
		}
    }
}
```

