# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Single-file static web app (`index.html`) — a clock for the Escola Sabatina (Sabbath School) at a church. The clock represents a 75-minute session starting at 09:00, divided into colored segments. Everything (HTML, CSS, JS) lives in one file. Deployed via GitHub Pages on every push to `main`.

## Time and Degree Mapping

The entire session is **75 minutes**, mapped to 360°. **1 minute = 4.8°**.

To convert minutes to degrees: `minutes × 4.8`

Current schedule (minutes → degrees):
| Stage             | Minutes | Degrees  | Color    |
|-------------------|---------|----------|----------|
| Abertura          | 0–15    | 0–72     | green    |
| Confraternização  | 15–20   | 72–96    | yellow   |
| Lição             | 20–60   | 96–288   | blue     |
| Fechamento        | 60–70   | 288–336  | red      |
| Pastoreio         | 70–75   | 336–360  | orange   |

## What Must Be Updated When Changing Stage Durations

All of these must be kept in sync whenever stage boundaries change:

1. **`segments` array** — `start`/`end` in degrees for each colored wedge
2. **`labels` array** — `midDeg` for each segment label (use midpoint of the degree range)
3. **`licaoStartDeg` / `licaoSpan`** — start degree and span (in degrees) of the Lição arc where weekday labels are drawn
4. **`licaoStart` / `licaoEnd`** — same boundaries used to compute tick positions
5. **Ticks array** `[0, 72, 96, 288, 312, ...licaoTicks]` — boundary degrees between stages
6. **`alertStages`** — `end` values in minutes for each stage transition alert
7. **`licaoMinStart`** — minute where Lição begins (used for live countdown logic)
8. **`currentStageKey(m)` function** — the `if (m < X)` thresholds must match the schedule

## Lição Sub-divisions (Days of the Week)

The Lição block is divided into 7 equal parts for the days: Sábado, Domingo, Segunda, Terça, Quarta, Quinta, Sexta.

`licaoDuracao = licaoMinEnd - licaoMinStart` (currently 40 min).

Each day's slice = `licaoDuracao / 7` minutes.

## Alert Messages

`alertStages` drives the countdown messages outside of the Lição block. Each entry fires when `minutesElapsed < stage.end`. The `urgente` animation triggers when ≤1 minute remains in any sub-stage.

## Clock Mechanics

- Full 360° rotation = 75 minutes (not 60)
- `minuteDeg = (elapsed / 75) * 6` — converts elapsed seconds to degrees
- `degToTimeStr(deg)` converts degrees to wall-clock time (base 09:00)
- The minute hand is draggable to simulate any point in the session
- `setInterval(update, 50)` — refreshes every 50ms

## Deployment

Push to `main` → GitHub Actions deploys to GitHub Pages automatically. No build step needed.
