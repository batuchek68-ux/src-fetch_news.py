import feedparser
import json
import os
from datetime import datetime

SOURCES = {
    "google_en": "https://news.google.com/rss?hl=en-US&gl=US&ceid=US:en",
    "google_cn": "https://news.google.com/rss?hl=zh-CN&gl=CN&ceid=CN:zh-Hans",
    "hackernews": "https://hnrss.org/frontpage"
}

def fetch_rss(url, source):
    feed = feedparser.parse(url)
    items = []

    for e in feed.entries[:50]:
        items.append({
            "title": e.get("title", ""),
            "link": e.get("link", ""),
            "published": e.get("published", ""),
            "source": source
        })
    return items


def main():
    all_items = []

    for name, url in SOURCES.items():
        all_items.extend(fetch_rss(url, name))

    os.makedirs("data", exist_ok=True)

    with open("data/raw_news.json", "w", encoding="utf-8") as f:
        json.dump({
            "time": datetime.utcnow().isoformat(),
            "count": len(all_items),
            "items": all_items
        }, f, ensure_ascii=False, indent=2)

    print(f"[OK] fetched {len(all_items)} items")


if __name__ == "__main__":
    main()
