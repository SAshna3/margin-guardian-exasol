# Margin Guardian: Track 02 - Returns & Margin Erosion Analytics

## Overview
**Margin Guardian** is a high-performance e-commerce analytics solution built for **Exasol Local Starter Kit**. It directly addresses **Track 02: "Returns that erode the margin on the sale"**, uncovering hidden financial losses caused by reverse logistics, restocking labor, processing fees, and unsellable inventory write-offs.

---

## Track 02 Focus & Problem Statement
While top-line gross revenue may look healthy, product returns silently destroy net profitability. Traditional reporting tools struggle with real-time join complexity across multi-layered supply chain data. **Margin Guardian** leverages Exasol’s in-memory processing engine to instantly calculate net profit erosion per product category and customer segment.

---

## Database Architecture (`MARGIN_ANALYSIS` Schema)
The database structure is optimized for relational integrity and fast analytical queries using five core tables:
- **`PRODUCTS`**: Stores product details, unit prices, and Cost of Goods Sold (COGS).
- **`CUSTOMERS`**: Categorizes buyers by market segment (Retail, D2C, Wholesale) and region.
- **`ORDERS`**: Captures transaction timestamps and fulfillment shipping modes.
- **`ORDER_ITEMS`**: Links orders to specific purchased products and quantities.
- **`"RETURNS"`**: Tracks return reasons, restockability status (boolean), reverse shipping costs, and handling fees.

---

## Setup & Execution Guide

### Prerequisites
* Exasol Personal Local Starter Kit installed and running locally.
* Command-line access via `exapump`.

### Step 1: Open the Interactive SQL Shell
Run the following command in your terminal:
```bash
exapump interactive -p starter-kit
### Step 2: Run Schema & Analytical Queries
Execute the DDL, data insertions, and analysis from `schema_and_queries.sql` inside your Exasol session.

---

## Key Findings & Business Impact
* **The Electronics Paradox:** High-value categories can show impressive gross margins on paper, but a single non-restockable return with heavy reverse logistics costs easily plunges net profit into negative figures.
* **Actionable Logistics:** Retailers can use these real-time metrics to adjust restocking fees or restrict return policies on vulnerable product lines.

## License
Distributed under the Apache 2.0 License. See `LICENSE` for details.
