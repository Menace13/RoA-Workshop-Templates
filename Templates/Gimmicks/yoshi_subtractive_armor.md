# Yoshi Double Jump Subtractive Armor Template
### Created by Menace13

## Description

In every Super Smash Bros. game, Yoshi's double jump has limited super armor that prevents any knockback below a certain threshold. In 64, Melee, and Ultimate, when Yoshi's double jump super armor is broken, he takes reduced knockback from the move that broke the armor, with the new knockback being the difference between his old knockback and his super armor value. For example, in 64, Yoshi's super armor had a tolerance of 120 knockback. So if Yoshi was hit by a move with 200 knockback, it would break the super armor, and he would take 80 knockback (200 - 120 = 80). This effect is frequently called "subtractive" armor.

This code is a faithful recreation of Yoshi's subtractive double jump armor for use in Rivals of Aether. **Minor warning: potential issues may occur in extreme scenarios, such as if the move hitting you has 0 frames of hitpause.**

*Note: if you want your character to be able to turn around when doing double jump, like Yoshi can, you should also implement the [Double Jump Turn Around](https://github.com/Menace13/RoA-Workshop-Templates/blob/master/Templates/Gimmicks/double_jump_turn_around.md) template.*

# Scripts

## init.gml

First we want to set the desired amount of double jump armor, so we don't have to edit it in multiple files. For comparisons to Etalus' super armor, his f-air armor will armor through any attack with less than 12 knockback, his uncharged ice armor strong attacks armor through attacks with less than 10 knockback, and his half-charged ice armor strong attacks armor through attacks with less than 25 knockback (more than half-charge = true super armor).

#### Code

```
// init.gml

DOUBLE_JUMP_ARMOR = 20;     //replace 20 with your number
```

## update.gml

In `update.gml` we give our character soft armor while in the double jump state. Soft armor is armor that can only withstand up to a certain knockback threshold, so we will be using that to replicate any situation where our armor successfully withstands the enemy attack.

#### Code

```
// update.gml

if (state == PS_DOUBLE_JUMP) {
    soft_armor = DOUBLE_JUMP_ARMOR;
}
```

## got_hit.gml

Here's the complicated part, where we turn our soft armor into true subtractive armor. I'll walk through each line of code:

Code                                                            | Description
--------------------------------------------------------------- | -----------
`if (state_cat == SC_HITSTUN && prev_state == PS_DOUBLE_JUMP)`  | First, we check that our character is currently in a hitstun state (most importantly, PS_HITSTUN), and that we got there straight from PS_DOUBLE_JUMP. We only want to run this code in that specific situation, becauase that means our double jump's soft armor was broken by our opponent and we were put into a hitstun state.
`var kb_ratio = (orig_knock - DOUBLE_JUMP_ARMOR) / orig_knock;` | We already know our new knockback is going to be the difference between our original knockback and our soft armor. However, we're going to be changing values, that are currently based on our old knockback, to be based on our new knockback, so we need to know the ratio between our old and new knockbacks. We will use this for every necessary change.
`orig_knock -= DOUBLE_JUMP_ARMOR;`                              | Editing `orig_knock` will change the knockback we receive when hitpause ends, so we simply subtract our double jump armor's threshold from the knockback we are about to take. Note that this line of code does the same thing as `orig_knock *= kb_ratio;`, but it is more readable this way.
`hsp *= kb_ratio;` and `vsp *= kb_ratio;`                       | Editing `hsp` and `vsp` don't actually affect our final trajectory (that is taken care of by the previous line) but they *do* affect whether or not we get a galaxy effect. Additionally, when we are initially hit, they are set to values based on the knockback we are expected to receive. Because we edited the knockback we *will* receive, this will cause false galaxy effects, so we multiply them by `kb_ratio` to put them at the correct values for our new knockback. This should results in galaxies appearing at the correct (far higher) percents.
`hitstun *= kb_ratio;` and `hitstun_full *= kb_ratio;`          | Last, it would be really bad if we didn't decrease our hitstun in line with our knockback decrease. Without lowering the hitstun by an amount proportional to our new lower knockback, we would be stuck in hitstun while falling for a very long time. For example, if Absa up-air hits Zetterburn at 90%, he takes 21.69 knockback and receives 67 frames of hitstun. If he had 20 subtractive armor, but didn't reduce the hitstun, he would have only 1.69 vertical knockback and fall *nearly 1000 pixels* before exiting hitstun. Instead, with these lines of code, he will exit hitstun approximately 3 pixels higher than where he started after only 5 frames of hitstun. So don't leave out these lines unless you want your character falling forever when the subtractive armor is broken.

#### Code

```
// got_hit.gml

if (state_cat == SC_HITSTUN && prev_state == PS_DOUBLE_JUMP) {
	var kb_ratio = (orig_knock - DOUBLE_JUMP_ARMOR) / orig_knock;
	orig_knock -= DOUBLE_JUMP_ARMOR;
	hsp *= kb_ratio;
	vsp *= kb_ratio;
	hitstun *= kb_ratio;
	hitstun_full *= kb_ratio;
}
```
