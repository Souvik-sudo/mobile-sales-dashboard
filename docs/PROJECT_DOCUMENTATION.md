# Mobile Sales Dashboard — Project Documentation

## 1. Overview

**Project Name:** Mobile Sales Dashboard
**Tool Used:** Microsoft Power BI Desktop
**Author:** Souvik Maity
**Learning Source:** Power BI course by Satish Dhawale (Skill Course)
**Type:** Personal learning project

### 1.1 Objective

To build a single-page, interactive Power BI dashboard that analyzes mobile phone
sales data across brands, cities, payment methods, and time, while practicing:

- Data cleaning and transformation in Power Query
- DAX measure creation
- Custom visual design and branding
- Interactive filtering with slicers

### 1.2 Business Questions Answered

- What are the total sales, quantity sold, and number of transactions overall?
- How does sales volume change month to month and day to day?
- Which cities generate the most revenue?
- Which brands and models sell the most?
- How do customers prefer to pay (UPI, card, cash)?
- How satisfied are customers, based on ratings?

---

## 2. Dataset

| Detail | Value |
|---|---|
| Source file | `Day - 30 - Mobile Sales Data.xlsx` |
| Sheet used | Sheet 1 |
| Format | Excel workbook (.xlsx) |
| Granularity | One row per transaction |

### 2.1 Key Columns (post-cleaning)

| Column | Description |
|---|---|
| Transaction ID | Unique identifier per sale |
| Date | Transaction date (cleaned/derived — see Section 3) |
| Day Name | Day of week (regenerated — see Section 3) |
| Brand | Phone manufacturer (Apple, Samsung, OnePlus, Vivo, Xiaomi, etc.) |
| Mobile Model | Specific phone model |
| City | Location of sale |
| Units Sold | Quantity sold in the transaction |
| Price per Unit | Unit price |
| Payment Method | UPI / Debit Card / Cash / Credit Card |
| Customer Ratings | Customer-given rating for the transaction |

---

## 3. Data Preparation (Power Query)

All cleaning was done in Power Query Editor before loading data into the report.

**Step-by-step process:**

1. **Loaded the source file** — `Day - 30 - Mobile Sales Data.xlsx`, Sheet 1.
2. **Changed data types** of `Day`, `Month`, and `Year` columns to **Text**, so
   they could be concatenated into a single string.
3. **Created a custom column** named `Date` using the formula:
   ```
   = [Day] & "-" & [Month] & "-" & [Year]
   ```
4. **Converted the new `Date` column's data type** from Text to **Date**.
5. **Repositioned** the `Date` column to sit immediately after `Transaction ID`,
   for readability.
6. **Removed** the original `Day`, `Month`, and `Year` columns, since they were
   now redundant after creating `Date`.
7. **Fixed a data-quality issue** in the `Day Name` column — some values were
   entered inconsistently (e.g., `"sat"` instead of `"Saturday"`). Fixed via:
   - Right-click on the column → **Replace Values**
   - Value to find: `sat`
   - Replace with: `Saturday`
   - Enabled **"Match entire cell contents"** under Advanced options, to avoid
     unintended partial replacements.
8. **Removed the `Day Name` column entirely** and regenerated it cleanly using:
   - Select `Date` column → **Add Column → Date → Day Name**

   This guarantees the day names are always derived correctly from the actual
   date, rather than relying on manual/raw entries.
9. **Repositioned** `Day Name` directly after `Date`.
10. **Closed & Applied** the query to load the cleaned data into the data model.

---

## 4. Data Model & DAX Measures

A single transaction-level sales table (`Sales_Data`) was used. Four core measures were created to
power the dashboard's KPI cards and visuals:

```dax
Total Sales = SUMX(Sales_Data, Sales_Data[Units Sold] * Sales_Data[Price per Unit])
```
> Calculates total revenue by multiplying units sold by price per unit for every
> row, then summing the result (row-by-row calculation, since Total Sales isn't
> a column that exists directly in the source data).

```dax
Total Quantity = SUM(Sales_Data[Units Sold])
```
> Simple sum of all units sold across transactions.

```dax
Transaction = COUNTROWS(Sales_Data)
```
> Counts the number of transactions (rows) in the table.

```dax
Average = AVERAGE(Sales_Data[Price Per Unit])
```
> Average selling price per unit across all transactions.

---

## 5. Report Design

### 5.1 Branding & Color Scheme

- The dashboard uses a **Motorola-inspired color palette**, matched precisely by:
  1. Inserting the Motorola logo image into PowerPoint.
  2. Drawing a rectangle shape over it.
  3. Using **Shape Format → Shape Fill → Eyedropper** to sample the exact blue
     from the logo.
  4. Copying the resulting hex code: **`#0060AA`**.
  5. Applying that hex code to the Power BI report canvas via
     **Format Page → Canvas Background → Color**.

### 5.2 Layout Elements

- **KPI Cards** (Total Sales, Total Quantity, Transaction, Average) arranged in
  a 2×2 grid:
  - Visual style: Tiles
  - Arrangement: Grid, 2 columns × 2 rows
  - Each card includes a **callout icon image** (not the default image field),
    positioned right-of-text, 30% image area size, 6px horizontal gap, right
    vertical alignment.
- **Rounded rectangle backgrounds** behind visuals/cards:
  - Corner rounding: 10% (for cards), 20% (for general panel backgrounds)
  - Shadow applied to bottom-right, color: white (from style)
  - Shadows/borders disabled selectively where visuals overlap, to avoid
    doubled shadow effects.
- **Month navigation slicer** (the vertical list of month buttons on the left):
  - Built from the `Date` hierarchy, using the Month level
  - Multi-button slicer layout → Arrangement: Vertical, Style: Tiles
  - Vertical gap: 2px
  - Background turned off on the button style, for a clean look

### 5.3 Visual Inventory

| Visual Type | Purpose | Configuration |
|---|---|---|
| Line Chart | Total Quantity by Month | X-axis: `Date` (Month → Day hierarchy, drill-up enabled); Y-axis: Total Quantity; Markers: On; Data labels: On |
| Clustered Column Chart | Top 3 Models by Sales | X-axis: Mobile Model; Y-axis: Total Sales; Filter: Top N = 3, By value: Total Sales |
| Pie Chart | Transactions by Payment Method | Legend: Payment Method; Values: Transaction |
| Funnel Chart | Customer Ratings distribution | Category: Customer Ratings; Values: Count of Customer Ratings |
| Area Chart | Total Sales by Day Name | X-axis: Day Name; Y-axis: Total Sales |
| Map (Bubble) | Total Sales by City | Location: City; Bubble size: Total Sales; Map settings: Show levels off, Category levels on |
| Table | Brand-wise summary | Columns: Brand, Total Sales, Total Quantity, Transactions; Alternating row banding (white text / blue background) |
| Slicers (×4) | Interactive filtering | Fields: Mobile Model, Payment Method, Brand Name, Day Name — all styled as dropdowns |

### 5.4 Build Sequence (how each visual was derived)

A notable efficiency technique used throughout the build: rather than creating
every visual from scratch, several charts were created by **copying an existing
configured visual and changing its chart type**, then remapping fields. This
preserved formatting (format painter) while swapping the underlying visual.

1. Built the **Line Chart** first (Total Quantity by Month), applied formatting.
2. **Copied** the line chart → changed it to a **Clustered Column Chart** → remapped
   to Mobile Model / Total Sales → applied Top N = 3 filter → this became the
   "Top 3 Models" visual.
3. **Copied** the clustered column chart again → changed it to a **Pie Chart** →
   remapped Legend to Payment Method, Values to Transaction.
4. **Copied** the clustered column chart again → changed it to a **Funnel Chart** →
   remapped Category to Customer Ratings, Values to Count of Customer Ratings →
   set chart title to "Customer Ratings".
5. Built the **Area Chart** (Sales by Day) independently, X: Day Name, Y: Total Sales.
6. Built the **Table** visual with Brand/Total Sales/Total Quantity/Transactions,
   then applied alternating row color formatting (white text on blue background).
7. Built the **Map visual**: selected the rectangle background, used Format Painter
   to apply the same styling to the map, then configured Location (City) and
   Bubble Size (Total Sales).
8. Added **four slicers** (Mobile Model, Payment Method, Brand Name, Day Name),
   each set to dropdown style, positioned around the report canvas.
9. Addressed overlapping shadow issues between adjacent visuals/cards by turning
   off shadow effects on the visuals causing visible double-shadows.

---

## 6. Tools & Technologies

| Tool | Purpose |
|---|---|
| Power BI Desktop | Report building, data modeling, visualization |
| Power Query Editor | Data cleaning and transformation |
| DAX | Calculated measures |
| Microsoft PowerPoint | Used only to extract the exact brand hex color via the eyedropper tool |

---

## 7. Key Learnings

- How to derive and clean date-related fields from separate Day/Month/Year columns.
- Writing basic but essential DAX measures (SUMX, SUM, COUNTROWS, AVERAGE).
- Matching a report's visual theme to a brand's identity using exact color
  extraction (eyedropper technique via PowerPoint).
- Efficiently reusing/copying visuals and switching chart types instead of
  rebuilding from scratch.
- Building custom slicer layouts (multi-button, vertical, tile-styled) instead
  of relying on default slicer appearance.
- Managing visual layering issues (overlapping shadows/borders) in a dense,
  single-page dashboard layout.

## 8. Possible Future Improvements

- Add a "Publish to Web" live link so viewers can interact with the dashboard
  directly in a browser without opening Power BI Desktop.
- Add tooltips with more granular transaction-level detail on hover.
- Add a YoY or MoM growth indicator if multi-year data becomes available.
- Consider bookmarks for different dashboard "views" (e.g., Executive Summary
  vs. Regional Deep-Dive).

---

## 9. Repository Structure

```text
mobile-sales-dashboard/
│
├── README.md
│
├── dashboard/
│   ├── Mobile_Sales_Dashboard.pbix
│   └── README.md
│
├── data/
│   ├── Day - 30 - Mobile Sales Data.xlsx
│   └── README.md
│
├── screenshots/
│   ├── dashboard_overview.png
│   └── README.md
│
└── docs/
    ├── PROJECT_DOCUMENTATION.md
    └── PROJECT_DOCUMENTATION.pdf
```

> **Repository note:** The filenames above are the recommended final names. Rename the actual files or update this tree to match GitHub exactly before publishing.

## 10. Verification Notes

The following items should be checked against the Power BI report before final publication:

- **Top 3 chart:** This document describes *Top 3 Models by Sales* using `Total Sales`. The main README previously described *Top 3 Models by Quantity*. Use the actual visual configuration consistently.
- **Brand field:** The dataset inventory calls the column `Brand`, while slicer notes call it `Brand Name`. Verify the exact field name.
- **DAX names:** The report currently documents measures `Transaction` and `Average`. Consider renaming these in Power BI to `Total Transactions` and `Average Price`, then update the documentation and visuals accordingly.
- **Price column:** The DAX examples use both `Price per Unit` and `Price Per Unit`. DAX identifiers are case-insensitive, but standardize capitalization for readable documentation.
- **Transaction granularity:** `COUNTROWS(Sales_Data)` counts rows. Confirm that each row represents one transaction, as stated in the dataset section.
- **Average price:** `AVERAGE(Sales_Data[Price Per Unit])` is an unweighted average of transaction-row unit prices. If you intend average selling price per unit across all units sold, use `DIVIDE([Total Sales], [Total Quantity])` instead.
- **Date conversion:** Confirm the Power Query text-to-date conversion uses the intended locale and produces correct dates.
- **Screenshot and PBIX paths:** Confirm the repository filenames match the paths above.
- **Public dashboard:** Add a real Publish to Web URL only if publication is permitted and the data is suitable for public access; otherwise omit the live link.

