Hi chellam, help me in creating a script using Android + termux + python + pip.

Script goal and instructions: download all .html extension (link that contains html) from the link inside <a> tag for example my page contains link like <a href="/pm_etexts/utf8/pmuni0002.html">pmuni0002.html</a>
and domain url https://www.projectmadurai.org
Links must be grabbed from: https://www.projectmadurai.org/pmworks.html

Sure chellam — here is a clean, working Python script you can run inside **Termux** using **Python + pip** to:

✔ Fetch the page: **[https://www.projectmadurai.org/pmworks.html](https://www.projectmadurai.org/pmworks.html)**
✔ Extract all `<a href="...html">` links
✔ Convert relative URLs to full URLs
✔ Download every `.html` file into a folder

Works on Android + Termux.

---

# ✅ Step 1 — Install dependencies in Termux

```bash
pkg update
pkg install python
pip install requests beautifulsoup4
```

---

# ✅ Step 2 — Create the Python script

Create a file:

```bash
nano download_htmls.py
```

Paste the script below:

```python
import requests
from bs4 import BeautifulSoup
import os
from urllib.parse import urljoin

BASE_URL = "https://www.projectmadurai.org"
START_URL = "https://www.projectmadurai.org/pmworks.html"

def fetch_links():
    print("[+] Fetching main page...")
    response = requests.get(START_URL)
    response.raise_for_status()

    soup = BeautifulSoup(response.text, "html.parser")
    links = []

    # Find all a-tags
    for a in soup.find_all("a", href=True):
        href = a["href"]

        # Only HTML files
        if href.endswith(".html"):
            full_url = urljoin(BASE_URL, href)
            links.append(full_url)

    return links

def download_files(links):
    folder = "downloaded_html"
    os.makedirs(folder, exist_ok=True)

    print(f"[+] Downloading {len(links)} HTML files...\n")

    for url in links:
        filename = url.split("/")[-1]
        filepath = os.path.join(folder, filename)

        print(f"Downloading: {filename}")
        try:
            r = requests.get(url)
            r.raise_for_status()

            with open(filepath, "w", encoding="utf-8") as f:
                f.write(r.text)
        except Exception as e:
            print(f"Failed: {url} -> {e}")

    print("\n[+] Done!")

if __name__ == "__main__":
    html_links = fetch_links()
    download_files(html_links)
```

Save: **CTRL + O → Enter → CTRL + X**

---

# ✅ Step 3 — Run the script

```bash
python download_htmls.py
```

Downloaded HTML files will be saved inside:

```
/data/data/com.termux/files/home/downloaded_html/
```

---

# 💡 Want extra features?

I can add any of these if you want:

* Download ONLY particular types (utf8/pm_etexts/etc)
* Convert downloaded HTML to text or PDF
* Add retry/resume support
* Multithreaded faster downloader
* Create EPUB from all downloaded HTML files

Just tell me, chellam ❤️
