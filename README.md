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
```  

### Step 2: Create Schema, Tables & Insert Sample Data 
Once your interactive shell is open, execute the following DDL and DML statements:

```sql
-- Create Schema
CREATE SCHEMA IF NOT EXISTS MARGIN_ANALYSIS;
OPEN SCHEMA MARGIN_ANALYSIS;

-- Create Tables
CREATE TABLE PRODUCTS (
    PRODUCT_ID DECIMAL(18,0) PRIMARY KEY,
    PRODUCT_NAME VARCHAR(200),
    CATEGORY VARCHAR(100),
    UNIT_PRICE DECIMAL(18,2),
    UNIT_COST DECIMAL(18,2)
);

CREATE TABLE CUSTOMERS (
    CUSTOMER_ID DECIMAL(18,0) PRIMARY KEY,
    CUSTOMER_NAME VARCHAR(150),
    SEGMENT VARCHAR(50),
    REGION VARCHAR(50)
);

CREATE TABLE ORDERS (
    ORDER_ID DECIMAL(18,0) PRIMARY KEY,
    CUSTOMER_ID DECIMAL(18,0),
    ORDER_DATE DATE,
    SHIPPING_MODE VARCHAR(50)
);

CREATE TABLE ORDER_ITEMS (
    ORDER_ITEM_ID DECIMAL(18,0) PRIMARY KEY,
    ORDER_ID DECIMAL(18,0),
    PRODUCT_ID DECIMAL(18,0),
    QUANTITY DECIMAL(18,0),
    SALE_PRICE DECIMAL(18,2)
);

CREATE TABLE "RETURNS" (
    RETURN_ID DECIMAL(18,0) PRIMARY KEY,
    ORDER_ID DECIMAL(18,0),
    ORDER_ITEM_ID DECIMAL(18,0),
    RETURN_DATE DATE,
    RETURN_REASON VARCHAR(100),
    RESTOCKABLE BOOLEAN,
    REVERSE_SHIPPING_COST DECIMAL(10,2),
    PROCESSING_COST DECIMAL(10,2)
);

-- Insert Sample Data
INSERT INTO PRODUCTS VALUES
(1, 'Pro Laptop 15', 'Electronics', 1200.00, 800.00),
(2, 'Wireless Headphones', 'Electronics', 150.00, 70.00),
(3, 'Cotton Hoodie', 'Apparel', 60.00, 20.00),
(4, 'Running Shoes', 'Footwear', 110.00, 45.00),
(5, 'Ergonomic Chair', 'Home & Kitchen', 350.00, 180.00);

INSERT INTO CUSTOMERS VALUES
(101, 'Acme Corp', 'Retail', 'North America'),
(102, 'TechWorld', 'D2C', 'Europe'),
(103, 'Jane Doe', 'D2C', 'North America'),
(104, 'Global Supplies', 'Wholesale', 'Asia-Pacific');

INSERT INTO ORDERS VALUES
(94821, 101, '2026-09-01', 'Express'),
(94822, 102, '2026-09-03', 'Standard'),
(94823, 103, '2026-09-05', 'Same-Day'),
(94824, 104, '2026-09-10', 'Standard');

INSERT INTO ORDER_ITEMS VALUES
(1, 94821, 3, 4, 240.00),
(2, 94822, 1, 1, 1200.00),
(3, 94823, 4, 1, 110.00),
(4, 94824, 5, 2, 700.00);

INSERT INTO "RETURNS" VALUES
(501, 94821, 1, '2026-09-07', 'Wrong Size / Fit', TRUE, 12.00, 5.00),
(502, 94822, 2, '2026-09-12', 'Defective Item', FALSE, 25.00, 15.00);
``` 

### Step 3: Run the Margin Erosion Analytics Query
Execute the core query to calculate gross profit versus total return erosion and final net profit:

```sql
SELECT 
    p.CATEGORY,
    COUNT(DISTINCT o.ORDER_ID) AS total_orders,
    SUM(oi.SALE_PRICE - (p.UNIT_COST * oi.QUANTITY)) AS gross_profit,
    SUM(COALESCE(r.REVERSE_SHIPPING_COST, 0) + COALESCE(r.PROCESSING_COST, 0) + 
        CASE WHEN r.RESTOCKABLE = FALSE THEN (p.UNIT_COST * oi.QUANTITY) ELSE 0 END) AS total_return_erosion,
    (SUM(oi.SALE_PRICE - (p.UNIT_COST * oi.QUANTITY)) - 
     SUM(COALESCE(r.REVERSE_SHIPPING_COST, 0) + COALESCE(r.PROCESSING_COST, 0) + 
         CASE WHEN r.RESTOCKABLE = FALSE THEN (p.UNIT_COST * oi.QUANTITY) ELSE 0 END)) AS net_profit
FROM ORDER_ITEMS oi
JOIN PRODUCTS p ON oi.PRODUCT_ID = p.PRODUCT_ID
JOIN ORDERS o ON oi.ORDER_ID = o.ORDER_ID
LEFT JOIN "RETURNS" r ON oi.ORDER_ITEM_ID = r.ORDER_ITEM_ID
GROUP BY p.CATEGORY;
```

### Step 4: Analyze the Results
Review the generated output table in your terminal to compare your **Gross Profit** against **Total Return Erosion**. Look closely at categories like Electronics to uncover hidden financial losses and negative **Net Profit** margins caused by non-restockable returns and reverse logistics.

### Step 5: Exit the Interactive Session
When you are finished executing your queries and capturing your terminal screenshots, you can exit the `exapump` interactive session by typing:

```bash
exit
```
### Step 6: Verify and Document Results
Verify that all five schema tables (`PRODUCTS`, `CUSTOMERS`, `ORDERS`, `ORDER_ITEMS`, and `"RETURNS"`) populated correctly without errors and that your net profit calculation accurately accounts for reverse logistics costs and non-restockable inventory write-offs.
---

## 💡 Key Findings & Business Impact
* **The Electronics Paradox:** High-value categories can show impressive gross margins on paper, but a single non-restockable return with heavy reverse logistics costs easily plunges net profit into negative figures.
* **Actionable Logistics:** Retailers can use these real-time metrics to adjust restocking fees or restrict return policies on vulnerable product lines.

## 📄 License
Distributed under the Apache 2.0 License. See `LICENSE` for details.
