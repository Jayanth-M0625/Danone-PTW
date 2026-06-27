# Danone PTW — Project Context & Handoff Document

> **Purpose:** This file is the single source of truth for any AI model or developer continuing work on this project. Read this entirely before making any changes. All design decisions, constraints, and current status are documented here.

---

## 1. Project Overview

This repository converts Danone's Excel-based Permit to Work (PTW) templates into print-optimized, professional HTML/CSS permit forms. The raw Excel data lives in `All_Permit_Templates.xlsx` and has also been dumped to `All_Permit_Templates_Dump.json` (use this for quick reference — no need to parse the Excel). Each permit is a self-contained folder with a single `index.html` file that contains all HTML and CSS inline (no external stylesheets).

---

## 2. Permits — Status Map

| Permit | Folder | Page Format | Status |
|---|---|---|---|
| LOTO / Line Break | `loto/` | A4, 2 pages | ✅ Complete |
| Super Height | `super_height/` | A4, 2 pages | ✅ Complete |
| Excavation | `excavation/` | A4, 2 pages | ✅ Complete |
| Confined Space | `confined/` | A4, 2 pages | ✅ Complete |
| Lifting & Shifting | `lifting/` | A4, 2 pages | ✅ Complete |
| Combined (Hot + Height + General) | `general_A3_permit/` | A3 landscape, 1 page | ✅ Complete |

---

## 3. Reference Permit — LOTO (`loto/index.html`)

The LOTO permit is the **design master**. When in doubt about styling, spacing, fonts, or component structure, refer to this file. All other A4 permits must match its visual style.

---

## 4. Universal Design Rules (apply to ALL permits)

### 4.1 Page Setup
- `@page { size: A4; margin: 5mm; }` for all A4 permits
- `@page { size: A3 landscape; margin: 5mm; }` for the combined A3 permit
- **No forceful page breaks.** Do NOT insert `<div class="page-break"></div>` anywhere. Page breaks were removed from all permits including LOTO. If the content overflows, the user scales it manually in the browser print dialog.

### 4.2 Card-Based Layout
Every logical section is a `.card` container. To optimize printable area and reduce clutter, all outer borders and corner radiuses are removed:
```css
.card {
  border: none;               /* border removed for seamless transition */
  border-radius: 0;           /* rounded corners removed */
  margin-bottom: 6px;
  padding: 4px 0;             /* side padding removed since border is gone */
  page-break-inside: avoid;
  break-inside: avoid;
}
```
The `page-break-inside: avoid` prevents cards from splitting across pages, but there are **no manual page-break divs**.

### 4.3 Header Structure & Validity Relocation (A4 permits)
Three-column grid for `.header-grid`:
- Left: Danone logo (`images/logo.png`, height ~30px)
- Centre: Permit title (large bold), Hindi subtitle
- Right: `.meta-grid` containing only `Permit No:` and `Date of Issue:` (stacked in a single column).

**Validity Fields Relocation:**
`Valid From` and `Valid Until` are removed from the top-right header `.meta-grid` and shifted into the last row of the permit details card, stacked vertically in the rightmost column (e.g. using flex-direction column and 65px fixed width for labels).

### 4.4 Checklist Layout (A4 permits only)
- Each checklist section is a **full-width card** spanning the page
- Inside the card, items are split into **two columns** using `.two-column-row` with `grid-template-columns: 1fr 1fr`
- Each column contains a `.checklist-table`

Checklist table column widths (enforced):
```css
.col-no    { width: 5%; }
.col-desc  { width: 57%; }
.col-yes   { width: 9%; }
.col-na    { width: 9%; }
.col-photo { width: 20%; padding: 1px; }
```

Row height is fully dynamic to let content flow naturally:
```css
/* Fixed row heights were removed. Row sizing is controlled by cell content and padding */
.checklist-table th,
.checklist-table td {
  padding: 4px 6px;
}
```

Photo column images (checkpoint reference photos):
```css
.photo-box img {
  height: 38px;       /* increased from 28px for better detail readability */
  width: 100%;
  object-fit: contain;
}
```
Images are placed as `<img src="images/checkpoint_X.png">` — these are placeholders for the user to fill in manually. **Do not generate or hard-code any images.**

### 4.5 PPE Grid
- 10 columns: `grid-template-columns: repeat(10, 1fr)`
- **No checkboxes:** All checkboxes are removed from the PPE cells. The cell header/title must state: `Personal Protective Equipment Requirements (specify type/NA) / (प्रकार/एन.ए. निर्दिष्ट करें)`.
- Icon size is scaled up to **36px tall** (since checkboxes are removed).
- Each cell contains: `.ppe-icon` (36px height) → `.ppe-label-en` (9px bold) → `.ppe-label-hi` (8px) → `.ppe-blank` (write-in line with solid `#666` border).

### 4.6 Tools Grid
- 7 columns: `grid-template-columns: repeat(7, 1fr)`
- Identical cell structure as PPE: no checkboxes, title includes `(specify capacity/type/NA) / (प्रकार/क्षमता/एन.ए. निर्दिष्ट करें)`, scaled up icon (36px height), labels, and write-in blank line.
- Placed **below** the PPE grid.

### 4.7 Uniform Checkboxes
To ensure aesthetic consistency, all input checkboxes (e.g. inside checklists and closeout tables) are styled uniformly:
```css
input[type="checkbox"] {
  width: 12px;
  height: 12px;
  margin: 0 auto;
  display: inline-block;
  vertical-align: middle;
  cursor: pointer;
}
```

### 4.8 Workmen Table Headers
Table headers in the workmen registration card are simplified to `Name / नाम` and `Sign / ह०` (instead of `Name of Workman / कार्यकर्ता का नाम`), fitting cleanly on a single row and saving vertical height.

### 4.7 Font & Typography
- Base font: Arial, sans-serif
- Body font-size: `12px` (increased from 10px to meet print readability standards of ~9pt)
- Hindi text: `font-family: 'Mangal', sans-serif` with class `.hi` (`font-size: 11px` general, `10px` in checklists, with `line-height: 1.25` and `margin-top: 3px` to provide clear visual separation from English labels)
- Close Out Checklist Hindi text requires explicit color override to remain visible:
  ```css
  .closeout-table td { color: #111; }
  .closeout-table .hi { color: #333; font-weight: 400; }
  ```

### 4.8 Input Fields
All form fields use:
```css
input[type="text"] {
  border: none;
  border-bottom: 1px dotted #555;
  background: transparent;
  font-size: 11px;
  outline: none;
  width: 100%;
}
```

### 4.9 Authorization Matrix
5 boxes in a row (`grid-template-columns: repeat(5, 1fr)`):
1. Area Owner
2. Permit Authorizer
3. Permit User
4. Contractor Safety Officer
5. Danone Safety Officer

Each box has Name and Sign fields.

### 4.10 Permit Handover / Closure
Two cards side by side (`grid-template-columns: 1fr 1fr`):
- Left: Handover/Extension (Handed over From → Handed over To)
- Right: Permit Closure (Area Owner + Permit Authorizer)

### 4.11 TBT Photo Section
**LOTO only** has an actual TBT image tag inside `.tbt-image-container`:
```html
<img src="images/image_tbt.png" alt="Tool Box Talk Photo Attachment">
```
Coded height constraint: `min-height: 550px;` (width is full card width ~756px).

**All other permits** (Super Height, Excavation, Lifting, Confined Space) use a dashed placeholder box:
```html
<div style="border: 2px dashed #aaa; min-height: 450px; display: flex; 
            align-items: center; justify-content: center; color: #aaa; ...">
  📷 Paste TBT Photo Here
  <span>टीबीटी फोटो यहाँ चिपकाएँ</span>
</div>
```
*Note:* The placeholder height is customized to pack space efficiently per permit:
- Super Height: `min-height: 450px;`
- Excavation: `min-height: 450px;`
- Lifting: `min-height: 450px;`
- Confined Space: `min-height: 350px;` (due to the gas test record and larger checklist on Page 2).

---

## 5. A3 Combined Permit — Special Rules (`general_A3_permit/index.html`)

This is a **single A3 landscape page** combining Hot Work, Work at Height, and General Work into one permit.

### 5.1 Core Layout
## 5. A3 Combined Permit Specifications (`general_A3_permit/index.html`)

This permit combines General Work, Hot Work, and Height Work and is designed to fit on exactly **1 A3 landscape page** (420mm × 297mm).

### 5.1 Balanced Column Layout
To fill out the wide landscape canvas and eliminate large patches of empty vertical whitespace, the checklists and closeout sections are laid out side-by-side inside `.three-col-zone` (3 columns):

```
COL 1 (General Work)    COL 2 (Hot Work)        COL 3 (Height Work)
│ General Pre-Work (8)  │ Hot Work Checks (21)  │ General checks (10)
│ Manual Lifting (5)    │                       │ Boom lift (5)
│                       │                       │ Ladder (3)
│ ── inline below ──    │ ── inline below ──    │ Scaffold (5)
│ Authorization Matrix  │ Fire Watch & Post     │
│ Handover / Closure    │ Hot Work Monitoring   │
│ Close Out Checklist   │ Self Declaration      │
```

*Note: The Tool Box Talk (TBT) Photo Attachment has been ignored/removed from this front A3 view (can be printed on the back).*

### 5.2 Deduplication Rules
These items appear only **once** in the A3 permit (not repeated across columns):
- Work completed / area cleaned → Close Out table (inside Column 1)
- Tools removed → Close Out table
- Other permit closed → Close Out table
- Waste disposal → Close Out table
- Workers briefed on safety (TBT) → General Work column only
- PPE → single shared grid, not inside any checklist column

### 5.3 Column Colour Theme
- General Work: `#2e7d32` (dark green) headers, `#e8f5e9` sub-headers
- Hot Work: `#bf360c` (deep orange) headers, `#fbe9e7` sub-headers
- Height Work: `#0d47a1` (dark blue) headers, `#e3f2fd` sub-headers

### 5.4 Checklist Item Structure (A3)
Uses a **5-column** grid per row: No | Description (en+hi) | Yes ☐ | N/A ☐ | Photo
```css
.cl-item {
  display: grid;
  grid-template-columns: 16px 1fr 16px 16px 42px;
  min-height: 36px;
}
```
Photo column images use `height: 34px; width: 100%; object-fit: contain;`.
Images are named sequentially: `image_g_1_1.png` through `image_H_4_5.png` across all three columns.

### 5.5 Inline Sections
Some sections are appended *inside* their respective checklist column div rather than as separate full-width cards:
- **Authorization Matrix + Handover/Closure + Close Out Checklist** → appended at bottom of Column 1 (General Work column)
- **Fire Watch + Self Declaration** → appended at bottom of Column 2 (Hot Work column)
- **Close Out Checklist** → removed from Column 3 (Height Work column) and unified in Column 1

### 5.6 Bottom Section
The bottom row uses `grid-template-columns: 1fr 1fr`:
- **Left half**: Authorization Matrix (5 boxes) stacked above a 2-col row of Permit Handover + Permit Closure cards
- **Right half**: Single TBT photo placeholder card that stretches to fill the full height of the left half

---

## 6. Image Conventions

| Image | Source path | Notes |
|---|---|---|
| Danone logo | `images/logo.png` | Used in all headers |
| PPE icons | `images/helmet.jpg`, `shoes.jpg`, `goggles.jpg`, `gloves.jpg`, `harness.jpg`, `earmuff.jpg`, `mask.jpg`, `vest.jpg`, `scba.jpg`, `others.jpg` | User provides |
| Tools icons | `images/spanner.jpg`, `hammer.jpg`, `drill.jpg`, `scissor.jpg`, `cutter.jpg`, `chain.jpg`, `others_tool.jpg` | User provides |
| Checkpoint photos | `images/checkpoint_1.png`, `checkpoint_2.png`, etc. | Placeholder — user pastes manually |
| TBT photo | `images/image_tbt.png` | **LOTO only** — user provides; other permits use dashed placeholder |

---

## 7. Excel / JSON Data Source

Raw data is available in `All_Permit_Templates_Dump.json` at the repo root. Keys are sheet names matching the Excel tabs:
- `"LOTO Permit"`
- `"Super Height Permit"`
- `"Excavation Permit"`
- `"Confined Space Permit"`
- `"Lifting Permit"`
- `"Hot Work Permit"`
- `"Height Work Permit"`
- `"General Work Permit"`

Each sheet is an array of rows; each row is an array of cell values (nulls for empty cells). Parse non-null cells to reconstruct checklist items and section headers.

---

## 8. What's Left / Future Tweaks

- All 6 permits are structurally complete. Any remaining work will be cosmetic tweaks requested by the user.
- The user adds all images manually. Do not auto-generate any images.
- Do not re-introduce page-break divs.
- If adding a new permit type, follow the A4 pattern from `loto/index.html`.
