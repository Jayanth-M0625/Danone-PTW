# Project Rules & Task Context: Danone PTW Permit Design Conversion

## Assignment Context
Transform the spreadsheet-based permit templates (contained in `All_Permit_Templates.xlsx` or `workbook_dump.json`) into visually appealing, clean, print-optimized web forms (HTML & CSS).
The styling, fonts, color palettes, and container grids must match the design pattern established in the LOTO / Line Break permit (`loto/index.html`).

## Permits List & Specifications
- **6 Permits Total:**
  1. **LOTO / Line Break Permit (`loto/`)**
     - **Constraint:** Exactly 2 A4 pages.
     - **Split Point:** Page 1 ends at the end of the *Authorization Matrix*. Page 2 begins with *Permit Handover/Extension* and *Permit Closure* sections, followed by the large *TBT Photo Attachment* section.
     - **Status:** **Completed & Verified**.
  2. **Super Height Permit (`super_height/`)**
     - **Constraint:** Exactly 2 A4 pages.
     - **Status:** **Completed & Verified**.
  3. **Excavation Permit (`excavation/`)**
     - **Constraint:** Exactly 2 A4 pages.
     - **Status:** **Completed & Verified**.
  4. **Confined Space Permit (`confined/`)**
     - **Constraint:** Exactly 2 A4 pages.
     - **Status:** **Completed & Verified**.
  5. **Lifting Permit (`lifting/`)**
     - **Constraint:** Exactly 2 A4 pages.
     - **Status:** **Completed & Verified**.
  6. **Combined Permit (Hot, Height, and General Work combined) (`general_A3_permit/`)**
     - **Constraint:** Exactly 1 A3 page.
     - **Status:** Pending.

## Crucial Design & Print Guidelines
- **Card-Based Layout:** Every card must have `page-break-inside: avoid; break-inside: avoid;` to prevent it splitting across page boundaries.
- **Page Breaks:** Add `<div class="page-break"></div>` at logical split points to enforce page breaks exactly.
- **PPE & Tools Grid Pattern:** PPE and Tools requirements grids must display cells horizontally (e.g., 10 columns for PPE, 7 columns for Tools). Each cell must have:
  - An icon image at the top.
  - English and Hindi text labels.
  - A checkbox centered under the labels.
  - A blank line (`border-bottom: 1px solid #999`) with an input field at the bottom to specify the type/capacity if the checkbox is ticked.
- **Checklist Structure:** Keep checklists structured in 2 columns (Left column and Right column) within each section card. Each checklist section must be a standalone card spanning the entire page width.
- **Documentation:** Use `PROJECT_CONTEXT.md` at the repository root as the primary source of documentation for next tasks.

