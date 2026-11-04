# klipper-backup-basement

Backup repository for the Klipper configuration files of two 3D printers located in the basement. This repo serves as version-controlled storage so that printer configurations can be restored quickly after a host failure, hardware swap, or major firmware update.

---

## Table of Contents

- [About](#about)
- [Printers](#printers)
- [Repository Structure](#repository-structure)
- [What Is Klipper?](#what-is-klipper)
- [Restoring a Configuration](#restoring-a-configuration)
- [Updating the Backup](#updating-the-backup)
- [Notes](#notes)

---

## About

This repository stores the Klipper `printer.cfg` files and any supporting configuration includes for two printers. Each printer has its own top-level directory so the configs never get mixed up. Changes are committed manually (or via a backup script) whenever a printer's configuration is modified.

---

## Printers

| Directory | Printer | Notes |
|-----------|---------|-------|
| `printer-1/` | Printer 1 | Primary workhorse printer |
| `printer-2/` | Printer 2 | Secondary / specialty printer |

> **Update this table** with the actual make/model of each printer once known (e.g. Voron 2.4, Ender 3, RatRig V-Core, etc.).

---

## Repository Structure

```
klipper-backup-basement/
├── printer-1/
│   ├── printer.cfg          # Main Klipper config
│   ├── moonraker.conf       # Moonraker API server config
│   └── includes/            # Optional: split-out config sections
│       ├── macros.cfg
│       ├── bed_mesh.cfg
│       └── ...
├── printer-2/
│   ├── printer.cfg
│   ├── moonraker.conf
│   └── includes/
│       └── ...
└── README.md
```

> The actual files committed here reflect the real directory layout on each printer's Raspberry Pi (or similar host). Adjust the tree above as the configs grow.

---

## What Is Klipper?

[Klipper](https://www.klipper3d.org/) is an open-source 3D printer firmware that runs the motion-planning calculations on a host computer (typically a Raspberry Pi) and sends low-level step commands to a standard microcontroller board (e.g. SKR, Octopus, Creality). All printer behaviour is defined in plain-text `.cfg` files, which makes them ideal for version control.

---

## Restoring a Configuration

1. **Clone or download** this repository onto the printer's host machine (or transfer the files via SCP/SFTP).

   ```bash
   git clone https://github.com/mjbeatty89/klipper-backup-basement.git
   ```

2. **Copy** the relevant printer's config files to the Klipper config directory (usually `~/printer_data/config/` for recent Klipper installs, or `~/klipper_config/` for older setups).

   ```bash
   cp -r klipper-backup-basement/printer-1/* ~/printer_data/config/
   ```

3. **Restart Klipper** to load the restored configuration.

   ```bash
   sudo systemctl restart klipper
   ```

4. Verify in the Mainsail / Fluidd UI that the printer is online and all config checks pass before attempting a print.

---

## Updating the Backup

After making changes to a printer's configuration, commit the updated files to keep this repo current:

```bash
# On the printer host — copy updated configs into the local clone
cp ~/printer_data/config/printer.cfg klipper-backup-basement/printer-1/printer.cfg

# Commit and push
cd klipper-backup-basement
git add printer-1/
git commit -m "printer-1: describe what changed"
git push
```

A cron job or a [klipper-backup](https://github.com/Staubgeborener/klipper-backup) style script can automate this step so that every config save is captured automatically.

---

## Notes

- Sensitive data such as API keys, passwords, or private network details should be kept out of config files before committing, or the repository should remain **private**.
- Moonraker's `[authorization]` section may contain trusted IP ranges — review before making the repo public.
- Large binary files (e.g. webcam images, timelapse videos) should **not** be stored here; add them to `.gitignore` if needed.
