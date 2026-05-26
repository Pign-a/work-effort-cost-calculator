# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

Single-file vanilla HTML/CSS/JS app: a professional-services project cost calculator for an Argentine market context. No build system, no dependencies, no package manager.

**To run:** open `calculadora-proyecto.html` directly in a browser. No server required, though the live exchange rate fetch may be blocked by CORS if served from `file://` on some browsers — use a local HTTP server (e.g. `npx serve .`) if the API call fails.

## Architecture

Everything lives in `calculadora-proyecto.html` — styles, markup, and script in one file.

**Core calculation (`calcular`):**
- `sb = horas × costo` — cost without break factor
- `cb = sb × factor` — cost with break (what gets loaded into Salesforce)
- `vs = cb × (1 + margen/100)` — suggested sale price
- All USD values are also shown in ARS using the live `usdRate`

**Exchange rate (`fetchRate`):**
- Hits `https://dolarapi.com/v1/dolares/oficial` on load and on manual refresh
- Falls back to `FALLBACK = 1450` ARS/USD and shows the amber alert banner on failure

**UI interactions:**
- Slider (`inp-slide`) and number input (`inp-margen`) are kept in sync — changes to either trigger `calcular()`
- "Copiar" button copies the raw `cb` value (no formatting) for pasting into Salesforce; uses `navigator.clipboard` with a `textarea`+`execCommand` fallback

## Inputs / outputs

| Input | Default | Description |
|---|---|---|
| Cantidad de horas | 64 | Project hours |
| Costo por hora | USD 38 | Hourly rate |
| Factor Break | 1.075 | Internal overhead multiplier (e.g. 1.075 = 7.5%) |
| Margen de venta | 35% | Sale markup applied on top of cost-with-break |

Outputs: Costo sin break, Costo con break (Salesforce value), Venta sugerida, Rentabilidad implícita, Ganancia neta.
