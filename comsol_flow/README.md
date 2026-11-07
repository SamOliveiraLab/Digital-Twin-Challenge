# COMSOL flow: how the liquid moves in the chamber

comsol_flow_0-13.npz: u_um_s, v_um_s (frame, y, x) fluid velocity in um/s on a 1 um grid over the chamber (34 x 60), frames 0-13,
x_um / y_um are the grid centres in the same coordinates as frame0_cells.csv (x right, y down, open side at x = 34).
comsol_flow_frame0.csv: the same for frame 0 as a table.
Source: COMSOL 6.2 model of this chamber (Tolulope, Oliveira Lab), colony treated as a porous medium that follows the real cell masks.
Flow runs along the open side (up to 6 um/s) and is almost zero inside the colony.

Question: the cells do not move with the fluid (we checked: 0.09 um per frame). So what does the flow do?
Use it to decide where fresh medium arrives and let that shape growth. Does it beat a simple distance-from-the-opening rule?
