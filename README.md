# Local AI Agent — Pizza Restaurant Review Q&A

A local Retrieval-Augmented Generation (RAG) application that answers questions about a pizza restaurant using customer reviews. It runs entirely offline via [Ollama](https://ollama.com) for embeddings and language modeling, with [Chroma](https://docs.trychroma.com) as the local vector store.

## How it works

1. **Load data** — `vector.py` reads restaurant reviews from `realistic_restaurant_reviews.csv`.
2. **Embed** — Each review (title + body) is embedded using Ollama's `mxbai-embed-large` model.
3. **Store** — Embeddings are persisted to a local Chroma collection (`restaurant_reviews`).
4. **Retrieve + Answer** — `main.py` takes your question, retrieves the top-5 most relevant reviews, and feeds them plus your question to Ollama's `llama3.2` model to generate an answer.

## Project structure

```
local ai agent/
├── main.py            # Interactive Q&A loop (Ollama LLM + retriever)
├── vector.py          # Builds/loads the Chroma vector store from CSV
├── realistic_restaurant_reviews.csv  # Review dataset (Title, Review, Rating, Date)
├── requirements.txt   # Python dependencies
├── .gitignore         # Ignored files/paths
├── chroma_langchain_db/   # Persisted Chroma vector database (created at runtime)
├── chrome_langchain_db/   # Legacy/alternate persist directory (see note below)
└── venv311-test/      # Local virtual environment (gitignored)
```

## Prerequisites

- **Python** 3.9+
- **Ollama** installed and running locally (`ollama serve`)
- Two Ollama models pulled:
  ```bash
  ollama pull mxbai-embed-large
  ollama pull llama3.2
  ```

## Setup

1. Clone or download this folder.
2. Create and activate a virtual environment:
   ```powershell
   python -m venv venv
   venv\Scripts\Activate.ps1
   ```
3. Install dependencies:
   ```powershell
   pip install -r requirements.txt
   ```
4. Start Ollama (if not already running):
   ```powershell
   ollama serve
   ```
5. Pull the required models (in another terminal):
   ```powershell
   ollama pull mxbai-embed-large
   ollama pull llama3.2
   ```

## Usage

Run the interactive agent:

```powershell
python main.py
```

You'll be prompted to enter questions about the pizza restaurant. Type `q` to quit.

Example:
```
Enter your question (q to quit): What do customers say about the pepperoni pizza?
```

## Known issues / notes

- **Chroma rust bindings on Windows:** `chromadb` 1.5.9 loads native Rust bindings (`chromadb_rust_bindings.pyd`) that may fail with *"DLL load failed: the specified procedure could not be found"* on some Windows machines. `vector.py` is configured to use Chroma's pure-Python `SegmentAPI` backend to work around this, which requires `hnswlib` (installed via `pip install hnswlib`; may need Microsoft C++ Build Tools to compile from source).
- **Persist directory typo:** `vector.py` sets `db_location = "./chrome_langchain_db"` while a `chroma_langchain_db/` folder also exists in the repo. They refer to the same intent; ensure you use the same path consistently if you inspect or reset the database.
- **Typos in source:** `main.py` template says "exeprt" (should be "expert"); `vector.py` reads `row["Title"]`/`row["Review"]` directly without `str()` coercion, which may fail on missing values.

## Dependencies

| Package | Purpose |
|---------|---------|
| `langchain` | Core RAG framework |
| `langchain-ollama` | Ollama LLM + embeddings integration |
| `langchain-chroma` | Chroma vector store integration |
| `pandas` | CSV reading |
| `chromadb` (transitive) | Local vector database |
| `hnswlib` (transitive) | Vector index backend for SegmentAPI |

## License

Unspecified. Add a LICENSE file if you intend to share or publish this project.
