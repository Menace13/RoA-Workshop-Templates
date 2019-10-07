# Inflict Burn
### Created by Wishdream

## Description

This template allows attacks and projectiles to inflict burn. This can be used with the [Burn Consume (Zetter Strong)](https://github.com/Menace13/RoA-Workshop-Templates/blob/master/Templates/Attacks/burn_consume.md) to achieve a burn and consume system just like Zetterburn.

Placeholders you will need to replace:

Placeholder       | Example | Description
----------------- | ------- | -----------
[move_name]       | utilt   | The lowercase move name, it's just used for the script name.
[MOVE_NAME]       | UTILT   | The uppercase move name, used with `AT_` calls e.g. `AT_UTILT`.
[HITBOX_NUMBER]   | 1       | The hitbox ID that will inflict the burn status.

# Scripts

## *[move_name]*.gml

Text to replace (3 placeholders): `[move_name]`, `[MOVE_NAME]`, `[HITBOX_NUMBER]`

**NOTE: If you used a full character as a base, this file already exists, and the following lines probably also already exist, you just have to edit them.**

Simply, add effect number 1 (which is Burn) on the projectile/hitbox you want to use to inflict the burn status.

```gml
// [move_name].gml

set_hitbox_value(AT_[MOVE_NAME], [HITBOX_NUMBER], HG_EFFECT, 1);
```

