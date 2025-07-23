# Wayback Machine Website Scraper

This Python script is designed to scrape the content of an archived website from the Internet Archive's Wayback Machine. It starts from a given URL, recursively follows all internal links, extracts the main content from each page, and saves it into a single, formatted Microsoft Word document (`.docx`).

## Features

- **Recursive Scraping**: Traverses an entire archived website by following internal links.
- **Robust Error Handling**: Implements a retry mechanism with exponential backoff to handle network errors and server rate-limiting.
- **Formatted Output**: Preserves basic HTML formatting such as headings (`<h1>-<h4>`), paragraphs (`<p>`), lists (`<ul>`, `<ol>`), and bold/italic text.
- **Duplicate Prevention**: Keeps track of visited Wayback Machine URLs and processed original URLs to avoid redundant scraping and infinite loops.
- **Incremental Saving**: Saves progress to the output `.docx` file after each page is successfully scraped, ensuring no data is lost if the script is interrupted.
- **Content Deduplication**: Checks for unique page content to avoid adding identical pages that might exist under different Wayback Machine timestamps.

## Requirements

- Python 3.x
- `requests`
- `beautifulsoup4`
- `python-docx`

You can install the necessary libraries using pip:
```bash
pip install requests beautifulsoup4 python-docx
```

## Usage

1.  **Configure the Script (Optional)**:
    Open `webscrapper.py` and modify the variables in the `main()` function if you want to scrape a different site:
    - `start_url`: The initial Wayback Machine URL to begin scraping from.
    - `output_filename`: The name of the Word document where the content will be saved.
    - `original_site_domain`: The domain of the original website. This is used to ensure the scraper stays within the target site's archive and doesn't wander off to external links.

2.  **Run the Script**:
    Execute the script from your terminal:
    ```bash
    python webscrapper.py
    ```

3.  **Find the Output**:
    The script will print its progress in the console. Once finished, the scraped content will be available in the specified `.docx` file (e.g., `Formatted_Scraped_Content.docx`).

## How It Works

1.  **Initialization**: The `main` function sets the initial parameters and creates a new Word document in memory.
2.  **Fetching**: The `scrape_page` function calls `get_with_retries` to fetch the HTML content of a URL. This function will retry several times with increasing delays if it encounters connection errors, which is common when scraping the Wayback Machine.
3.  **Parsing**: `BeautifulSoup` is used to parse the HTML. The script first removes the header injected by the Wayback Machine to isolate the original page content.
4.  **Formatting and Extraction**: The `add_content_with_formatting` function iterates through the tags in the page's `<body>`. It identifies headings, paragraphs, and lists and adds them to the Word document while preserving their structure and basic styling (bold/italic).
5.  **Link Following**: The script finds all `<a>` tags within the content area, validates that they point to the same original domain, and recursively calls `scrape_page` for each valid link.
6.  **Saving**: After processing each page, the script saves the entire Word document to disk, ensuring that progress is not lost.
