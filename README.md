# automate_image_export

A Python script that automates exporting one piece of artwork from Affinity Designer at several sizes. It drives the Export dialog with [PyAutoGUI](https://pyautogui.readthedocs.io/), then moves each exported PNG into a folder named after the artwork.

It saves the repetitive clicking of exporting the same illustration at multiple resolutions.

> **Status:** a personal script tied to one screen layout. See [Known issues](#known-issues) before running it.

## How it works

For each size you pass in, the script:

1. Clicks **File**, then **Export...**
2. Clicks the size field in the export settings and types the size
3. Clicks **Export**, types the file name `<name>_<size>.png` and clicks **Save**
4. Waits a moment, then moves the saved file into `./<name>/`

It creates the `./<name>/` folder first if it doesn't exist.

## Requirements

- Python 3.7 or later (it uses `dataclasses`)
- [PyAutoGUI](https://pypi.org/project/PyAutoGUI/)
- Affinity Designer, with the document you want to export open and in front

```bash
python3 -m pip install pyautogui
```

## Usage

Run it from the folder where Affinity Designer saves exported files:

```bash
python3 automate_export.py "helloworld" --list 128 512
```

This exports `helloworld_128.png` and `helloworld_512.png`, and moves both into `./helloworld/`.

| Argument | Meaning |
| --- | --- |
| `FILENAME` | Base name for the exported files, and the name of the folder they're moved into |
| `--list` | One or more sizes to export |

PyAutoGUI's fail-safe is on by default, so you can stop a run by moving the mouse to a corner of the screen.

## Configuring screen positions

The script clicks fixed screen coordinates, not buttons found by name. They're defined near the top of `automate_export.py` and match the screen layout it was written for (a graphics tablet used as a second screen below the main monitor):

| Name | What it points at |
| --- | --- |
| `fileButton` | The **File** menu |
| `exportButton` | **File → Export...** |
| `sizeField` | The size box next to "Size:" in Export Settings |
| `exportSettingsButton` | The **Export** button in Export Settings |
| `saveButton` | The **Save** button in the save dialog |

To use it on a different setup, update these `Coord(x, y)` values. PyAutoGUI's `pyautogui.position()` prints the current mouse position, which makes finding them easy.

## Known issues

- **The script crashes as committed.** `main()` calls `save_and_move(args.FILENAME, args.FILESIZES)`, but the function takes a third argument, `filepath`, so Python raises a `TypeError` before anything is clicked. Passing `filepath` through fixes it.
- **The coordinates are specific to one machine.** They need updating for any other screen layout or Affinity Designer window position.
- **Output is always PNG.** The file type is a constant at the top of the script.
- **There's no error handling if an export fails.** The script waits a fixed second after saving and then tries to move the file, so a slow save will raise a `FileNotFoundError`.
