# ROV Float Ballast Tuner

An interactive, real-time replica of the **2526 ROV Calculation** spreadsheet (`Tech_Float_Calculation`).
Every input is a live slider — results, charts, and the float/sink verdict recompute instantly as you drag.
No dependencies, no build step, single file.

**Live:** https://ssc-rov.github.io/rov-float-calculator/

## What you can tune

| Control | Default (sheet baseline) |
|---|---|
| Cylinder height | 55 cm |
| Cylinder diameter | **80 / 100 / 110 mm** presets + custom |
| Number of cylinders | 1 (option to add a second, identical one) |
| Net weight per cylinder | 7 kg |
| Additional payload | 0 kg |
| Syringe / ballast volume per cylinder | 550 ml |
| Water density ρ | 998 kg/m³ |
| Drag coefficient Cx | 0.84 |
| g | 9.81 m/s² |
| Target depth | 3 m |
| Syringe intake (live slider) | 0 ml |

## Headline readouts

- **Water needed to sink** — how many ml the syringe must take in before the ROV loses positive buoyancy,
  checked against syringe capacity ("sinks after 320 ml — 58% of syringe" vs "needs 2,165 ml — can never sink").
- **Live intake slider** — drag water into the syringe and watch the state flip in real time; a red tick marks
  the exact sink threshold. The cross-section drawing shows the cylinder riding higher/lower as you tune.
- **Descent charts** — depth s(t) and velocity v(t) with time-to-target-depth, plus the full t/s/v table
  at 0.1 s steps like the spreadsheet.

Configs are shareable: every control is stored in the URL (e.g. `?d=80&n=2&w=2.2`).

## Physics (matches the spreadsheet's formulas)

```
V      = πr²h                        (per cylinder, ×N)
m_req  = ρ·V                         "Required Bottle Mass" — displaced water
extra  = m_req − m_net·N − payload   "Extra mass need to add"
moving = ρ·V_w·N − extra             "Water Mass use for moving" (full-intake descent)
k      = ½ρSCx                       drag coefficient term
ODE    = m_a·s″ = m_w·g − k·s′|s′|   integrated numerically (RK4, dt = 0.01 s), m_a = m_req
```

**Note:** the s(t)/v(t) table printed in the original spreadsheet comes from a closed-form fit that is *not*
consistent with the ODE written in the sheet itself (the sheet's own constants imply ≈3.1 m/s terminal velocity,
not the 1.31 m/s in its table). This site integrates the ODE as written, so trajectories will differ from the
sheet's printed table — the derived masses/volumes match the sheet exactly at baseline.

## Run locally

Open `index.html` in a browser, or:

```
python3 -m http.server 8000
# → http://localhost:8000
```

## Deploy

GitHub Pages serves from `main` (repo settings → Pages). Any push to `main` redeploys automatically.
