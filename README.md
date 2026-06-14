import json
from collections import Counter
import re

def load_news():
    with open("data/raw_news.json", "r", encoding="utf-8") as f:
        return json.load(f)

def clean_text(text):
    return re.findall(r'\b[a-zA-Z]{4,}\b', text.lower())

def simple_sentiment(text):
    positive_words = ["rise", "growth", "gain", "positive", "boost", "up"]
    negative_words = ["fall", "drop", "crisis", "loss", "decline", "war"]

    text = text.lower()

    score = 0
    for w in positive_words:
        if w in text:
            score += 1
    for w in negative_words:
        if w in text:
            score -= 1

    if score > 0:
        return "positive"
    elif score < 0:
        return "negative"
    return "neutral"

def analyze(news):
    all_words = []
    sentiment_result = {"positive": 0, "neutral": 0, "negative": 0}

    for item in news:
        title = item.get("title", "")
        words = clean_text(title)
        all_words.extend(words)

        sentiment = simple_sentiment(title)
        sentiment_result[sentiment] += 1

    top_keywords = Counter(all_words).most_common(10)

    return {
        "top_keywords": [w for w, _ in top_keywords],
        "sentiment": sentiment_result,
        "total_articles": len(news)
    }

def save(result):
    with open("output/trend.json", "w", encoding="utf-8") as f:
        json.dump(result, f, indent=2, ensure_ascii=False)

def main():
    news = load_news()

    if not news:
        print("No news data found")
        return

    result = analyze(news)
    save(result)

    print("Trend analysis completed")
    print(result)

if __name__ == "__main__":
    main()
