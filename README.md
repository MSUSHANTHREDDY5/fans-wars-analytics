# Fanbase Analyzer Agent (n8n)

Automated fanbase comparison and sentiment analysis for two celebrities using n8n and OpenAI.  
This workflow accepts a user input (webhook), extracts two subreddit/keyword names, fetches recent Reddit posts for both fan communities, cleans and combines the data, runs an LLM analysis to compare positivity & engagement, generates a chart, and emails the final report.

- **Execution Path:** Webhook → Reddit → Clean → Combine → Validate → OpenAI → Chart → Email
- **Sources:** Reddit posts (two subreddits or keywords provided by user)
- **Outputs:** Email report (HTML) with AI analysis and chart image
- **Model:** OpenAI (configured via n8n LangChain/OpenAI node)


Workflow JSON: [Fanbase Analyzer Agent Workflow.json](https://github.com/MSUSHANTHREDDY5/fans-wars-analytics/blob/main/Fanbase%20Analyzer%20Agent.json)

---

## 📸 Visuals

![Workflow Snapshot](Worflow%20-n8n-Snapshot.png)

Terminal Input:
![Terminal Input](https://github.com/MSUSHANTHREDDY5/fans-wars-analytics/blob/main/WorkFLow-Terminal(curl)-Input.png)

Gmail Output:
![Gmail Output](https://github.com/MSUSHANTHREDDY5/fans-wars-analytics/blob/main/WorkFlow-Gmail-Output.png)


Data vizualization chart:
![Data vizualization chart](https://github.com/MSUSHANTHREDDY5/fans-wars-analytics/blob/main/visualization-chart.png)

Demo Video:
WorkFlow run: [WorFlow Execution.mp4](https://github.com/MSUSHANTHREDDY5/fans-wars-analytics/blob/main/WorkFlow-Execution(3).mp4)

---

## ⚙️ High-Level Flow

1. Receive user input via webhook containing two celebrity names or subreddit keywords.  
2. Extract subreddit-friendly names using an OpenAI extraction node.  
3. Fetch recent Reddit posts for both fanbases.  
4. Clean post data (titles, texts, and subreddit labels).  
5. Combine and prepare text for analysis.  
6. Validate data through guardrails (length + NSFW filtering).  
7. If valid, analyze combined posts with OpenAI for engagement & positivity comparison.  
8. Generate a chart comparing results using QuickChart API.  
9. Send the complete report (AI summary + chart) via Gmail.  

---

## 🧠 Workflow Architecture

- n8n workflow with:
  - **Webhook Trigger** – Receives user input (`input`, `email`).  
  - **OpenAI Node** – Extracts two subreddit-friendly names.  
  - **Code Node (Split Input)** – Splits OpenAI output into subreddit1 & subreddit2.  
  - **Reddit Nodes (x2)** – Fetch latest posts for each celebrity fanbase.  
  - **Set Nodes (x2)** – Normalize and clean Reddit data.  
  - **Merge Node** – Combines both fanbase datasets.  
  - **Code Node (Prepare Combined Text)** – Builds structured analysis text.  
  - **Code Node (Validate Posts)** – Checks post length and filters NSFW content.  
  - **If Node (Is Data Valid?)** – Routes valid/invalid paths.  
  - **OpenAI Node (Analyze Fanbase Sentiment)** – Compares fanbases by engagement & tone.  
  - **Code Node (Generate Chart Data)** – Parses AI response for chart metrics.  
  - **HTTP Request Node (QuickChart)** – Generates bar chart comparing positivity & engagement.  
  - **Merge Node (Final Analysed Message)** – Combines text & chart link.  
  - **Gmail Node (Send Fanbase Report)** – Sends final email summary.  

---

##  Node Breakdown

### 1. Receive User Input (Webhook)
- Method: `POST`
- Path: `/fan-compare`
- Body example:
```json
{
  "input": "Compare Taylor Swift and Selena Gomez fanbases",
  "email": "user@example.com"
}
```

### 2. Extract Celebrity Names (OpenAI)
- Uses LLM to extract exactly two subreddits or fanbase names.
- Output format: `{"subreddit1": "...", "subreddit2": "..."}`

### 3. Fetch Reddit Posts (Celebrity 1 & 2)
- Operation: `getAll`
- Parameters: subreddit name, limit (default 2, recommend 20–100 for real use).

### 4. Clean & Combine Data
- Normalizes Reddit post fields: title, text, subreddit.
- Combines into unified dataset for AI analysis.

### 5. Validate Posts (Guardrail)
- Checks for text sufficiency and filters NSFW keywords.
- Invalid data halts workflow gracefully.

### 6. Analyze Fanbase Sentiment (OpenAI)
- Generates structured analysis:
  - Positivity %
  - Engagement %
  - Winner (bolded name)
  - 3-line summary
  - 5-point professional breakdown

### 7. Generate Chart Data
- Parses sentiment metrics via regex or JSON pattern.
- Output structure:
```json
{
  "celeb1": "Vinicus Jr",
  "pos1": 85,
  "eng1": 92,
  "celeb2": "Lamine Yamal",
  "pos2": 78,
  "eng2": 88
}
```

### 8. AI Chart Generator (QuickChart API)
- Builds bar chart visualizing Positivity and Engagement % comparison.
- Returns chart image URL.

### 9. Send Fanbase Report (Gmail)
- Sends formatted HTML email with AI text and embedded chart.

---

##  Example Output

**Subject:** “Fanbase Comparison Report — Vinicus Jr vs Lamine Yamal”  

**Email Body (HTML)**:

Gmail Output:
![Gmail Output](https://github.com/MSUSHANTHREDDY5/fans-wars-analytics/blob/main/WorkFlow-Gmail-Output.png)

---

##  Setup & Configuration

### Prerequisites
- n8n (Cloud or Self-hosted)
- Configured credentials for:
  - **Reddit OAuth2**
  - **OpenAI API key**
  - **Gmail OAuth2**
- QuickChart API (no authentication required for public endpoints)

### Import the Workflow
1. Open n8n → *Import Workflow* → Upload `Fanbase Analyzer Agent.json`.
2. Configure credentials:
   - Reddit: Both `Fetch Reddit Posts` nodes
   - OpenAI: `Extract Celebrity Names` & `Analyze Fanbase Sentiment`
   - Gmail: `Send Fanbase Report`
3. Activate webhook or use *Execute Workflow* to test.

---

##  Data Validation Example (Guardrail)

```js
const combinedText = $json.combinedText || '';
if (combinedText.length < 200) {
  return [{ json: { valid: false, reason: "Not enough Reddit data." } }];
}
const banned = ["nsfw","violence","hate","racist"];
if (banned.some(word => combinedText.toLowerCase().includes(word))) {
  return [{ json: { valid: false, reason: "Content flagged as unsafe." } }];
}
return [{ json: { valid: true, combinedText } }];
```

---

##  Chart Data Generation Example

```js
const text = $json.message?.content || '';
const regex = /Celebrity 1: (.*?) — Positivity: (\d+)% — Engagement: (\d+)%[\s\S]*?Celebrity 2: (.*?) — Positivity: (\d+)% — Engagement: (\d+)%/;
const m = text.match(regex);
return [{
  json: {
    celeb1: m[1], pos1: parseInt(m[2]), eng1: parseInt(m[3]),
    celeb2: m[4], pos2: parseInt(m[5]), eng2: parseInt(m[6])
  }
}];
```

---

##  Running the Workflow

1. **POST** to the webhook endpoint `/webhook/fan-compare`.
2. Workflow fetches Reddit posts for both celebrities.  
3. Validates and analyzes fanbase data.  
4. Generates comparison chart.  
5. Sends the analysis report via Gmail to provided email address.

---

##  Error Handling

- If Reddit fetch fails → skip analysis and log error.  
- If Guardrail fails → send polite “Insufficient data” email.  
- If QuickChart fails → fallback to text-only report.  
- If OpenAI fails → retry once with backup model (`gpt-4-turbo-mini`).

---

##  Security & Compliance

- Never expose API keys publicly.  
- Use n8n credentials manager for all integrations.  
- Follow Reddit API rate limits & ToS.  
- Avoid storing user emails beyond reporting purpose.

---

##  Extensibility

- Add Twitter or Instagram APIs for multi-platform analysis.  
- Integrate Slack or Discord for instant summary notifications.  
- Persist analysis results to database (Postgres, Firestore, etc.).  
- Add emotion or topic breakdown using OpenAI structured prompts.

---

##  References

Workflow JSON: [Fanbase Analyzer Agent Workflow.json](https://github.com/MSUSHANTHREDDY5/fans-wars-analytics/blob/main/Fanbase%20Analyzer%20Agent.json)

Workflow Snapshot:[Workflow-image.png](https://github.com/MSUSHANTHREDDY5/fans-wars-analytics/blob/main/Worflow%20-n8n-Snapshot.png)

Terminal Input:[Termninal-image.png](https://github.com/MSUSHANTHREDDY5/fans-wars-analytics/blob/main/WorkFLow-Terminal(curl)-Input.png)

Gmail Output:[Gmail-image.png](https://github.com/MSUSHANTHREDDY5/fans-wars-analytics/blob/main/WorkFlow-Gmail-Output.png)

Data vizualization chart:[Visualization-image.png](https://github.com/MSUSHANTHREDDY5/fans-wars-analytics/blob/main/visualization-chart.png)

WorkFlow run: [WorFlow Execution.mp4](https://github.com/MSUSHANTHREDDY5/fans-wars-analytics/blob/main/WorkFlow-Execution(3).mp4)

---
