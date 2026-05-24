# ConstructPro — Labor Costing Module
## Integration Guide & RSMeans Metric Methodology

---

## Files Delivered

| File | What it does |
|------|-------------|
| `labor.js` | Complete labor module — crew DB, calculation engine, UI panels |

---

## Step-by-Step Integration into Your App

### 1. Copy the file
Place `labor.js` into your `/modules/` folder alongside `boq.js`, `mto.js`, etc.

### 2. Edit `index.html` — Add the Tab
Inside the `.mod-tabs` div (after the Spec DB tab):
```html
<button class="mod-tab labor" id="mt-labor" onclick="switchModule('labor')">👷 Labor Costing</button>
```

### 3. Edit `index.html` — Add the Panel Wrapper
After the `<div class="module-wrap" id="mod-spec">` closing `</div>`, add:
```html
<div class="module-wrap" id="mod-labor">
  <div id="labor-panels" style="padding:0 20px 40px;"></div>
</div>
```

### 4. Edit `index.html` — Load the Script
Before `<script src="modules/app.js"></script>`, add:
```html
<script src="modules/labor.js"></script>
```
Load order must be: `state.js` → `data.js` → `nav.js` → `boq.js` → `mto.js` → `pricebook.js` → **`labor.js`** → `project.js` → `exports-excel.js` → `specdb.js` → `app.js`

### 5. Edit `base.css` — Add Tab Colors
```css
.mod-tab.labor.active { background: var(--orange); color: #0d1117; }
.mod-tab.labor:hover  { color: var(--orange); }
```

---

## How RSMeans Labor Costs Are Calculated

### The Master Formula
```
Labor Cost = Quantity × LH/Unit × Loaded Hourly Rate (₱/hr)
```

### Each Term Explained

**Quantity** — the scope of work in the crew's native unit (m², m³, kg, m, set).
This comes from your MTO or BOQ quantities.

**LH/Unit (Labor-Hours per Unit)** — how many man-hours it takes one worker to produce one unit of output.
Derived from RSMeans using the formula:
```
LH/Unit = (Total crew man-hours per day) ÷ (Daily output quantity)
        = (Σ member_count × 8 hr) ÷ outputQty
```

**Loaded Hourly Rate (₱/hr)** — the blended cost of the entire crew per hour of work:
```
Crew Daily Rate (₱/day) = Σ (member_count × daily_wage)
Loaded Hourly Rate      = Crew Daily Rate ÷ 8 hr/day
```

### Worked Example — Formwork: Beams & Girders

**Crew C-2A:**
| Role | Count | Daily Wage | Subtotal |
|------|-------|-----------|---------|
| Carpenter Foreman | 1 | ₱850 | ₱850 |
| Carpenter (Skilled) | 4 | ₱750 | ₱3,000 |
| Helper (Unskilled) | 2 | ₱550 | ₱1,100 |
| **TOTAL** | **7** | | **₱4,950/day** |

**Crew Daily Rate** = ₱4,950/day
**Loaded Hourly Rate** = ₱4,950 ÷ 8 = **₱618.75/hr**
**Daily Output** = 18.0 m²/day
**Total Man-Hours per Day** = 7 workers × 8 hr = 56 man-hrs
**LH/m²** = 56 ÷ 18.0 = **3.111 LH/m²**

**For a project with 120 m² of beam formwork:**
```
Total LH   = 120 m² × 3.111 LH/m²   = 373.3 man-hrs
Labor Cost = 373.3 hr × ₱618.75/hr  = ₱231,056
Crew-Days  = 373.3 hr ÷ 56 MH/day   = 6.67 crew-days
```

---

## RSMeans Imperial → Metric Conversion Factors

| RSMeans Unit | Multiply By | → Metric Unit |
|-------------|-------------|--------------|
| LH/SF | × 10.7639 | LH/m² |
| LH/CY | × 1.30795 | LH/m³ |
| LH/LF | × 3.2808 | LH/m |
| LH/lb | × 2.2046 | LH/kg |
| $/CY (USD) | ÷ 0.7646 × exchange rate | ₱/m³ |

**Productivity Note:** Philippine construction productivity is applied at 55–75% of the RSMeans US baseline for structural trades (formwork, rebar, concrete), and 80–90% for MEP trades. This means the LH/Unit values in the database are already adjusted upward from the raw RSMeans figures.

---

## Crew Database — All Trades

### DIVISION 03 — CONCRETE WORKS

| Crew Code | Trade | Crew Composition | Output | LH/Unit |
|-----------|-------|-----------------|--------|---------|
| C-2A | Formwork — Beams | 1 Foreman + 4 Carpenters + 2 Helpers | 18 m²/day | 3.111 LH/m² |
| C-2B | Formwork — Columns | 1 Foreman + 4 Carpenters + 2 Helpers | 15.5 m²/day | 3.613 LH/m² |
| C-2C | Formwork — Elevated Slabs | 1 Foreman + 5 Carpenters + 3 Helpers | 22 m²/day | 3.273 LH/m² |
| C-2D | Formwork — Grade Beams | 1 Foreman + 3 Carpenters + 2 Helpers | 20 m²/day | 2.400 LH/m² |
| C-2E | Formwork — Retaining Walls | 1 Foreman + 4 Carpenters + 2 Helpers | 16 m²/day | 3.500 LH/m² |
| C-2F | Formwork — Stairs | 1 Foreman + 4 Carpenters + 1 Helper | 9 m²/day | 5.333 LH/m² |
| C-5A | Rebar — General | 1 Foreman + 4 Ironworkers + 1 Helper | 1,200 kg/day | 0.040 LH/kg |
| C-5B | Rebar — Slabs | 1 Foreman + 5 Ironworkers + 2 Helpers | 1,600 kg/day | 0.040 LH/kg |
| C-14A | Concrete — Footings | 1 Foreman + 2 Finishers + 6 Helpers | 8 m³/day | 9.000 LH/m³ |
| C-14B | Concrete — Columns | 1 Foreman + 2 Finishers + 4 Helpers | 4.5 m³/day | 12.444 LH/m³ |
| C-14C | Concrete — Elevated Slabs | 1 Foreman + 4 Finishers + 6 Helpers | 10 m³/day | 8.800 LH/m³ |
| C-14D | Concrete — Beams | 1 Foreman + 2 Finishers + 5 Helpers | 6 m³/day | 10.667 LH/m³ |

### DIVISION 04 — MASONRY

| Crew Code | Trade | Crew Composition | Output | LH/Unit |
|-----------|-------|-----------------|--------|---------|
| D-1A | CHB Laying 4" | 1 Foreman + 3 Masons + 3 Helpers | 55 m²/day | 1.018 LH/m² |
| D-1B | CHB Laying 6" | 1 Foreman + 3 Masons + 3 Helpers | 45 m²/day | 1.244 LH/m² |
| D-2A | Cement Plaster | 1 Foreman + 3 Masons + 3 Helpers | 65 m²/day | 0.862 LH/m² |

### DIVISION 09 — ARCHITECTURAL FINISHES

| Crew Code | Trade | Output | LH/Unit |
|-----------|-------|--------|---------|
| TL-1A | Floor Tiling (≤600mm) | 28 m²/day | 1.714 LH/m² |
| TL-1B | Wall Tiling (≤300×600) | 20 m²/day | 2.400 LH/m² |
| PT-1A | Interior Paint 2-coat | 150 m²/day | 0.267 LH/m² |
| CL-1A | Gypsum Ceiling Board | 30 m²/day | 1.600 LH/m² |
| WP-1A | Waterproofing Membrane | 60 m²/day | 0.667 LH/m² |

### DIVISION 16/26 — ELECTRICAL

| Crew Code | Trade | Output | LH/Unit |
|-----------|-------|--------|---------|
| E-1A | Branch Wiring in Conduit | 120 m/day | 0.333 LH/m |
| E-1B | Panelboard Installation | 1 panel/day | 32 LH/panel |
| E-1C | Lighting Fixtures | 20 fixtures/day | 1.600 LH/set |
| E-1D | Power Outlets | 30 outlets/day | 1.067 LH/set |

### DIVISION 22 — PLUMBING

| Crew Code | Trade | Output | LH/Unit |
|-----------|-------|--------|---------|
| P-1A | Cold Water PPR 25mm | 60 m/day | 0.667 LH/m |
| P-1B | Soil/Waste uPVC 100mm | 45 m/day | 0.889 LH/m |
| P-1C | Sanitary Fixtures | 4 sets/day | 8.000 LH/set |

### DIVISION 07 — ROOFING / DIV 31 — SITE WORKS

| Crew Code | Trade | Output | LH/Unit |
|-----------|-------|--------|---------|
| F-3A | Long Span Roofing | 70 m²/day | 0.686 LH/m² |
| B-11A | Manual Excavation | 8 m³/day | 7.000 LH/m³ |
| B-11B | Compacted Backfill | 12 m³/day | 4.000 LH/m³ |

---

## How Labor Costs Flow to BOQ

```
Labor Module (labor.js)
        │
        │  syncLaborToBoq()
        ▼
BOQ.laborData[trade]  ──→  distributed as labRate in:
  • BOQ.fwData    (Formwork)
  • BOQ.rbData    (Rebar)
  • BOQ.crData    (Concrete)
  • BOQ.archData  (Architectural / Masonry)
  • BOQ.elecData  (Electrical)
  • BOQ.plumbData (Plumbing)
        │
        ▼
boqPanelSummary() picks up labRate per row
→ Total = (matRate + labRate) × qty
→ Applied markup + contingency + VAT
→ Grand Total BOQ
```

The `syncLaborToBoq()` function distributes each trade's labor cost proportionally across BOQ rows based on their quantities. Alternatively you can manually type a `Lab Rate` in any BOQ row.

---

## Wage Basis (DOLE NCR 2024)

| Role | Daily Rate |
|------|-----------|
| Foreman / Leadman | ₱850 |
| Carpenter (Skilled) | ₱750 |
| Ironworker (Skilled) | ₱750 |
| Mason (Skilled) | ₱750 |
| Electrician (Licensed) | ₱750 |
| Plumber (Licensed) | ₱750 |
| Tile Setter | ₱750 |
| Painter | ₱750 |
| Semi-Skilled Laborer | ₱650 |
| Unskilled Helper | ₱550 |

You can override these per-project using the **Rate Override** field in each labor line item.

---

*ConstructPro Labor Module v1.0 — RSMeans Div 03/04/07/09/16/22/31 adapted for Philippine metric construction*
