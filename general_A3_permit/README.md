# Danone PTW: A3 Combined Permit Design Guide & Instructions

This document provides step-by-step instructions on how to tweak, resize, and align the A3 Combined Permit (`general_A3_permit/index.html`) layout. 

---

## 1. Column Layout & Bottom Alignment
To keep the A3 Landscape layout looking uniform and clean, all three columns in the checklist zone stretch to have the **exact same vertical height** at the bottom.

* **The Stretched Grid Container:** 
  The three columns are wrapped in `.three-col-zone`. In the CSS stylesheet, this has:
  ```css
  .three-col-zone {
    display: grid;
    grid-template-columns: 1fr 1fr 1fr;
    gap: 4px;
    margin-bottom: 3px;
    align-items: stretch; /* Forces equal height for Col 1, 2, and 3 */
  }
  ```
  Since the containers stretch, their bottom borders align perfectly.

* **Pushing Bottom Cards (e.g. Self Declaration / Close Out Checklist) to the Bottom:**
  Inside the columns, we use `margin-top: auto;` to push the closeout cards to the absolute bottom of the stretched height:
  * **Column 1:** The **Authorization Matrix** has `style="margin-top: auto;"` which pushes it and the subsequent stacked cards (Handover, Closure, Close Out Checklist) down.
  * **Column 2:** The side-by-side **Fire Watch & Self Declaration** container has `style="margin-top: auto;"` to align them.

---

## 2. Adjusting Workmen Table vs. PPE Grid Heights
At the top of the permit, the **Names of Workmen & Signs** card and **Personal Protective Equipment** card are side-by-side. To align their bottom boundaries:

* **Workmen Table Height:** 
  The Workmen Table contains **3 parallel pairs of Name/Sign columns** (6 columns total in the body) across 6 rows. The cell spacing is controlled in CSS:
  ```css
  .workmen-table th, .workmen-table td {
    padding: 2px 3px;
    font-size: 11.5px;
  }
  ```

* **PPE Section Height:**
  The PPE grid displays cells horizontally in a 5×2 layout. Its height is mainly determined by the size of the PPE icons:
  ```css
  .ppe-icon {
    width: 100%;
    height: 38px; /* Tweak this height to expand or shrink the PPE card vertically */
    display: flex;
    align-items: center;
    justify-content: center;
  }
  .ppe-icon img {
    max-height: 38px; /* Must match the container height */
    max-width: 100%;
    object-fit: contain;
  }
  ```
  * *Tip:* If the Workmen table height changes, increase or decrease `.ppe-icon { height: XXpx; }` to make them end at the exact same level.

---

## 3. Spacing the Stacked Cards in Column 1
In Column 1, the following sections are vertically stacked under the general work checklist:
1. **Authorization Matrix**
2. **Permit Handover / Extension**
3. **Permit Closure**
4. **Permit Close Out Checklist** (Post-work checks)

* **Vertical Margins:** 
  To change the vertical gaps between these stacked cards, adjust the `margin-top` or `margin-bottom` inline styles in the HTML elements:
  * Authorization Matrix: `margin-top: auto; margin-bottom: 12px;`
  * Handover / Extension: `margin-top: 3px; margin-bottom: 0;`
  * Permit Closure: `margin-top: 3px; margin-bottom: 0;`
  * Close Out Checklist: `margin-top: 4px; margin-bottom: 2px;`

* **Card Paddings:** 
  Card paddings can be modified using:
  ```css
  .auth-box {
    padding: 3px; /* Increase to add more interior space in names/signatures */
  }
  ```

---

## 4. Valid From / Valid Until Relocation
The validity inputs are located in the details card (Card 1) inside a 6-column grid layout:
```html
<div class="field-grid" style="grid-template-columns: 1.5fr 1.1fr 1.1fr 0.8fr 1fr 140px; margin-top:2px;">
```
* **Adjusting Widths:**
  * **`140px`** at the end is the width of the stacked validity block.
  * If you need to make the validity inputs wider, increase `140px` (e.g. to `160px`) and slightly decrease other columns (like changing `1.5fr` to `1.4fr`) to balance the horizontal space.

---

## 5. Adjusting Yes / N/A Column Widths in Checklist Rows

Each checklist row uses a **5-column CSS grid**: `No | Description | Yes | N/A | Photo`.

The widths are controlled by this rule in the `<style>` block (around line 115):
```css
.cl-item {
  display: grid;
  grid-template-columns: 16px 1fr 22px 22px 42px;
  /*                     No   Desc  Yes  N/A  Photo */
}
```
The **header row** uses the exact same template and must stay in sync:
```css
.cl-header-row {
  display: grid;
  grid-template-columns: 16px 1fr 22px 22px 42px;
}
```

**To change Yes / N/A column widths:**
* Increase `22px` (the 3rd and 4th values) to any desired width, e.g. `28px` or `30px`.
* The `1fr` (Description column) **automatically shrinks** to compensate — it takes the remaining space — so the Description will get slightly narrower. This is the correct behaviour.
* Always update **both** `.cl-item` and `.cl-header-row` together so the header and data rows remain aligned.

*Example — to make Yes/N/A columns 28px wide:*
```css
grid-template-columns: 16px 1fr 28px 28px 42px;
```

---

## 6. Increasing Vertical Size of Fire Watch & Self Declaration

The Fire Watch panel and Self Declaration sit **side-by-side** inside Column 2, wrapped in this container (search for `Side-by-side Fire Watch` in the HTML):
```html
<div style="display: grid; grid-template-columns: 1.15fr 0.85fr; gap: 4px; margin-top: auto; margin-bottom: 2px;">
```

### To increase their vertical height (fill more white space):
These panels stretch because the columns use `align-items: stretch` in `.three-col-zone`. Since Column 3 (Height Work) is the tallest, Columns 1 and 2 stretch to match it. The Fire Watch & Self Declaration section will automatically grow taller if Column 3 grows taller.

**If you still see white space above Fire Watch / Self Declaration:**  
The `margin-top: auto;` on the container pushes it to the bottom. To instead have it **fill the white space from the top**, try removing `margin-top: auto;` and adding a `min-height` or simply increase the padding inside the panels.

### To increase font sizes inside Fire Watch & Self Declaration:
These use inline `style` attributes — find the block in the HTML and change all the `font-size: 10.5px` values:
```html
<!-- Fire Watch section -->
<div class="fw-panel-title" style="font-size: 10.5px; ...">  ← change this
<div class="fw-row-inline" style="font-size: 10.5px; ...">   ← and this

<!-- Self Declaration -->
<div class="inline-title green" style="font-size: 11px; ...">    ← header
<p class="decl-inline" style="font-size: 10.5px; ...">           ← body text
<div class="decl-field" style="font-size: 10.5px; ...">          ← Name/Sign/Date
```
> **Tip:** Increasing `padding` values (e.g. from `3px` to `6px` on `.fw-panel-inline`) also pushes the section taller without changing font size, distributing the white space evenly across the three panels.
