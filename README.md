# Maple Saturation (Plasma)

Screen saturation and gamma control for **KDE Plasma 6 Wayland**, applied through ICC profiles generated at runtime with ArgyllCMS and registered through colord.

Companion project for Hyprland: [maple-saturation-hyprland](https://github.com/MapleProjects/maple-saturation-hyprland).

## How it works

1. `kscreen-doctor -j` lists connected, enabled outputs.
2. `colormgr` registers the display as a colord device if it is missing.
3. The system sRGB profile is decompiled to XML with `iccToXml`.
4. Saturation and gamma are written into the profile.
5. `iccFromXml` compiles it back into an ICC file under `~/.local/share/icc`.
6. `kscreen-doctor output.<name>.iccprofile.<path>` applies it.

Two fixed filenames (`maple_active_a.icc` and `maple_active_b.icc`) are alternated on every apply. Rewriting the same path leaves KWin serving a cached profile, so the rotation forces a reload while keeping the directory to exactly two files.

## Features

- Saturation from 0.00x (grayscale) to 4.00x, slider plus numeric field
- Gamma curve from 0.10 to 3.00
- Per-display selector for multi-monitor setups
- Native KWin color pipeline, no overlays and no GPU filter chain
- `--apply` restores the last saved values silently and exits, useful for autostart
- Settings persist in `~/.config/maple-saturation-plasma/settings.json`
- Dark themed interface

## Install

```bash
paru -S maple-saturation-plasma-git
```

## Usage

```bash
maple-saturation-plasma          # GUI
maple-saturation-plasma --apply  # apply saved values and exit
```

Autostart example (`~/.config/autostart/maple-saturation-plasma.desktop`):

```ini
[Desktop Entry]
Type=Application
Name=Maple Saturation (Plasma)
Exec=maple-saturation-plasma --apply
X-KDE-autostart-after=panel
```

## Dependencies

- KDE Plasma 6 running a Wayland session, with `kscreen-doctor` available
- `python` and `python-pyqt6`
- `argyllcms` and `colord`
- `iccxml` (AUR) for the `iccToXml` / `iccFromXml` converters

A base sRGB profile must exist in one of the usual paths, for example `/usr/share/color/icc/colord/sRGB.icc` (provided by colord).

## Limitations

- There is no contrast control. The GUI group labelled "Contrast Vibrancy" only modifies the gamma curve.
- `--apply` always targets the first detected output, not the display selected in the GUI.
- Plasma/KWin only. On Hyprland or other compositors the colord and kscreen-doctor path does nothing.
- Values above roughly 2.50 can clip saturated primaries.