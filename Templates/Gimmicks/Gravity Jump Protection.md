# Gravity Jump Protection
### Created by Mawral

## Description

This script allows a character with an Absa-like jump to perform a [Gravity Jump](https://gfycat.com/demandingkeenhake) without spending their double-jump.
Workshop characters do not have this feature by default (even though Absa does), so it is recommended to include this code for any character with an Absa-jump.

# Scripts

```GML
//init.gml
grav_jump_protection = 0;
```

```GML
//update.gml
//gravity-jump protection
if (grav_jump_protection) {
	grav_jump_protection = 0;
	if (fast_falling && djumps > 0) djumps--;
}
if (state == PS_DOUBLE_JUMP && check_fast_fall && djumps > 0) grav_jump_protection = 1;
```
