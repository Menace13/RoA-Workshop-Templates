# Double Jump Turn Around Template
### Created by Menace13

## Description

This template allows the character to turn around when doing a double jump, similar to any multi-jump character in any Smash game.

## Code (in `update.gml`)

```
// update.gml

if (state == PS_DOUBLE_JUMP && state_timer == 0) {
	if (spr_dir == 1 && (left_down || (left_pressed && !right_down))) {
		spr_dir = -1;
	} else if (right_down || (right_pressed && !left_down)) {
		spr_dir = 1;
	}
}
```

## Details

What this code does:
1. Checks if the player has *just* started their double jump.
2. If they are facing right, and they either are currently holding left, or they pressed left in the last 6 frames and are not currently holding right, then it turns them to the left.
3. If they are facing left, and they either are currently holding right, or they pressed right in the last 6 frames and are not currently holding left, then it turns them to the right.

In practice, it should be hard to turn the wrong way when using this code. If you are turned right and buffer double jump + left, it will turn left when double jumping unless you are specifically holding right. The only problem is if the player was holding backwards for a while before pressing double jump, buffers the double jump, and is not holding backwards anymore when the double jump actually happens. It may also have unintended consequences for keyboard players if they hold left and right at the same time when the double jump happens... but keyboard players are used to that by now.
