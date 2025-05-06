# Webpage Summarizer with LLaMA 3.2

A Python notebook that fetches a webpage, extracts and cleans its text content, and uses the LLaMA 3.2 model via the OLLAMA API to generate a concise summary. Ideal for quickly distilling long-form articles or blog posts into key insights.

---

## Features

- **HTTP Fetching**  
  Retrieves arbitrary web pages with custom headers.

- **HTML Parsing & Cleaning**  
  Uses BeautifulSoup to remove scripts, styles, images, inputs, and extract only human-readable text.

- **LLaMA 3.2 Integration**  
  Pulls the LLaMA 3.2 model with `ollama pull llama3.2` and performs summarization in a single call to the OLLAMA API.

- **Jupyter Notebook**  
  Interactive workflow; displays the summary as rendered Markdown.

---

## Prerequisites

- **Python 3.9+**  
- **Jupyter Notebook** or **JupyterLab**  
- **OLLAMA** CLI & daemon running locally  
- **OLLAMA API key** (if using a protected endpoint)

---

## Installation

1. **Clone the repository**  
   ```bash
   git clone https://github.com/premkumarkora/webpage-summarizer-llama3.2.git
   cd webpage-summarizer-llama3.2
   ```

2. **Create & activate a virtual environment**  
   ```bash
   python3 -m venv venv
   source venv/bin/activate     # Linux/macOS
   venv\Scripts\activate.bat  # Windows
   ```

3. **Install dependencies**  
   ```bash
   pip install -r requirements.txt
   ```

   `requirements.txt` should include:
   ```
   requests
   beautifulsoup4
   ```

4. **Pull the LLaMA 3.2 model**  
   ```bash
   ollama pull llama3.2
   ```

---

## Configuration

1. **OLLAMA API endpoint**  
   In your notebook (or a separate `.env` file), set:
   ```python
   OLLAMA_API = "http://localhost:11434/v1/chat/completions"
   HEADERS = {
       "User-Agent": "Mozilla/5.0 …",
       "Authorization": f"Bearer {OLLAMA_API_KEY}"  # if required
   }
   ```

2. **Custom headers**  
   Adjust `headers` in the HTTP GET request to bypass simple bot filters.

---

## Usage

1. **Launch Jupyter**  
   ```bash
   jupyter notebook Summarizer.ipynb
   ```

2. **Run all cells**  
   - The notebook will:
     1. Fetch and parse the target URL.
     2. Clean the HTML to plain text.
     3. Construct a summarization prompt.
     4. Call the local LLaMA 3.2 model via OLLAMA.
     5. Render the returned summary as Markdown.

3. **Modify target URL**  
   Change the `url` variable to summarize any publicly accessible website.

---

## Code Breakdown

```python
# 1. Pull the model
!ollama pull llama3.2

# 2. Fetch webpage
headers = {
    "User-Agent": "Mozilla/5.0 …"
}
response = requests.get("https://www.koraaitech.com/", headers=headers)

# 3. Parse & clean
soup = BeautifulSoup(response.content, 'html.parser')
title = soup.title.string if soup.title else "No title found"
for tag in soup.body(["script", "style", "img", "input"]):
    tag.decompose()
text = soup.body.get_text("
", strip=True)

# 4. Prepare prompt
prompt = (
    f"Please provide a concise summary of the following webpage.\n\n"
    f"Title: {title}\n\n"
    f"Content:\n{text}\n\n"
)
messages = [{"role": "user", "content": prompt}]

# 5. Call OLLAMA API
payload = {"model": "llama3.2", "messages": messages, "stream": False}
resp = requests.post(OLLAMA_API, json=payload, headers=HEADERS)

# 6. Display result
display(Markdown(resp.json()["message"]["content"]))
```

---

## Contributing

1. Fork this repository.  
2. Create a feature branch: `git checkout -b feature/YourFeature`.  
3. Commit your changes.  
4. Open a Pull Request.

Please ensure code style consistency (PEP 8) and update `requirements.txt` as needed.

---

## License

This project is licensed under the **MIT License**.
