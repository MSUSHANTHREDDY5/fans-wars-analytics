# fans-wars-analytics
AGENTIC AI PROJECT(TWITTER_X AS RESOURCE AND GOOGLE COLAB,N8N FRAMEWWORK AS TOOLS)

# 📊 Fans Wars – Agentic Automation Project

## 🧭 Overview
**Fans Wars** is an Agentic AI project that automates the process of analyzing fan interactions on **Twitter (X)**.  
It compares activity between two or more fan communities (for example, *#TeamSRK* vs *#TeamSalman*) and automatically sends analytical reports to Gmail.

The entire process is automated using **n8n** workflows and **Google Colab**, without any manual intervention.

---

## 🎯 Objective
To design an **Agentic system** that:
- Collects real-time data from **Twitter/X**
- Analyzes fan engagement, sentiment, and activity trends
- Sends automated reports to **Gmail** using AI-generated summaries

---

## ⚙️ Tools & Technologies

| Type | Tool/Platform | Purpose |
|------|----------------|----------|
| **Resource** | Twitter (X) | Source of fan data |
| **Tool 1** | Google Colab | Data scraping, cleaning, and analysis |
| **Tool 2** | n8n | Automation workflow and agentic intelligence |
| **Tool 3** | Gmail | Delivery of daily analytical reports |
| **Tool 4** | Google Sheets | *(Optional)* Store tweet data and metrics |

---

## 🔁 Workflow
1. **n8n** triggers the process automatically (using a Cron node).  
2. **Google Colab** scrapes Twitter data using `snscrape` and performs sentiment & engagement analysis.  
3. The analyzed data is returned to **n8n**, which uses an **AI Node** to generate human-readable insights.  
4. Finally, **n8n** sends the report to Gmail with summary and charts.

---

## 💡 Example Output (Email)
**Subject:** Fans Wars – Daily Report  


---

## 🕒 Automation Cycle
- Runs every 12 hours (configurable)
- Automatically updates results and sends new reports

---

## 💰 Cost
This is a **zero-investment project**, using only free-tier tools (twitter tweets(X),n8n Cloud, Google Colab, and Gmail).

---

## 👨‍💻 Author
**M Sushanth Reddy**  
B.Tech 1st Year, VNR VJIET  
📧 Email: [sushanthreddy2007@gmail.com](mailto:sushanthreddy2007@gmail.com)  
🌐 GitHub: [MSUSHANTHREDDY5](https://github.com/MSUSHANTHREDDY5)


