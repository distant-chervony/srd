# `srd` — simple `xrandr`

## Preamble
I like to work in low-light environments, but even the minimum brightness of my monitors (using all the available settings) is still too bright.\
At least for me and my dark dev corner.

Over time, and after many experiments, I've developed a couple of useful scripts to mitigate this.\
I'd like to share my Bash solution to this problem with you.

## Quick Features Overview

- Intelligent options handling
- Quite robust, regex-driven guardrails
- Dynamic connected/primary display detection
- The option to hardcode specific displays
- And, last but not least:
 - Clean, understandable errors, so you can catch mistyped values
 - User-friendly help menu

## Installation

### IMO, the Cleanest Path

1. `cd` into the directory that is in your `PATH`
 - For example

 ```bash
 cd ~/.local/bin
 ```

2. Get the tool
 - Fetch only the `srd` directly

 ```bash
 wget https://raw.githubusercontent.com/distant-chervony/srd/refs/heads/main/srd/bash/srd
 ```

3. Make it executable
 - Run 

 ```bash
 chmod u+x ./srd
 ```

Now nobody else can mess with your `srd` :) 

And that's it. You can now use it.

----------

Theoretically, you can swap `wget` for `curl`, download the file through your browser, or `git clone` the repository if you'd like to track updates.

Alternatively, instead of dropping the script into your `.local/bin/`, you can put it wherever you like and simply create an alias in your shell.

For example:

```bash
alias srd='/home/user/Downloads/tools/srd'
```

Or you can place it somewhere else and launch it using a path relative to your current working directory.  

For example, with the full prompt:

```bash
[user@machine ~/scripts]$ ./srd -b 6
```

## Configuration

On the very top of the script, you'll find the following lines:

```bash
# ==============================
# Start of the Display Settings
# ==============================

DETECT_PRIMARY=0
OUTPUTS=()
ADVANCED_MODE=0

#=============================
# End of the Display Settings
#=============================
```

*What do these options mean?*

### 1. `DETECT_PRIMARY`

This is a boolean-like numeric toggle.

When this option is turned off, i.e., `0`, and no displays are specified in the `OUTPUTS` list, `srd` will detect every currently connected display (as reported by `xrandr`) and apply the configuration to them.

However, if you set this option to `1`, `srd` will detect only the primary display and apply the configuration to it.

### 2. `OUTPUTS`

This is an indexed array of strings containing the names of the outputs as they appear in `xrandr`.

You can specify them like this:

```bash
OUTPUTS=('DP-1' 'HDMI-0')
```

If `OUTPUTS` is left empty, then the script will dynamically fetch currently connected displays (according to `xrandr`) and apply the configuration to them.

### 3. `ADVANCED_MODE`

This is a boolean-like numeric toggle.

When this option is turned off (the default), the script allows you to quickly and easily change the required values, while applying additional guardrails.

In normal mode, the script accepts single digits from `2` to `9`, representing values from `0.2` to `0.9`, respectively.

For example:

```bash
srd -b 7 -g 8
```

will set the brightness to `0.7` and the gamma to `0.8`.

When `ADVANCED_MODE` is set to `1`, you can tune each gamma channel individually with values ranging from `0.01` to `2`.

It also allows you to set the brightness above `1` using `-b`, within a range of `0.1` to `2`.

## Usage Details

- The tool is intentionally kept plain, self-contained and easily reversible. It doesn't maintain any history, memory, or cache.
- `srd`'s defaults are the default brightness and gamma values used by `xrandr`, i.e. `1`.
- When invoked without any flags, it simply restores everything to the default values.

> [!NOTE]
> The tool has internal caps, so a typo won't leave you with an undistinguishable picture.
>
> * For single integers:
>  * from 2 to 9
>
> * For floats (advanced mode only):
>  * from 0.1 (for `-b`) or 0.01 (for `-g`) to 2

*P.S. Althought it adds a bit of cognitive load to memorize this, I felt the need to introduce this difference.*\
*When working in the dark, I usually keep only the red gamma channel to preserve night vision, but I don't need that same logic applied to brightness.*

### Examples

The `-b` flag controls brightness, while `-g` controls gamma.

By default, `-b` expects a single integer, while in advanced mode it expects a float.\
Similarly, `-g` expects a single integer by default, while in advanced mode it accepts three comma-separated values (without spaces).

The following example sets the brightness to `0.7` and leaves gamma at its default value:

```bash
srd -b 7
```

This sets the brightness to `0.5` and all three RGB gamma values to `5:5:5`:

```bash
srd -b 5 -g 5
```

And this sets the brightness to `0.7` and the RGB gamma values to `1:0.8:0.5`:

```bash
srd -b 7 -g 1,0.8,0.5
```

There is also the `-f` flag, which temporarily inverts the `ADVANCED_MODE` setting for the current command.

For example, this command forces advanced mode, sets the brightness to `0.7` and sets the RGB gamma values to `1:0.25:0.05`:

```bash
srd -f -b 0.7 -g 1,0.25,0.05
```

> [!TIP]
> Type `srd -h` or `srd --help` to take a glance at the correct syntax.

## Acknowledgements
- [xrandr](https://gitlab.freedesktop.org/xorg/app/xrandr): Underlying brightness and gamma manipulation.

## License
[MIT](./LICENSE)
