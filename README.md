import json
from datetime import datetime

TREND_FILE = "output/trend.json"
REPORT_FILE = "output/report.md"
DASHBOARD_FILE = "output/dashboard.json"

def load_trend():
    with open(TREND_FILE, "r", encoding="utf-8") as f:
        return json.load(f)

def generate_markdown(trend):
    top_keywords = trend.get("top_keywords", [])
    topics = trend.get("topics", [])

    md = f"""# 🧠 Global Intelligence Daily Report

Date: {datetime.now().strftime("%Y-%m-%d")}

---

## 🔥 Top Keywords

"""

    for word, count in top_keywords[:10]:
        md += f"- **{word}**: {count}\n"

    md += "\n---\n\n## 📊 Topic Trends\n\n"

    for t in topics:
        md += f"- {t['name']}: {t['count']}\n"

    md += "\n---\n\n## 🧠 AI Summary\n\n"
    md += "Global news shows concentration in tech, economy and geopolitics trends.\n"

    return md

def generate_dashboard(trend):
    return {
        "keywords": trend.get("top_keywords", []),
        "topics": trend.get("topics", []),
        "updated_at": datetime.now().isoformat()
    }

def main():
    trend = load_trend()

    # report
    md = generate_markdown(trend)
    with open(REPORT_FILE, "w", encoding="utf-8") as f:
        f.write(md)

    # dashboard json
    dashboard = generate_dashboard(trend)
    with open(DASHBOARD_FILE, "w", encoding="utf-8") as f:
        json.dump(dashboard, f, indent=2)

    print("report generated")

if __name__ == "__main__":
    main()
