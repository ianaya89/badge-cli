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

### Options

| Flag | Long | Values | Default | Description |
|------|------|--------|---------|-------------|
| `-s` | `--speed` | `1`–`8` | `3` | Scroll speed. Comma-separated per message. |
| `-B` | `--brightness` | `25`, `50`, `75`, `100` | `100` | Display brightness (%). |
| `-m` | `--mode` | `0`–`10` | — | Display mode (see below). Comma-separated per message. |
| `-b` | `--blink` | `0`, `1` | — | Blink the message. Comma-separated per message. |
| `-a` | `--ants` | `0`, `1` | — | Animated border ("marching ants"). Comma-separated per message. |
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

Simple message:

```bash
badge "Hello World"
```

Laser mode at speed 5:

```bash
badge -s 5 -m 8 "LASER"
```

Still, centered, half brightness:

```bash
badge -m 4 -B 50 "still text"
```

Blinking with animated border:

```bash
badge -a 1 -b 1 "blinking border"
```

Animation mode with built-in icons:

```bash
badge -m 5 -s 2 "  :heart2:    :HEART2:"
```

Two messages with different speeds and modes:

```bash
badge -s 1,8 -m 0,4 "slow scroll" "fast still"
```

Embed an icon inline:

```bash
badge "I :HEART2: you"
```

Use a custom image as a prefix:

```bash
badge ":path/to/logo.png: my text"
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
