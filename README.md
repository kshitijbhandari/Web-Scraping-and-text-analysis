# Web Scraping and Text Analysis

A two-stage NLP pipeline that scrapes article text from 114 Blackcoffer insight URLs and computes 13 readability and sentiment metrics for each article, writing results to an Excel file.

---

## Project Structure

```
├── scraped_file.ipynb        # Stage 1 – Web scraping
├── text_analysis.ipynb       # Stage 2 – Text analysis
├── Input.xlsx - Sheet1.csv   # Input file with URL_ID and URL columns
├── Output Data Structure.xlsx# Template for the output columns
├── output_new.xlsx           # Final output with all computed metrics
├── scraped_data/             # Folder where scraped .txt files are saved
├── stopwords/                # Custom stopword lists (.txt files)
└── MasterDictionary/
    ├── positive-words.txt    # 2006 positive sentiment words
    └── negative-words.txt    # 4783 negative sentiment words
```

---

## Stage 1 – Web Scraping (`scraped_file.ipynb`)

Reads URLs from `Input.xlsx - Sheet1.csv` and scrapes each article using `requests` and `BeautifulSoup`.

**What it does:**
- Targets the `div#td-outer-wrap.td-theme-wrap` container on each page
- Extracts the `<h1>` title and all `<p>` tag content
- Filters out boilerplate lines (navigation links, footer text, etc.)
- Saves each article as a `.txt` file in `scraped_data/`, named using the URL + URL_ID
- 3 URLs were found to be broken and are skipped (indices 7, 20, 107), resulting in **111 successfully scraped files**

**Libraries:** `requests`, `beautifulsoup4`, `pandas`, `tqdm`

---

## Stage 2 – Text Analysis (`text_analysis.ipynb`)

Reads each scraped `.txt` file and computes the following 13 metrics:

| # | Metric | Description |
|---|--------|-------------|
| 1 | Positive Score | Count of words matching the positive-words dictionary |
| 2 | Negative Score | Count of words matching the negative-words dictionary |
| 3 | Polarity Score | `(pos - neg) / (pos + neg + ε)` — ranges from -1 to +1 |
| 4 | Subjectivity Score | `(pos + neg) / (total tokens + ε)` |
| 5 | Avg Sentence Length | Total tokens / number of sentences |
| 6 | % Complex Words | Words with 2+ vowel syllables as a % of total words |
| 7 | Fog Index | `0.4 × (Avg Sentence Length + % Complex Words)` |
| 8 | Avg Words per Sentence | Same as Avg Sentence Length |
| 9 | Complex Word Count | Raw count of complex words |
| 10 | Word Count | Token count after removing NLTK English stopwords |
| 11 | Syllable per Word | Average vowel count per word (proxy for syllables) |
| 12 | Personal Pronouns | Count of `I, we, my, ours, us` (excludes `US` the country) |
| 13 | Avg Word Length | Total character length of all tokens / token count |

**Stopwords** are loaded from the custom `stopwords/` folder (14,231 words) in addition to NLTK's built-in English stopwords.

The 3 broken URLs are filled with `'-'` placeholders to keep row alignment with the original input, and results are saved to `output_new.xlsx`.

**Libraries:** `nltk`, `pandas`, `numpy`, `re`, `collections`

---

## How to Run

1. **Install dependencies**
   ```bash
   pip install requests beautifulsoup4 pandas openpyxl tqdm nltk numpy lxml
   ```

2. **Download NLTK data** (first run only)
   ```python
   import nltk
   nltk.download('punkt')
   nltk.download('stopwords')
   ```

3. **Run Stage 1** — Open and run all cells in `scraped_file.ipynb`. This creates the `scraped_data/` folder and populates it with 111 `.txt` files.

4. **Run Stage 2** — Open and run all cells in `text_analysis.ipynb`. This reads the scraped files and produces `output_new.xlsx`.

---

## Output

`output_new.xlsx` contains one row per URL (114 rows total) with all 13 metrics populated. Rows for the 3 inaccessible URLs are marked with `'-'`.
