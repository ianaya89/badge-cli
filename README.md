# badge-cli

A macOS bash wrapper around [led-name-badge-ls32](https://github.com/jnweiger/led-name-badge-ls32) for controlling USB LED name badges (model CH583 by wch.cn, VID `0x0416` / PID `0x5020`).

---

## Requirements

- macOS (uses `DYLD_LIBRARY_PATH=/opt/homebrew/lib`)
- `sudo` access (required for HID device communication)
- Python 3

Install native dependencies:

```bash
brew install hidapi
```

Install Python dependencies:

```bash
pip3 install pyhidapi pyusb
```

Clone the upstream library to `/tmp`:

```bash
git clone https://github.com/jnweiger/led-name-badge-ls32 /tmp/led-name-badge-ls32
```

---

## Installation

```bash
git clone https://github.com/youruser/badge-cli.git
cp badge-cli/badge ~/bin/badge
chmod +x ~/bin/badge
```

Add to `~/.zshrc`:

```bash
export DYLD_LIBRARY_PATH=/opt/homebrew/lib
```

Then reload your shell:

```bash
source ~/.zshrc
```

> The script has a `BADGE_SCRIPT` variable at the top pointing to `/tmp/led-name-badge-ls32/led-badge-11x44.py`. Edit it if you cloned the library elsewhere.

---

## Usage

```
badge [OPTIONS] "message1" ["message2" ...]
```

### Presets

The fastest way to get a good-looking result. Explicit flags override preset values.

| Preset | Mode | Speed | Blink | Ants | Brightness | Use for |
|--------|------|-------|-------|------|------------|---------|
| `scroll` | scroll-left | 3 | off | off | 100% | Default feel |
| `still` | still-centered | 1 | off | off | 100% | Static display |
| `alert` | scroll-left | 7 | on | on | 100% | Errors / warnings |
| `laser` | laser | 6 | off | off | 100% | Dramatic entrance |
| `chill` | scroll-left | 1 | off | off | 50% | Ambient / idle |
| `party` | animation | 5 | off | on | 100% | Celebration |
| `done` | still-centered | 1 | off | off | 75% | Task complete |

```bash
badge --preset done "Shit done"
badge --preset alert "DEPLOY FAILED"
badge --preset laser "LGTM"
badge --preset alert -b 0 "quiet alert"   # override preset blink
badge --list-presets                       # full preset table
```

### Options

| Flag | Long | Values | Default | Description |
|------|------|--------|---------|-------------|
| `-p` | `--preset` | preset name | — | Named preset (see above). |
| | `--list-presets` | — | — | Print preset table and exit. |
| `-s` | `--speed` | `1`–`8` | `3` | Scroll speed. Comma-separated per message. |
| `-B` | `--brightness` | `25`, `50`, `75`, `100` | `100` | Display brightness (%). |
| `-m` | `--mode` | `0`–`10` | `0` | Display mode (see below). Comma-separated per message. |
| `-b` | `--blink` | `0`, `1` | `0` | Blink the message. Comma-separated per message. |
| `-a` | `--ants` | `0`, `1` | `0` | Animated border ("marching ants"). Comma-separated per message. |
| `-t` | `--type` | `11x44`, `12x48` | `11x44` | Badge display type. |
| `-M` | `--method` | `auto`, `hidapi`, `usb` | `auto` | Communication method. |
| `-D` | `--device-id` | `auto`, `<id>` | `auto` | Target a specific device. |
| `-l` | `--list-icons` | — | — | List all built-in icons. |

### Modes (`-m`)

| Value | Effect |
|-------|--------|
| `0` | Scroll left |
| `1` | Scroll right |
| `2` | Scroll up |
| `3` | Scroll down |
| `4` | Still, centered |
| `5` | Animation |
| `6` | Drop down |
| `7` | Curtain |
| `8` | Laser |
| `9` | Smooth |
| `10` | Rotate |

---

## Examples

```bash
badge "Hello World"
badge --preset done "Shit done"
badge --preset alert "DEPLOY FAILED"
badge --preset laser "LGTM"
badge --preset party "I :HEART2: you"
badge --preset alert -b 0 "quiet alert"       # override preset blink
badge -s 1,8 -m 0,4 "slow scroll" "fast msg"  # multi-message
badge ":path/to/logo.png: my text"             # custom image
```

---

## Built-in Icons

Use icon names wrapped in colons inside any message string:

| Icon | Token |
|------|-------|
| Ball | `:ball:` |
| Happy face | `:happy:` |
| Happy face 2 | `:happy2:` |
| Heart (small) | `:heart:` |
| Heart (small, alt) | `:HEART:` |
| Heart (large) | `:heart2:` |
| Heart (large, alt) | `:HEART2:` |
| Fablab logo | `:fablab:` |
| Bicycle (left) | `:bicycle:` |
| Bicycle (right) | `:bicycle_r:` |
| Owncloud logo | `:owncloud:` |
| Empty/spacer | `::` |

List all icons supported by the library:

```bash
badge --list-icons
```

---

## Notes

- `sudo` is invoked automatically by the script. You will be prompted for your password if needed.
- macOS only. The `DYLD_LIBRARY_PATH` export is required so Python can find the Homebrew-installed `libusb`.
- The upstream library path is hardcoded to `/tmp/led-name-badge-ls32`. Edit the `BADGE_SCRIPT` variable at the top of `~/bin/badge` to point elsewhere.
