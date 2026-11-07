# Digital Twin Challenge

**Before you start:** clone this exact version of Viva-munk (Vivarium with cells) and install it. Other versions won't give the same results.

    git clone https://github.com/SamOliveiraLab/Viva-munk.git
    cd Viva-munk
    git checkout 3fc41887e17f9a435080a751e106e04223446185
    pip install -e .
    cd ..

Then install the rest with `pip install -r requirements.txt jupyter` (more below).

## The challenge

A digital twin is a computer copy of something real that behaves like the real thing. We built one for bacteria, and now it's your turn!

We grew E. coli bacteria inside a tiny chamber on a chip, about as wide as a strand of hair, and took a picture every 5 minutes. Every cell grows longer, splits into two, and pushes its neighbours out of the way. With just a few rules, a few hundred cells pack the whole chamber.

We give you the very first picture: 248 real cells, where each one is, how long it is, and which way it's facing. We also give you the first 65 minutes of what really happened. The next 70 minutes? Hidden! Build a simulation, starting from the first picture, that predicts them.

No code is given: how you build it is up to you. We want to see how you think, how you approach the problem, and how close you get. Build your twin with **Vivarium** and **Viva-munk** (links below). `images/digital_twin_result.png` and `images/digital_twin_video.gif` show what our digital twin produces, so you know what a final result looks like.

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
| `images/digital_twin_result.png` | our final result: the figure and the table under it |
| `images/digital_twin_video.gif` | our final result: the video of our twin next to the real cells |
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

### Rule 3, position

Position is where the cell sits in the chamber: its x and y in µm, taken from its pixel position in the picture. Fresh food arrives through the open side of the chamber (x = 34 µm). Do cells near the opening grow faster than cells deep inside? *Where to look:* compare each cell's growth speed (rule 1) with its `x_um`.

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

1. For each rule you add: a plot of your twin's cell count next to the real one (frames 0 to 13), and the **% error** of the cell count and of the cell area against the real data. % error is the average gap between your value and the real value, as a percentage of the real value. Lower is better.
2. The final result, like ours in `images/`: a figure of (a) the cell count and (b) the total area over time, one line for each set of rules, against the real cells; the table of % error under it; and a video of your twin next to the real cells.
3. A few sentences discussing your results.

## Links and documentation

- Viva-munk, the version to use for this challenge: https://github.com/SamOliveiraLab/Viva-munk
- Vivarium documentation: https://github.com/vivarium-collective/process-bigraph
