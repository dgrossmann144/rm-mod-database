```sp
global.preserve_window_load = true

global.preserve_window_save = fun () {
  ini_open("mods/rmml/preserve_window/config.ini")
  ini_write_real("preserve_window", "window_x", window_get_x())
  ini_write_real("preserve_window", "window_y", window_get_y())
  ini_close()
}
```

# controller

## create

```sp
if global.preserve_window_load {
  alarm_set(0, 1)
} else {
  global.preserve_window_save()
}
```

## alarm_0

```sp
ini_open("mods/rmml/preserve_window/config.ini")
let x = ini_read_real("preserve_window", "window_x", -1)
let y = ini_read_real("preserve_window", "window_y", -1)
if x != -1 and y != -1 {
  let window_width = window_get_width()
  let window_height = window_get_height()

  -- magic to determine if the window would be in bounds
  let offsets = window_get_visible_rects(x, y, x + window_width, y + window_height)

  -- check if all overlaps are zeros
  let i = array_length(offsets) - 1
  while i >= 0 {
    if
      offsets[i-4] != 0
      or offsets[i-5] != 0
      or offsets[i-6] != 0
      or offsets[i-7] != 0
    {
      window_set_position(x, y)
      break
    }
    i -= 8
  }
}
ini_close()
global.preserve_window_load = false
```

## room_start

```sp
if !global.preserve_window_load {
  global.preserve_window_save()

  if !instance_exists(omenu_new) {
    global.rmml.unload()
    return
  }
}
```
