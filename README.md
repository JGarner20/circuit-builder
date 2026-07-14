# Circuit // Builder

A single-file, dependency-free circuit sandbox: drag parts onto a breadboard, wire them together, and watch a real DC solver push current through the circuit — traces glow from teal (idle) to pink (energized), LEDs actually light up.

**[Live-ish demo]:** open `index.html` in any browser, or serve the repo with GitHub Pages.

## Features

- **Parts:** battery, resistor, LED (with 4 colors), switch
- **Wiring:** click a terminal, click another terminal — orthogonal traces auto-route between them
- **Real physics:** a nodal-analysis DC solver (Norton companion models, Gaussian elimination) computes actual node voltages and branch currents every time you edit the circuit — not a canned animation
- **LEDs behave like diodes:** they only light up when forward-biased above their threshold voltage, solved iteratively each frame
- **Live inspector:** click any part or wire to see its current, voltage, and power, and to edit values (resistance, voltage, switch state, LED color)
- **Drag, rotate (`R`), delete (`Del`)** any placed part
- **Export / import** your circuit as a `.json` file

## How the solver works

Every 2-terminal part is reduced to a Norton equivalent: a conductance `g` in parallel with a current source `Isc`.

| Part | Model |
|---|---|
| Wire / closed switch | near-zero resistance (`0.02 Ω` / `0.03 Ω`) |
| Open switch | near-infinite resistance |
| Resistor | its set resistance |
| Battery | small internal resistance (`0.05 Ω`) + a current source equal to `V / R` |
| LED | when forward voltage exceeds ~1.2 V, modeled as a `15 Ω` resistor in series with a ~2 V drop; otherwise near-open |

Every part's terminals become nodes in a nodal-analysis matrix. The app builds the conductance matrix, solves it with Gaussian elimination, checks whether each LED's assumed on/off state was consistent with the result, and re-solves (up to 10 passes) until it converges. That gives real node voltages, which get turned back into per-part current, voltage, and power for the inspector and the glow effects.

## How to launch it

No install, no build step — just open the file:

- **Locally:** double-click `index.html`, or drag it into any browser window.
- **Live on the web:** if GitHub Pages is enabled for this repo, open the Pages URL shown in the repo's **Settings → Pages** (or under **About** on the main repo page).

## How to use it

1. **Place a part** — click a part in the left rail (Battery, Resistor, LED, Switch), then click anywhere on the board to drop it.
2. **Wire it up** — click **Wire**, then click a terminal (the small circle at each end of a part), then click a second terminal to connect them. Click **Wire** again to exit wiring mode.
3. **Inspect / edit** — click any part or wire to see its live current, voltage, and power in the right-hand panel. Batteries, resistors, switches, and LED colors are editable there.
4. **Move, rotate, delete** — drag a placed part to reposition it, press `R` to rotate the selected part, press `Del`/`Backspace` (or use the trash button) to remove it.
5. **Toggle a switch** — click directly on a switch to flip it open/closed.
6. **Save your work** — use **export .json** in the top bar to download your circuit, and **import .json** to load it back in later.

Traces glow from teal (idle) to hot pink (energized) as current flows, and LEDs light up once there's enough forward voltage across them — the whole board updates live as you build.

## Ideas for extending it

- Multiple batteries / series-parallel battery packs
- Capacitors and simple transient (RC charge/discharge) animation
- A potentiometer part with a draggable wiper
- Snap-to-terminal wire dragging instead of click-click
- Save multiple circuits / a gallery of example circuits
