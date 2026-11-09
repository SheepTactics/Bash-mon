# Bash-Mon

Bash-Mon is a live, lightweight system monitor designed for the terminal. It is Raspberry Pi friendly and displays temperature, CPU, RAM, voltage, disk read/write, and network I/O statistics in a usable terminal.

![System Monitor](Sysmon-v1.0.1B)
![System Monitor](2Sysmon-v1.0.1B)

## Features
- Live per-second updates in the terminal (low overhead).
- Per-terminal background monitor so your shell prompt remains usable.
- Optional desktop launcher that opens a terminal running the monitor.
- Auto-detects the best network interface and default terminal.
- Optional Raspberry Pi voltage reading via `vcgencmd`.
- Safe per-tty PID files to avoid duplicate monitors in the same terminal.

## Requirements
- Linux with procfs (`/proc` and `/sys`). Developed for Raspberry Pi but works on other Linux systems.
- Bash.
- A real terminal device (must be run in a real tty, not a redirected/pipe environment).
- Optional: `vcgencmd` on Raspberry Pi for voltage readings.
- Optional: a desktop environment for the desktop launcher.

## Quick install
From a shell on the target machine:

```bash
curl -fsSL https://raw.githubusercontent.com/SheepTactics/Pi-mon/main/main%20bash | bash
```

or

```bash
wget -qO- https://raw.githubusercontent.com/SheepTactics/Pi-mon/main/main%20bash | bash
```

The installer installs the monitor to `~/.local/bin/sysmon.sh`, the launcher as `~/.local/bin/stat`, and optionally adds a desktop entry at `~/Desktop/SysMon.desktop`. It also adds `~/.local/bin` to your `~/.bashrc` if needed.

When the installer runs, it prompts:
- `1` - Terminal command only (default)
- `2` - Terminal command + Desktop icon (visual mode)

## Manual usage
- Start the monitor in the current terminal: `stat`
- Stop the monitor in the current terminal: `stat stop`
- Restart: `stat restart`
- Pass through to the real coreutils `stat`: `stat FILE...`

If `stat` is not found immediately after install, refresh your shell:

```bash
export PATH="$HOME/.local/bin:$PATH"
hash -r
```

Or open a new terminal.

## What the monitor shows
- TMP: temperature in °C (if available via `/sys/class/thermal`)
- CPU: CPU usage as a percentage
- RAM: RAM usage as a percentage
- PWR: voltage (via `vcgencmd`) if available
- R/W: disk read/write rate (KB/s or MB/s)
- I/O: network in/out (KB/s or MB/s)

## How it works
The `stat` launcher starts `sysmon.sh` in the background for the current tty and creates a per-tty PID file in `$XDG_CACHE_HOME` (or `~/.cache`). The monitor queries `/proc` and `/sys` once per second, computes disk/network deltas, and redraws a status area at the top of the terminal. It avoids duplicate instances for the same tty and restores terminal state when it exits.

## Desktop launcher
In visual mode, the installer creates `~/Desktop/SysMon.desktop`. It tries to select an available terminal from: `gnome-terminal`, `xfce4-terminal`, `lxterminal`, `mate-terminal`, `konsole`, `xterm`, `alacritty`, or `kitty`.

The desktop entry runs `stat` inside a terminal and leaves an interactive shell open after the monitor exits.

## Troubleshooting
- “Bash-Mon must be run in a terminal.” Ensure you're running `stat` in a real tty (`tty` prints `/dev/pts/*`).
- If `stat` does not start, check `~/.cache/sysmon-<tty_id>.pid` and ensure no stale PID is blocking startup.
- If images do not display on GitHub, the repository contains `Sysmon-v1.0.1B` and `2Sysmon-v1.0.1B` as example screenshots.
- Voltage shows `N/A`: `vcgencmd` or the required platform PMIC values may be unavailable.
- In terminal-only mode, the installer removes any existing desktop icon.

## Files in this repository
- `main bash` - Installer script.
- `Sysmon-v1.0.1B`, `2Sysmon-v1.0.1B` - Example screenshots/GIFs.
- After installation: `~/.local/bin/sysmon.sh` (monitor), `~/.local/bin/stat` (launcher), and optionally `~/Desktop/SysMon.desktop` (desktop launcher).

## Contributing
Bug reports and pull requests are welcome. Please include terminal type, distro, and whether you're on Raspberry Pi when reporting hardware or temperature issues.
