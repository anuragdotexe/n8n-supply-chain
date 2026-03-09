
# 🧠 Supply Chain OTIF Automation (n8n + Supabase)

This repository contains the **n8n workflow blueprint** for an automated supply chain data pipeline. The system monitors incoming sales reports via Gmail, parses multiple CSV attachments simultaneously, and synchronizes the data with a PostgreSQL (Supabase) database for real-time analysis.

## 🚀 The Problem & Solution

Manual tracking of **OTIF (On-Time In-Full)** metrics is prone to human error and delays.

* **The Solution:** This workflow automates the extraction of "Daily Sales" reports, transforming raw dates into standardized database formats and populating a structured schema for business intelligence.

---

## 🛠️ Workflow Architecture

The workflow consists of four primary stages:

1. **Ingestion (Gmail Trigger):** Polls every minute for emails with the subject `daily sales` and automatically downloads attachments.
2. **Parallel Processing:** The workflow splits into two distinct paths to handle different data granularity:
* **Aggregate Path:** Extracts data from `attachment_0` for high-level summaries.
* **Order Line Path:** Extracts detailed product-level data from `attachment_1`.


3. **Data Transformation:** Uses n8n expressions to reformat human-readable dates (e.g., `dd-MM-yyyy`) into ISO-standard database dates (`yyyy-MM-dd`).
4. **Storage (PostgreSQL):** Inserts cleaned rows into two specific tables: `fact_aggregate` and `fact_order_line`.

---

## 📊 Database Schema

The pipeline expects the following structure in your **Supabase/Postgres** instance:

### `fact_aggregate` Table

| Column | Type | Description |
| --- | --- | --- |
| `order_id` | String | Unique Identifier |
| `order_placement_date` | Date | Standardized YYYY-MM-DD |
| `otif` | String | On-Time In-Full Status |

### `fact_order_line` Table

| Column | Type | Description |
| --- | --- | --- |
| `product_id` | Number | Product Identifier |
| `order_qty` | Number | Quantity Ordered |
| `delivery_qty` | Number | Quantity Delivered |

---

## ⚙️ Setup Instructions

### Prerequisites

* **n8n** instance (Desktop or Cloud)
* **Supabase** account (with a PostgreSQL database)
* **Gmail API** credentials

### Deployment

1. **Import Workflow:** Copy the contents of `My workflow.json` and paste them into your n8n canvas.
2. **Configure Credentials:**
* Set up your **Gmail OAuth2** credentials.
* Connect your **PostgreSQL** credentials using your Supabase connection string.


3. **Create Tables:** Ensure the `fact_aggregate` and `fact_order_line` tables exist in your database before running the trigger.
4. **Activate:** Switch the workflow to **Active** to begin polling for emails.

---

## 📈 Impact

* **Zero Latency:** Data is available in the database within 60 seconds of an email arriving.
* **Accuracy:** Eliminates date-formatting errors through automated parsing logic.
* **Scalability:** Built to handle multiple attachments in a single execution.
