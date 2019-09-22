# Short Endlag on Hit Template
### Created by Menace13

## Description

This template provides code that shortens the endlag of a move drastically on hit.

Text to replace:

Placeholder       | Example | Description
----------------- | ------- | -----------
[move_name]       | utilt   | The lowercase move name, it's just used for the script name.
[MOVE_NAME]       | UTILT   | The uppercase move name, used with `AT_` calls e.g. `AT_UTILT`.
[FINAL_WINDOW]    | 3       | The window number of the endlag window for the attack you are editing. For example, if the move has one hitbox that lasts for all of window 2, then window 3 is the final / endlag window, so you would replace [FINAL_WINDOW] with 3.
[ENDLAG_ON_WHIFF] | 16      | The number of frames of endlag you want the move to have on whiff.
[ENDLAG_ON_HIT]   | 8       | The number of frames of endlag you want the move to have on hit.

# Scripts

## *[move_name]*.gml

Text to replace (6 placeholders): `[move_name]`, `[MOVE_NAME]`, `[FINAL_WINDOW]`, `[ENDLAG_ON_WHIFF]`, `[MOVE_NAME]`, `[FINAL_WINDOW]`

```
// [move_name].gml - this file already exists if you used any full character as a base, and the follow lines probably also already exist, you just have to edit them

set_window_value(AT_[MOVE_NAME], [FINAL_WINDOW], AG_WINDOW_LENGTH, [ENDLAG_ON_WHIFF]);
set_window_value(AT_[MOVE_NAME], [FINAL_WINDOW], AG_WINDOW_HAS_WHIFFLAG, 0);
```

## hit_player.gml

Text to replace (4 placeholders): `[MOVE_NAME]`, `[MOVE_NAME]`, `[FINAL_WINDOW]`, `[ENDLAG_ON_HIT]`

```
// hit_player.gml

if (my_hitboxID.attack == AT_[MOVE_NAME]) {
    set_window_value(AT_[MOVE_NAME], [FINAL_WINDOW], AG_WINDOW_LENGTH, [ENDLAG_ON_HIT]);
}
```

## attack_update.gml

Text to replace (3 placeholders): `[MOVE_NAME]`, `[MOVE_NAME]`, `[FINAL_WINDOW]`

```
// attack_update.gml

if (attack == AT_[MOVE_NAME]) {
    if (window == 1 && window_timer == 1) {
        reset_window_value(AT_[MOVE_NAME], [FINAL_WINDOW], AG_WINDOW_LENGTH);
    }
}
```

