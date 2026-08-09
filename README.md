# SysMon (Pi-mon)

Live, lightweight system monitor designed for the terminal (Raspberry Pi friendly). SysMon draws a persistent single- or multi-line status area at the top of your terminal showing temperature, CPU, RAM, voltage, disk read/write and network I/O. It also provides an optional desktop launcher.

![System Monitor](Sysmon-v1.0.1B)
![System Monitor](2Sysmon-v1.0.1B)

## Features
- Live per-second updates in the terminal (low overhead).
- Per-terminal background monitor so your shell prompt remains usable.
- Optional desktop launcher that opens a terminal running the monitor.
- Auto-detects best network interface and default terminal.
- Optional Raspberry Pi voltage reading via `vcgencmd`.
- Safe per-tty PID files to avoid duplicate monitors in the same terminal.

## Requirements
- Linux with procfs (/proc and /sys) - developed for Raspberry Pi but works on other Linux systems.
- Bash (POSIX-ish; tested in bash).
- A real terminal device (must be run in a real tty; not a redirected/pipe environment).
- Optional: `vcgencmd` on Raspberry Pi for voltage readings.
- Optional: a desktop environment (for the desktop launcher).

## Quick install (one line)
From a shell on the target machine:
```bash
curl -fsSL https://raw.githubusercontent.com/SheepTactics/Pi-mon/main/main%20bash | bash
```
or
```bash
wget -qO- https://raw.githubusercontent.com/SheepTactics/Pi-mon/main/main%20bash | bash
```

This installer will:
- install the monitor to `~/.local/bin/sysmon.sh`
- install the launcher as `~/.local/bin/stat`
- optionally add a Desktop entry `~/Desktop/SysMon.desktop` when visual mode is selected
- add `~/.local/bin` to your `~/.bashrc` if needed

When the installer runs it prompts:
- `1` - Terminal command only (default)
- `2` - Terminal command + Desktop icon (visual mode)

## Manual usage
- Start monitor in current terminal:
  - Run: `stat` (this starts the monitor in the current tty)
- Stop monitor in current terminal:
  - Run: `stat stop`
- Restart:
  - Run: `stat restart`
- Pass through to real coreutils `stat`:
  - `stat FILE...` will call the system `stat` if available.

If `stat` is not found immediately after install, refresh your shell:
```bash
export PATH="$HOME/.local/bin:$PATH"
hash -r
```
or open a new terminal.

## What the monitor shows
- TMP: temperature in °C (if available via /sys/class/thermal)
- CPU: CPU usage as percentage
- RAM: RAM usage as percentage
- PWR: voltage (vcgencmd) if available
- R/W: disk read/write rate (KB/s or MB/s)
- I/O: network in/out (KB/s or MB/s)

## How it works (brief)
- The `stat` launcher spawns `sysmon.sh` in the background for the current tty and creates a per-tty PID file in `$XDG_CACHE_HOME` (or `~/.cache`).
- `sysmon.sh` queries `/proc` and `/sys` once per second, computes deltas for disk/network, and redraws a top status area by setting a terminal scroll region so your shell output scrolls below the status area.
- The monitor is careful to avoid duplicate instances for the same tty and cleans up terminal state when it exits.

## Desktop launcher
If you install in visual mode the script will create `~/Desktop/SysMon.desktop`. The installer tries to pick a terminal program from this list (first available):
`gnome-terminal, xfce4-terminal, lxterminal, mate-terminal, konsole, xterm, alacritty, kitty`.

The `.desktop` file runs `stat` inside a terminal and leaves an interactive shell after `stat` exits.

## Troubleshooting
- "SysMon must be run in a terminal." - ensure you're running `stat` in a real tty (tty prints `/dev/pts/*`).
- If `stat` does not start: check `~/.cache/sysmon-<tty_id>.pid` and ensure no stale PID is blocking start.
- If images do not display on GitHub: the repo contains `Sysmon-v1.0.1B` and `2Sysmon-v1.0.1B` used as example screenshots.
- Voltage shows `N/A`: you do not have `vcgencmd` or the platform doesn't provide the required PMIC values.
- To remove the desktop icon in terminal-only mode the installer deletes any existing `~/Desktop/SysMon.desktop`.

## Files in this repository
- `main bash` - Installer script (paste/curl this to run the installer).
- `Sysmon-v1.0.1B`, `2Sysmon-v1.0.1B` - example screenshots/GIFs.
- (After install) files placed on user system:
  - `~/.local/bin/sysmon.sh` - monitor script
  - `~/.local/bin/stat` - launcher
  - `~/Desktop/SysMon.desktop` - optional desktop launcher

## Contributing
- Bug reports and pull requests welcome. Open an issue with details and a reproducible case.
- Please include terminal type, distro, and whether you're on Raspberry Pi when reporting hardware/temperature issues.
