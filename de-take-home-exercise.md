# Data Engineer — Take-Home Exercise

## E-Commerce Product Catalog Consolidation

**Time:** 3–4 days (expect 4–6 hours of actual work)
**Platform:** Complete on Snowflake OR Microsoft Fabric (based on your role)

---

## Scenario

StyleHaus, an online fashion retailer, just acquired UrbanThread — a competing brand. Both companies have independent product catalogs with significant overlap: the same physical products listed under different names, SKUs, and descriptions.

Your job: build a pipeline that ingests both catalogs, identifies duplicate products across the two brands, merges them into a single unified catalog, and serves the result through an API.

The leadership team needs this unified catalog by next quarter to:

- Eliminate duplicate listings on the combined storefront
- Consolidate pricing (customers are finding the same item at two different prices)
- Track which brand contributed which product data (for vendor negotiations)

---

## The Data

Two CSV files are provided.

### `stylehaus_products.csv` (100 rows)

| Column       | Type      | Example                                                |
| ------------ | --------- | ------------------------------------------------------ |
| sku          | string    | SH-NK-AM90-WHT-10                                      |
| product_name | string    | Nike Air Max 90 - White/Black                          |
| brand        | string    | Nike                                                   |
| category     | string    | Men's Running Shoes                                    |
| description  | string    | Classic Nike Air Max 90 with visible Air cushioning... |
| price        | float     | 129.99                                                 |
| color        | string    | White/Black                                            |
| size         | string    | 10                                                     |
| material     | string    | Mesh/Leather                                           |
| in_stock     | boolean   | true                                                   |
| updated_at   | timestamp | 2026-09-15 14:30:00                                    |

### `urbanthread_products.csv` (100 rows)

| Column        | Type      | Example                                         |
| ------------- | --------- | ----------------------------------------------- |
| product_code  | string    | UT-2847391                                      |
| title         | string    | NIKE AIRMAX 90 WHITE BLACK                      |
| manufacturer  | string    | NIKE                                            |
| type          | string    | RUNNING SHOES - MEN                             |
| details       | string    | Air Max 90 sneaker, white and black colorway... |
| retail_price  | float     | 134.99                                          |
| colour        | string    | WHITE/BLK                                       |
| dimensions    | string    | US 10                                           |
| fabric        | string    | Mesh, Leather upper                             |
| availability  | string    | In Stock                                        |
| last_modified | timestamp | 2026-10-01 09:15:00                             |

### Built-in data quality issues

The data intentionally contains:

- **Name variations:** "Nike Air Max 90 - White/Black" vs "NIKE AIRMAX 90 WHITE BLACK"
- **Abbreviations:** "White/Black" vs "WHITE/BLK", "Levi's" vs "Levis"
- **Column name mismatches:** `product_name` vs `title`, `color` vs `colour`, `size` vs `dimensions`
- **Price discrepancies:** same product, different prices across brands
- **Description differences:** one brand has richer descriptions than the other
- **Category formatting:** "Men's Running Shoes" vs "RUNNING SHOES - MEN"
- **~30 products that genuinely overlap** between the two catalogs
- **~70 products unique to each** brand

---

## What to Build

You receive two messy product catalogs. Your goal is to produce a single, clean, deduplicated **gold catalog** and serve it through an API.

How you approach the deduplication is up to you — we want to see your thinking, not a prescribed set of steps.

### Requirements

1. **Ingest both CSVs** into your data platform (Lakehouse tables or Snowflake tables).

2. **Cleanse and standardize** the data. The two sources use different column names, formatting, and conventions. Bring them into a common shape.

3. **Identify and resolve duplicates** across the two catalogs. ~30 products exist in both. Figure out how to find them and merge them. Your approach should use at least one AI/GenAI technique (embeddings, LLM, or both) — beyond that, it's your call.

4. **Produce a gold catalog** — one row per unique product. When two records are merged:
   - Prefer the longer, more descriptive name and description
   - Use the lower price
   - Use the most recently updated value for other fields
   - Track which source brand contributed each field value

5. **Serve it through an API** — build a FastAPI app with:
   - An endpoint to **search** the gold catalog by product name (return top results with relevance)
   - An endpoint to **get** a single product by ID (include source lineage — which brand contributed which field)

---

## What to Submit

1. **Pipeline code** — notebooks or Python scripts, runnable on your platform
2. **FastAPI application** — the API code with a requirements.txt
3. **README** — setup instructions to run the pipeline and API
4. **Writeup (1–2 pages)** covering:
   - Your approach and why you made the choices you did
   - The similarity threshold you chose and why
   - How you designed the LLM prompt and what you learned
   - Trade-offs you made (speed vs accuracy, cost vs quality)
   - What you would improve with more time
   - How you would handle 10M products instead of 100

---

## Evaluation Criteria

| Criteria                     | Weight | What we look for                                                                                                                |
| ---------------------------- | ------ | ------------------------------------------------------------------------------------------------------------------------------- |
| **Pipeline runs end to end** | 25%    | We will run your code. It must work. Partial pipelines that error out score zero here.                                          |
| **Deduplication approach**   | 25%    | How did you find duplicates? What technique did you use and why? Did you catch the obvious overlaps? Are there false positives? |
| **Code quality**             | 20%    | Modular, readable, testable code. Not a single giant notebook cell. Functions, error handling, separation of concerns.          |
| **Gold catalog & lineage**   | 15%    | Is the gold catalog clean? Can we trace which source contributed which field?                                                   |
| **API**                      | 5%     | Clean endpoints, proper response shapes. Does search work?                                                                      |
| **Writeup**                  | 10%    | Clear thinking about trade-offs. Honest about limitations. Thoughtful about scale.                                              |

### What earns bonus points

- Tests (any kind — unit, integration, or data validation queries)
- Handling edge cases: what if a product matches two different products?
- Thinking about cost: how much did the AI calls cost?
- Thinking about scale: what changes at 10M products?

### What we're NOT looking for

- Perfect deduplication accuracy (this is a take-home, not a production system)
- Beautiful UI or frontend
- Complex deployment (Docker, CI/CD, cloud hosting)
- Over-engineering (simpler is better)

---

## Platform-Specific Notes

### If you're using Snowflake

- Use a Snowflake trial account or the credentials we provide
- Snowpark Python or SQL stored procedures for the pipeline
- Snowflake Cortex AI functions are available for AI/GenAI work

### If you're using Microsoft Fabric

- Use a Fabric trial account or the credentials we provide
- Fabric Spark notebooks (PySpark) for the pipeline
- Fabric AI Functions and Azure OpenAI are available for AI/GenAI work

### If you don't have platform access

- Use any Spark environment (local PySpark, Databricks Community Edition)
- Use any AI/LLM API you're comfortable with
- Note in your writeup what you would change for Snowflake/Fabric

---

Good luck. We're excited to see your approach.
