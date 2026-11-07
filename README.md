# Digital Twin Challenge

## The challenge

A digital twin is a computer copy of something real that behaves like the real thing. We built one for bacteria, and now it's your turn!

We grew E. coli bacteria inside a tiny chamber on a chip, about as wide as a strand of hair, and took a picture every 5 minutes. Every cell grows longer, splits into two, and pushes its neighbours out of the way. With just a few rules, a few hundred cells pack the whole chamber.

We give you the very first picture: 248 real cells, where each one is, how long it is, and which way it's facing. We also give you the first 65 minutes of what really happened. The next 70 minutes? Hidden! Build a simulation, starting from the first picture, that predicts them.

No code is given: how you build it is up to you. We want to see how you think, how you approach the problem, and how close you get. Build your twin with **Vivarium** and **Viva-munk** (links below). `images/digital_twin_result.png` shows what our digital twin produces, so you know what a result looks like.

## Files

| file | what it is |
|---|---|
| `starter_notebook.ipynb` | the notebook to fill in: every step you need to show |
| `data/frame0_cells.csv` | the first picture as a table: one row per real cell (position in µm, total length, width, angle, area) |
| `data/chamber.json` | the size of the chamber (34 x 60 µm), which side is open, units |
| `data/real_counts_0-13.csv` | the real number of cells and the area they cover, every 5 minutes for the first 65 minutes (frames 0 to 13) |
| `data/tracks_0-13.csv` | every real cell in every frame 0 to 13 |
| `data/lineage_0-13.csv` | who divided from whom (mother and daughters) |
| `data/real_masks_0-13.npz` | the real cell outlines, frames 0 to 13 |
| `data/real_frame0_phase.png`, `data/real_frame0_masks.png`, `data/real_movie_0-13.gif` | the real pictures and movie |
| `comsol_flow/` | the COMSOL flow: how the liquid moves through the chamber, from a COMSOL model of this exact chip |
| `images/digital_twin_result.png` | what our digital twin produces |
| `requirements.txt` | the packages to install |

## Packages and install

You need Python 3.11 or newer, and git. The packages: **Vivarium** (`process-bigraph`, the simulation framework), **Viva-munk** (`multi-cell`, cells with 2D physics on top of Vivarium), **pymunk** (the physics engine), numpy, pandas, matplotlib and pillow. All of them are in `requirements.txt`:

    python -m venv twin
    source twin/bin/activate          (Windows: twin\Scripts\activate)
    pip install -r requirements.txt jupyter

If you use uv: `uv venv --python 3.11`, `source .venv/bin/activate`, then `uv pip install --no-sources -r requirements.txt jupyter`.

## The rules

A digital twin is only as good as its rules. A rule is anything you assume about how a cell behaves. Don't guess them: measure them from the data. Build your twin by adding the rules one at a time, and after each one, show us what changed.

### Rule 1, cells grow

Every cell gets longer over time. By the same amount every minute, or by the same percentage? Do all cells grow at the same speed? *Where to look:* `data/tracks_0-13.csv`. Follow one `cell_id` from frame to frame and look at its `length_um` over time, then do it for many cells.

### Rule 2, cells divide

At some point a cell splits into two daughters. When? At a fixed size, or after growing a fixed amount since it was born? *Where to look:* `data/lineage_0-13.csv` tells you who is the mother of whom; compare a mother's length in her last frame with her daughters' lengths in their first frame. Look up the "adder" rule for bacteria.

### Rule 3, where a cell sits matters

Fresh food arrives through the open side of the chamber (x = 34 µm). Do cells near the opening grow faster than cells deep inside? *Where to look:* compare each cell's growth speed (rule 1) with its `x_um`.

### Extras

Does the growth speed change over the experiment? Do crowded cells grow more slowly? Can the COMSOL flow in `comsol_flow/` (how the liquid moves through the chamber) explain where cells grow? None of these is needed to do well.

Viva-munk already knows that cells push each other (physics) and can remove cells that leave through the opening; you just have to set them up.

## Hints

- Plot `log(length)` against time: a straight line means the cell grows by the same percentage every minute.
- Cells in the first picture are at different points in their lives; a long cell is probably about to divide.
- In Viva-munk, a cell's `length` is the straight middle part only: total length = `length` + 2 × `radius`.
- Viva-munk already has an `AdderGrowDivide` process you can build on.
- Count only the cells inside the 34 x 60 µm camera window, like the microscope does.
- Every run is a little random: run a few seeds and average them before you trust a change.

## What to hand in

Your notebook (use `starter_notebook.ipynb`: it lists every step), showing:

1. For each rule you add: a plot of your twin's cell count next to the real one (frames 0 to 13), and the **RMSE** of the cell count and of the cell area against the real data. RMSE (root mean square error) is the square root of the average of (your value - real value)² over the frames; lower is better.
2. A table of all your RMSE values, rule by rule, so we can see what each rule changed.

Optional: a few sentences on which rules your twin uses and what in the data backs each one.

## Links and documentation

- Viva-munk, the version to use for this challenge: https://github.com/SamOliveiraLab/Viva-munk
- Vivarium documentation: https://github.com/vivarium-collective/process-bigraph
