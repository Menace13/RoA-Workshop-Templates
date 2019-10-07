# Burn Consume (Zetter Strong)
### Created by Wishdream

## Description

This template allows attacks to consume burn in order to achieve an explosion like Zetterburn's strong when the target is on fire.
The template requires that you use the [Inflict Burn](https://github.com/Menace13/RoA-Workshop-Templates/blob/master/Templates/Attacks/burn_inflict.md) template to set up the burn.

Placeholders you will need to replace:

Placeholder       | Example | Description
----------------- | ------- | -----------
[move_name]       | utilt   | The lowercase move name, it's just used for the script name.
[MOVE_NAME]       | UTILT   | The uppercase move name, used with `AT_` calls e.g. `AT_UTILT`.
[HITBOX_NUMBER1]  | 1       | The hitbox ID that will hit confirm for burn.
[HITBOX_NUMBER2]  | 2       | The hitbox ID that will consume burn and cause the explosion.

# Scripts

## *[move_name]*.gml

Text to replace (4 placeholders): `[move_name]`, `[MOVE_NAME]`, `[HITBOX_NUMBER1]`, `[HITBOX_NUMBER2]`

**NOTE: If you used a full character as a base, this file already exists, and the following lines probably also already exist, you just have to edit them.**

To perform a burn consume just like Zetterburn's, there are two parts:
 1. The first hitbox confirms the hit, **effect no. 3 adds extra hit stun to a burned player**.
 2. The second hitbox creates the explosion, **effect no. 2 consumes the burn** and **will not show if the player isn't burned**.

```gml
// [move_name].gml

// Use in the hitbox that confirms if the player is burned
set_hitbox_value(AT_[MOVE_NAME], [HITBOX_NUMBER1], HG_EFFECT, 3);

// Use in the hitbox that only hits if the player is burned
set_hitbox_value(AT_[MOVE_NAME], [HITBOX_NUMBER2], HG_EFFECT, 2);
```
