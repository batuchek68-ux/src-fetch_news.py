import json
from collections import Counter

INPUT_FILE = "data/raw_news.json"
OUTPUT_FILE = "output/trend.json"

def load_news():
    with open(INPUT_FILE, "r", encoding="utf-8") as f:
        return json.load(f)

def extract_keywords(title):
    # 非AI版轻量关键词提取（先跑通）
    stopwords = {"the", "a", "an", "of", "in", "on", "and", "to", "for"}
    words = title.lower().split()
    return [w for w in words if w not in stopwords and len(w) > 3]

def analyze(news):
    all_keywords = []

    for item in news:
        title = item.get("title", "")
        all_keywords.extend(extract_keywords(title))

    freq = Counter(all_keywords)

    top_keywords = freq.most_common(20)

    return {
        "total_news": len(news),
        "top_keywords": [
            {"word": k, "count": v} for k, v in top_keywords
        ]
    }

def save(result):
    with open(OUTPUT_FILE, "w", encoding="utf-8") as f:
        json.dump(result, f, indent=2, ensure_ascii=False)

def main():
    news = load_news()

    if not news:
        print("No news data found")
        save({"error": "empty dataset"})
        return

    result = analyze(news)
    save(result)

    print("Analysis done:", len(result["top_keywords"]))

if __name__ == "__main__":
    main()
