# Vector Databases with the Federalist Papers

Two Jupyter labs that introduce vector databases using [Chroma](https://www.trychroma.com/) and the 85 Federalist Papers.

| Notebook | What you do |
| --- | --- |
| `lab1_federalist_semantic_search.ipynb` | Chunk, embed, and store the papers in Chroma; run semantic queries with metadata filters; compare semantic and keyword search |
| `lab2_federalist_authorship.ipynb` | Use the Lab 1 vectors to classify the 12 disputed papers, then build function-word style vectors and compare the two representations |

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

## Run order

**Run Lab 1 before Lab 2.** Lab 2 reads the `federalist_db/` folder that Lab 1 creates, so both notebooks must be in the same folder.

## Troubleshooting

| Problem | Fix |
| --- | --- |
| `ModuleNotFoundError: chromadb` in the notebook | The notebook kernel is not using your `.venv`. Select the `.venv` kernel, or run the `%pip install` cell at the top of Lab 1. |
| `TypeError` about `configuration` when creating a collection | You have an old Chroma (0.x). Run `pip install -U "chromadb>=1.0"` and restart the kernel. |
| `Collection [federalist] does not exist` in Lab 2 | Run Lab 1 first, in the same folder. |
| Download from Gutenberg fails | Download `pg1404.txt` manually and place it next to the notebook. |
