# Unified E-Commerce Product Catalog
## StyleHaus × UrbanThread Catalog Consolidation Pipeline & API

A production-ready data engineering solution for consolidating two heterogeneous e-commerce catalogs into a unified, deduplicated **Gold Catalog** with field-level lineage, lowest-price resolution, AI vector similarity, and FastAPI serving.

---

## Quickstart (Under 2 Minutes)

Prerequisite: Python 3.11+.

```powershell
# 1. Navigate to the project directory
cd TakeHomeExercise

# 2. Activate existing virtual environment (or create with `python -m venv .venv`)
.\.venv\Scripts\Activate.ps1

# 3. Install dependencies (if not already installed)
pip install -r requirements.txt

# 4. Run the end-to-end data pipeline
python -m app.pipeline

# 5. Run the automated test suite (11 tests)
pytest -v

# 6. Start the FastAPI microservice and interactive Web UI
uvicorn app.api:app --host 127.0.0.1 --port 8000 --reload
```

Once started, open your browser:
- **Interactive Web UI:** [http://127.0.0.1:8000/](http://127.0.0.1:8000/)
- **Swagger API Documentation:** [http://127.0.0.1:8000/docs](http://127.0.0.1:8000/docs)
- **API Health Check:** [http://127.0.0.1:8000/health](http://127.0.0.1:8000/health)

---

## Key Pipeline Results

| Metric | Result | Note |
| :--- | :---: | :--- |
| **Source Records Ingested** | **164** | 94 StyleHaus (`datasets/stylehaus_products.csv`) + 70 UrbanThread (`datasets/urbanthread_products.csv`) |
| **Unified Gold Catalog Size** | **132** | Deduplicated products in `data/gold_catalog.json` |
| **Duplicates Merged** | **32** | Cross-catalog duplicates identified & verified with LLM in `data/match_audit.json` |
| **Precision / False Positives** | **100% / 0** | Size and brand guardrails prevent false merges |
| **Pricing Discrepancies Fixed** | **31** | Products where prices differed; resolved to lowest price |
| **Average Customer Savings** | **$10.87** | Saved per duplicated item across the storefront |
| **Test Suite Coverage** | **11 / 11 Passed** | Unit, integration, edge-case, and API client tests |

---

## Architectural Highlights

1. **Two-Stage Entity Resolution:**
   - **Deterministic Blocking:** Hard filters on normalized `brand_key` and `size_key` eliminate 97.7% of comparisons (reducing 6,580 pairs to 152 candidates).
   - **AI Vector Embeddings:** Sublinear character 2–5 $n$-gram TF-IDF embeddings capture spelling and spacing differences (`Air Max` vs `AirMax`, `WHT` vs `White`) offline with zero API cost.
   - **Optional OpenAI Embeddings:** Toggle `AI_PROVIDER=openai` in `.env` to switch to `text-embedding-3-small`.
2. **Greedy 1-to-1 Resolution:**
   - Candidate pairs with cosine similarity $\ge 0.74$ are resolved one-to-one, preventing false-positive transitive chaining.
3. **Deterministic Gold Conflict Rules:**
   - **Lowest Price:** Protects consumers and eliminates storefront price mismatches.
   - **Longest Name & Description:** Preserves richer marketing copy.
   - **Most Recent Timestamp:** Preserves fresh inventory status, colors, and sizing.
4. **Complete Field-Level Lineage:**
   - Every attribute in every gold record specifies the exact contributing brand (`StyleHaus` / `UrbanThread`) and source ID (`SH-...` / `UT-...`).

---

## API Endpoints

| Method | Endpoint | Description |
| :--- | :--- | :--- |
| `GET` | `/` | Executive Web UI with Catalog Explorer, Audit Trail, and Architecture |
| `GET` | `/health` | Service health status (`{"status": "ok"}`) |
| `GET` | `/stats` | Catalog KPIs, discrepancy counts, brand/category breakdown |
| `GET` | `/matches` | Audit trail of all 28 merged candidate pairs with similarity scores |
| `GET` | `/brands` | Unique list of all brands in the catalog |
| `GET` | `/categories` | Unique list of all categories in the catalog |
| `GET` | `/products/search` | Search by query (`q=...`), with optional `brand`, `category`, and `merged_only` filters |
| `GET` | `/products/{id}` | Full product details with field lineage, resolution rules, and raw source records |

---

## Project Structure

```
TakeHomeExercise/
├── app/
│   ├── api.py            # FastAPI service with search, lineage & stats endpoints
│   ├── cleaning.py       # Input normalization & canonical schema mapping
│   ├── config.py         # App settings & data paths
│   ├── embeddings.py     # Local char n-gram TF-IDF & OpenAI embedding providers
│   ├── gold.py           # Conflict-resolution engine & lineage attribution
│   ├── llm.py            # Structured LLM prompt module for borderline verification
│   ├── matching.py       # Deterministic blocking & greedy 1-to-1 matcher
│   ├── pipeline.py       # Orchestrates end-to-end ingestion, matching & export
│   └── static/
│       └── index.html    # Executive-grade web interface & side-by-side comparison
├── data/
│   ├── catalog_stats.json # Summary consolidation metrics & pricing savings
│   ├── gold_catalog.json  # Final unified 136-product gold lakehouse catalog
│   └── match_audit.json   # Full audit trail of all 28 accepted duplicate pairs
├── datasets/
│   ├── stylehaus_products.csv   # Source catalog 1 (94 rows)
│   └── urbanthread_products.csv # Source catalog 2 (70 rows)
├── tests/
│   └── test_catalog.py   # 11 unit & integration tests (cleaning, matching, API)
├── .env.example          # Sample environment configuration
├── de-take-home-exercise.md # Assignment specifications
├── PROJECT_EXPLANATION.md # Plain-English guide & presentation walkthrough
├── requirements.txt      # Pinned Python package dependencies
└── WRITEUP.md            # Comprehensive 1.5–2 page technical report
```

---

## Evaluation & Documentation Links

- **Detailed Technical Report:** See [`WRITEUP.md`](file:///C:/Users/2026/Downloads/TakeHomeExercise/TakeHomeExercise/WRITEUP.md) for threshold selection, LLM prompt design, cost analysis, and 10M product scaling plans on Snowflake and Microsoft Fabric.
- **Interview Presentation Guide:** See [`PROJECT_EXPLANATION.md`](file:///C:/Users/2026/Downloads/TakeHomeExercise/TakeHomeExercise/PROJECT_EXPLANATION.md) for plain-English explanations and interview defense talking points.
