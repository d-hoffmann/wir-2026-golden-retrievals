# BM25 Search & Evaluation Pipeline

Welcome! This folder contains a fully configurable **BM25 Search & Evaluation Pipeline** built using [PyTerrier](https://pyterrier.readthedocs.io/). 

This pipeline is designed for researchers and students to experiment with and understand how changes in document processing, query cleaning, and parameter tuning affect search engine performance. It uses the **LongEval-Sci** dataset, exactly like the project tutorials.

We provide **two ways** to run this pipeline:
1. 💻 **Command-Line Script (`bm25_pipeline.py`):** Perfect for scripting, terminal power-users, and batch evaluation.
2. 📓 **Jupyter Notebook (`bm25_pipeline.ipynb`):** Perfect for interactive, step-by-step learning, visualization, and rapid qualitative testing inside your web browser.

---

## 📚 What is BM25? (A Quick & Simple Intro)

**BM25** (Best Match 25) is a highly effective, classical ranking function used by search engines to estimate the relevance of a document to a given search query. It calculates a score for each document based on term matching.

It has two key mathematical dials (hyperparameters) that you can turn:
1. **$k_1$ (Term Frequency Saturation):** Controls how much weight is given to a term when it appears multiple times in a document. 
   - A lower $k_1$ (e.g., `0.5` to `1.0`) means that seeing a search word many times in a document doesn't add much extra score (relevance "saturates" quickly).
   - A higher $k_1$ (e.g., `1.5` to `2.0`) allows document scores to keep increasing more as the term is repeated.
2. **$b$ (Document Length Normalization):** Controls how much we penalize long documents. Long documents naturally contain more words, which might make search terms appear more often by chance.
   - $b = 1.0$: Fully penalizes long documents (assumes longer documents are just "fluffy" and verbose).
   - $b = 0.0$: No penalty for long documents (assumes long documents just cover more distinct topics).
   - Standard default is `0.75`.

---

## 🛠️ Installation & Setup

Before running the pipeline, make sure you have installed Python 3.12 (or newer) and the necessary libraries. 

Run the following command in your terminal to install everything:

```bash
pip install pandas python-terrier ir_datasets_longeval
```

*Note: PyTerrier runs on Java. If you do not have Java installed, PyTerrier will attempt to download/install a light Java Virtual Machine (JVM) helper automatically, or you can install Java (JRE/JDK 11 or higher) on your machine.*

---

## 🚀 How to Run the Pipeline

The pipeline is driven by the `bm25_pipeline.py` script. You can run it in three different modes: **Evaluation**, **Single Search**, or **Interactive Shell**.

### 1. Standard Evaluation Mode (Default)
Runs search queries against the collection, evaluates the results using standard metrics (MAP, nDCG, Reciprocal Rank), and outputs a summary table.

```bash
python3 bm25_pipeline.py
```

### 2. Interactive Search Mode
Launches a command-line search engine! Enter any query to see the top 5 matching abstracts, their document IDs, and relevance scores.

```bash
python3 bm25_pipeline.py --interactive
```

### 3. Single Search Mode
Performs a search for a single query and displays the results immediately before exiting.

```bash
python3 bm25_pipeline.py --search "climate change and global warming"
```

---

## ⚙️ Configuration & Experiment Switches

You can pass various flags to the script to test their impacts. Below is a full list of options:

| Flag | Type | Default | Description |
|---|---|---|---|
| `--index-path` | `str` | `./index` | Path where the search index is stored. |
| `--rebuild` | `switch` | `False` | Forces the index to rebuild (required when changing document filters). |
| `--limit-docs` | `int` | `-1` | Limits indexing to only the first `N` documents. **Crucial for super fast prototyping (e.g., `--limit-docs 10000`).** |
| `--filter-english`| `switch` | `False` | Filters out non-English documents during indexing. |
| `--min-words` | `int` | `0` | Ignores documents with fewer than `N` words during indexing. |
| `--remove-stopwords`| `switch`| `False` | Cleans common English stopwords (like "the", "and", "is") from queries before search. |
| `--k1` | `float` | `1.2` | Controls BM25 term frequency saturation. |
| `--b` | `float` | `0.75` | Controls BM25 document length normalization penalty. |
| `--per-query` | `switch` | `False` | Outputs evaluation metrics for every single query instead of just the average. |

---

## 🔬 Recommended Experiments to Try

Here are some systematic tests you can run to understand what differences different pipeline steps make on search quality:

### Experiment 1: The Impact of Quick Prototyping (`--limit-docs`)
Does indexing fewer documents change evaluation scores?
1. Build a mini index of 20,000 documents:
   ```bash
   python3 bm25_pipeline.py --index-path ./mini_index --limit-docs 20000 --rebuild
   ```
2. Observe how the evaluation metrics change compared to indexing the full collection.

### Experiment 2: The Impact of Filtering Non-English Docs (`--filter-english`)
Does purging other languages from a scientific corpus improve retrieval of English queries?
1. Build the index with the fast English filter:
   ```bash
   python3 bm25_pipeline.py --index-path ./index_en --filter-english --rebuild
   ```
2. Check the logs: How many documents were filtered out?
3. Compare the evaluation results (`MAP`, `nDCG@10`) against the unfiltered baseline.

### Experiment 3: Tuning Document Length Normalization (`--b`)
Is a longer scientific abstract more likely to be relevant, or is it just verbose?
1. Try a high length penalty ($b = 1.0$):
   ```bash
   python3 bm25_pipeline.py --b 1.0
   ```
2. Try no length penalty ($b = 0.0$):
   ```bash
   python3 bm25_pipeline.py --b 0.0
   ```
3. Observe which configuration yields a higher nDCG score.

### Experiment 4: Query Stopword Removal (`--remove-stopwords`)
Does removing common connector words (e.g., "the", "a", "for", "with") from queries prevent matching noise?
1. Run with stopwords removed:
   ```bash
   python3 bm25_pipeline.py --remove-stopwords
   ```
2. Run baseline (default):
   ```bash
   python3 bm25_pipeline.py
   ```

---

## 🔮 Coming Next: Embedding-Based Search

This BM25 pipeline is our keyword-matching baseline. In the next stage, we will implement an **Embedding Search Pipeline** (semantic vector search). 

Once both pipelines are ready, you will be able to perform comparative research, answering questions like:
- Does semantic search outperform keyword matching on complex, natural-language queries?
- Can we combine BM25 and vector search (Hybrid Search/Reciprocal Rank Fusion) to get the best of both worlds?
