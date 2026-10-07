# Vector Databases with the Federalist Papers

Three Jupyter labs that introduce vector databases using [Chroma](https://www.trychroma.com/). Labs 1 and 2 use the 85 Federalist Papers; Lab 3 measures retrieval quality on the SciFact benchmark of scientific claims.

| Notebook | What you do |
| --- | --- |
| `lab1_federalist_semantic_search.ipynb` | Chunk, embed, and store the papers in Chroma; run semantic queries with metadata filters; compare semantic and keyword search |
| `lab2_federalist_authorship.ipynb` | Use the Lab 1 vectors to classify the 12 disputed papers, then build function-word style vectors and compare the two representations |
| `lab3_scifact_retrieval_eval.ipynb` | Load the SciFact benchmark (5,183 abstracts, 300 judged claims) into Chroma; score semantic, BM25, and hybrid search with recall@k, MRR, and nDCG@10 |

Everything runs locally on a laptop CPU. There is no database server to start and no API key to obtain.

## Setup

You need **Python 3.10, 3.11, or 3.12**. (Newer Python versions may not yet have wheels for all of Chroma's dependencies.)

```bash
git clone https://github.com/dberndt-usf/vector_db_federalist.git
cd vector_db_federalist

python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate

pip install -r requirements.txt
jupyter lab
```

Then open `lab1_federalist_semantic_search.ipynb` and run the cells in order with **Shift+Enter**.

If you already use VS Code, you can skip `jupyter lab`: open the notebook and choose the `.venv` interpreter as the kernel.

## What happens the first time you run Lab 1

- **Embedding model download.** Chroma's default model (`all-MiniLM-L6-v2`) downloads about 80 MB on first use and is cached for later runs.
- **Text download.** The notebook fetches the papers from Project Gutenberg (eBook #1404) and saves a local copy as `pg1404.txt`. If Gutenberg throttles the request, place `pg1404.txt` next to the notebook and rerun the cell.
- **Database creation.** Lab 1 writes a `federalist_db/` folder. Embedding the 1,920 chunks takes one to three minutes.

## What happens the first time you run Lab 3

- **Data download.** The notebook fetches BEIR's copy of SciFact from Hugging Face (`corpus.jsonl.gz`, `queries.jsonl.gz`, `test.tsv`, a few megabytes) into a `scifact_data/` folder. The corpus and queries come from a pinned older revision of `BeIR/scifact`, because in April 2026 the current revision was converted to Parquet and no longer has the `.jsonl.gz` files. If the download fails, unzip BEIR's [`scifact.zip`](https://public.ukp.informatik.tu-darmstadt.de/thakur/BEIR/datasets/scifact.zip) and copy `corpus.jsonl`, `queries.jsonl`, and `qrels/test.tsv` into `scifact_data/`.
- **Database creation.** Lab 3 writes a `scifact_db/` folder. Embedding the 5,183 abstracts takes several minutes; the optional chunking extension adds a second collection and several more minutes. Both collections are reused if you restart the kernel.

## Run order

**Run Lab 1 before Lab 2.** Lab 2 reads the `federalist_db/` folder that Lab 1 creates, so both notebooks must be in the same folder.

Lab 3 stands alone: it builds its own database and can be run before or after Labs 1 and 2.

## Troubleshooting

| Problem | Fix |
| --- | --- |
| `ModuleNotFoundError: chromadb` in the notebook | The notebook kernel is not using your `.venv`. Select the `.venv` kernel, or run the `%pip install` cell at the top of Lab 1. |
| `TypeError` about `configuration` when creating a collection | You have an old Chroma (0.x). Run `pip install -U "chromadb>=1.0"` and restart the kernel. |
| `Collection [federalist] does not exist` in Lab 2 | Run Lab 1 first, in the same folder. |
| Download from Gutenberg fails | Download `pg1404.txt` manually and place it next to the notebook. |
| Download from Hugging Face fails in Lab 3 | Download BEIR's [`scifact.zip`](https://public.ukp.informatik.tu-darmstadt.de/thakur/BEIR/datasets/scifact.zip), place `corpus.jsonl`, `queries.jsonl`, and `test.tsv` (plain or gzipped) in `scifact_data/`, and rerun the cell. |
| Lab 3 semantic nDCG@10 far below 0.5 | Rerun Part 1, then rebuild the collection with `rebuild=True`. |
