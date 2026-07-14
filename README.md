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

## Adding to GitHub

```bash
git init
git add index.html README.md
git commit -m "Circuit builder"
git branch -M main
git remote add origin <your-repo-url>
git push -u origin main
```

To host it for free with GitHub Pages: repo **Settings → Pages → Deploy from branch → main → / (root)**.

## Ideas for extending it

- Multiple batteries / series-parallel battery packs
- Capacitors and simple transient (RC charge/discharge) animation
- A potentiometer part with a draggable wiper
- Snap-to-terminal wire dragging instead of click-click
- Save multiple circuits / a gallery of example circuits
