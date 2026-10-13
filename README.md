# hp-pavilion-gaming-15-linux-touchpad-fix
Fix for ELAN touchpad automatically disabling after closing/opening the laptop lid on HP Pavilion Gaming 15 running Linux.

## Problem
This laptop model disables its own touchpad by what it looks by pure accident or conflict with something else when it goes into suspension and then wakes up from it.

## Solution

### 1. Identify the scancodes
Open the terminal and execute this command:
```bash
udevadm info /sys/class/input/event3
```

The output should look like this:
```bash
...
E: KEYBOARD_KEY_d8=!touchpad_off
E: KEYBOARD_KEY_d9=!touchpad_on
...
```

### 2. Create a systemd-hwdb rule
Take note of the touchpad_off and touchpad_on keys then create a hwdb rule with nano:
```bash
sudo nano /etc/udev/hwdb.d/99-hp-touchpad-keymap.hwdb
```

Paste the following content into the file:
```bash
evdev:name:AT Translated Set 2 keyboard:*
  KEYBOARD_KEY_d8=reserved
  KEYBOARD_KEY_d9=reserved
```

Save the changes and exit. If the scancode of any of the keys is different simply change them to fit your system.

### 3. Apply the changes
Update the systemd database with this command:
```bash
sudo systemd-hwdb update && sudo udevadm trigger --subsystem-match=input
```

### 4. Verify the fix
Run the info command again and ensure the keys are now mapped to ```reserved```:
```bash
udevadm info /sys/class/input/event3
...
E: KEYBOARD_KEY_d8=reserved
E: KEYBOARD_KEY_d9=reserved
...
```

### 5. Testing
Close your laptop lid, wait for it to suspend, open it back up, and test if your touchpad remains fully operational.
