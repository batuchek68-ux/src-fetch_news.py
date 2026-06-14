import requests
import json

def fetch_news():
    url = "https://api.gdeltproject.org/api/v2/doc/doc?query=economy&mode=ArtList&format=json"

    try:
        response = requests.get(url, timeout=10)
        data = response.json()
    except Exception as e:
        print("API error:", e)
        return []

    articles = data.get("articles", [])

    news = []

    # ✅ 兼容 dict / list 两种结构
    if isinstance(articles, dict):
        articles = articles.values()

    for item in articles:
        news.append({
            "title": item.get("title", ""),
            "source": item.get("sourceCountry", ""),
            "url": item.get("url", ""),
            "time": item.get("seendate", "")
        })

    with open("data/raw_news.json", "w", encoding="utf-8") as f:
        json.dump(news, f, indent=2, ensure_ascii=False)

    print(f"Fetched {len(news)} news items")

if __name__ == "__main__":
    fetch_news()
