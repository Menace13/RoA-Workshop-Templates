# Charging Gauge Template
### Created by Menace13

## Description
This charging gauge template includes code for making a functional gauge, including code for:
- [x] Creating variables to track the gauge
- [x] Changing the value of the gauge when hitting attacks
- [x] Changing the value of the gauge when parrying the opponent]
- [ ] Changing the value of the gauge when being hit by attacks
- [ ] Changing the value of thee gauge when being parried by the opponent
- [ ] Changing the value of the gauge when being killed by an opponent
- [x] Displaying the gauge visually in the HUD
- [ ] Displaying the gauge below the character sprite
- [ ] Spending gauge when using an attack to alter the attack, with an option to not spend gauge
- [ ] Displaying debug text to show your exact gauge value 

# Scripts

## init.gml (REQUIRED)

These variables' names can be changed as you want, as long as you change them *everywhere* and not just in the initialization. You can also change the starting value and maximum value of the gauge here. An example name would be `heat_gauge = 0;` and `heat_gauge_max = 1200;` for Elliana. Declaring your variables is REQUIRED, all other code is optional depending on how you want to implement your gauge.

#### Code
```
// init.gml

GAUGE_NAME_CURRENT = 0;
GAUGE_NAME_MAX = 100;
GAUGE_NAME_USED = false;
```

#### Details
Variable             | Description
-------------------- | -----------
`GAUGE_NAME_CURRENT` | Contains the current value of the gauge. This will be what you increase and decrease as necessary. You can also change the starting value of the gauge here.
`GAUGE_NAME_MAX`     | The maximum value for the gauge. Code is included to ensure that `GAUGE_NAME_CURRENT` never goes above this value.
`GAUGE_NAME_USED`    | A boolean used in `attack_update.gml` to remember when gauge is spent on a move after spending it. Unnecessary if gauge is never spent.

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







