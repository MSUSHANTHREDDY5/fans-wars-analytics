# Celebrity Fanbase Analyzer (OpenAI + n8n)

Automate sentiment and activity analysis for celebrity fanbases using n8n and OpenAI.  
This workflow collects social media posts, aggregates them, performs AI-driven analysis, and outputs engagement insights and summaries to Google Sheets or email/Slack.

- Two execution paths:
  - **Manual/API-triggered** for ad-hoc analysis.
  - **Google Sheets-triggered** for scheduled or bulk runs.
- Sources: Social media post APIs or JSON datasets.
- Outputs: AI summary + Google Sheet entries + optional Gmail/Slack notifications.
- Models: OpenAI GPT-4 or Azure OpenAI (via n8n OpenAI node).

> Workflow JSON: [Automation UseCase/Celebrity Fanbase Analyzer.json](link-to-your-workflow-json)

---

## Visuals

*(Add your screenshots and demo links here)*  
Example placeholders:

- Workflow Snapshot  
- OpenAI Output Example (3-line summary)  
- Google Sheets Output Example  

---

## High-Level Flow

Fetch Fanbase Posts → Clean & Aggregate → AI Sentiment + Activity Analysis → Output Report

Two branches:

- **Manual / API Trigger** → HTTP Request (Posts) → Extract Text → OpenAI Analysis  
  ↓  
  Generate 3-Line Summary  
  ↓  
  Store in Google Sheets  
  ↓  
  Send via Gmail / Slack

- **Google Sheets Trigger** → New Row (Celebrity Name) → Fetch Posts (API) → AI Summary  
  ↓  
  Update Sheet with Sentiment, Activity, and Summary Columns

---

## Architecture

- n8n workflow with:
  - Manual Trigger / Webhook (user-initiated run)
  - HTTP Request Node (fetch posts data)
  - Function / Code Node (JavaScript) for text extraction and preprocessing
  - OpenAI Node (sentiment and engagement analysis)
  - Google Sheets Node (append/update results)
  - Gmail / Slack Node (optional summary notifications)
  - Webhook / API Response Node (return structured AI insights)

ASCII overview:

```
[Manual Trigger] ─► [HTTP Posts Fetch] ─► [Function: Clean Text] ─► [OpenAI Sentiment + Activity Analysis] ─► [Google Sheets Append] ─► [Gmail / Slack Send]
[Google Sheets Trigger] ─► [Fetch New Entries] ─► [HTTP Posts Fetch] ─► [Function Parse] ─► [OpenAI Summary] ─► [Google Sheets Update]
```

---

## Nodes and Responsibilities

### Branch 1 — Manual / API-Triggered Analysis

1) **Trigger Node**
- Starts the workflow on demand via button or webhook.
- Inputs: celebrity name or identifier.

2) **HTTP Request Node**
- Fetches recent social media posts or fan interactions.
- Example: GET `/api/posts?celebrity={{$json.name}}`

3) **Function Node (Extract Texts)**
- Extracts relevant text content from JSON:
  ```js
  return items.map(item => ({
    json: {
      postsText: item.json.posts.map(p => p.body).join("\n\n")
    }
  }));
  ```

4) **OpenAI Node**
- Prompt template:
  > “You are an AI social media analyst. Analyze these posts and summarize fan activity and positivity in 3 lines.”

5) **Set Node**
- Formats output with fields:
  - Celebrity Name
  - Summary
  - Sentiment Score
  - Activity Rating

6) **Google Sheets Node (Append)**
- Appends summarized analysis results to tracking sheet.

7) **Gmail / Slack Node**
- Sends notification or result snippet to stakeholders.

---

### Branch 2 — Google Sheets-Triggered Analysis

1) **Google Sheets Trigger**
- Watches for new or edited rows (e.g., new celebrity name).

2) **HTTP Request Node**
- Fetches recent fanbase posts based on the given name.

3) **Function Node**
- Prepares text corpus for AI analysis.

4) **OpenAI Node**
- Generates a structured summary:
  - Fan activity trend  
  - Sentiment polarity (positive/neutral/negative)  
  - Overall engagement level

5) **Google Sheets Update Node**
- Writes back AI results to the same row:
  - Sentiment Score  
  - Positivity Summary  
  - Engagement Insights

6) **Optional Loop / Batch Handling**
- Iterates across multiple new entries.

---

## AI Model Configuration

- Default: **OpenAI GPT-4** (via n8n OpenAI node).  
- Optional: **Azure OpenAI** if credentials configured.  
- Context includes aggregated post text and metadata.  
- Output structured as concise 3-line summary for easy reading.

---

## Example AI Output

```markdown
# Celebrity Fanbase Analysis — Taylor Swift

- The fanbase is highly active with consistent engagement across posts.  
- Sentiment leans strongly positive, emphasizing admiration and loyalty.  
- Overall, the community exhibits energetic participation and upbeat tone.
```

---

## Setup

### Prerequisites
- n8n (Cloud or self-hosted)
- Access to:
  - Social media API or static dataset endpoint
  - Google Sheets API (OAuth credentials)
  - OpenAI / Azure OpenAI API key
  - Gmail or Slack credentials (optional notification)
- Google Sheet with columns:
  - Celebrity Name
  - Fan Activity Summary
  - Sentiment Score
  - Positivity Level
  - Last Analyzed Timestamp

### Import the Workflow
- Download: `Celebrity Fanbase Analyzer.json`
- In n8n → *Import Workflow from File*  
- Configure credentials for:
  - HTTP Request Node
  - Google Sheets Node
  - OpenAI Node
  - Gmail / Slack (if used)

### Configure IDs and Targets
- **Google Sheets:**  
  - Target Sheet ID & tab name for appending/updating results.
- **OpenAI:**  
  - API key via credentials.
  - Default model: `gpt-4-turbo` or `gpt-4o-mini`.
- **Webhook (if any):**  
  - URL endpoint for external integrations.

---

## Key Code Patterns

Extracting fan posts:
```js
return items.map(item => ({
  json: {
    postsText: item.json.posts.map(p => p.body || p.text || '').join('\n\n')
  }
}));
```

Formatting AI output:
```js
const summary = $json["message"]["content"];
return [{ json: { celebrity: $json.name, summary, analyzedAt: new Date().toISOString() } }];
```

---

## Running

- **Manual / API Mode:**
  1) Trigger the workflow manually or via webhook.
  2) Provide celebrity name or fanbase keyword.
  3) Workflow fetches posts → analyzes → stores → notifies.

- **Google Sheets Mode:**
  1) Add a new celebrity name in the configured sheet.
  2) Trigger runs automatically.
  3) AI results are appended or updated in corresponding cells.

---

## Data Model (Sheets)

| Celebrity | Activity Level | Sentiment | Summary | Timestamp |
|------------|----------------|------------|----------|------------|
| Taylor Swift | High | Positive | Loyal and energetic fanbase with upbeat tone. | 2025-10-28 |

---

## Error Handling

- HTTP fetch failure → log + optional email alert.  
- OpenAI timeout → retry with fallback model.  
- Invalid JSON or empty posts → skip gracefully.  
- Google Sheets write error → queue retry or backup log.

---

## Performance and Reliability

- Batch processing controlled by “Loop” and “Split Out” nodes.  
- Rate limiting recommended for API calls.  
- Execution logs enabled in n8n for traceability.  
- Optional Slack notifications for failures.

---

## Security and Compliance

- Store credentials only via n8n Credential Manager.  
- Exclude API keys from committed files.  
- Comply with platform TOS for data access.  
- No storage of PII or raw user data beyond analytics.

---

## Extensibility

- Add new sources (Reddit, Twitter, Instagram APIs).  
- Expand analytics (topic clustering, emotion detection).  
- Integrate dashboard (e.g., Data Studio or Notion).  
- Support batch exports to CSV or Google Drive.

---

## References

- Workflow JSON: `Celebrity Fanbase Analyzer.json`  
- Example Output Screenshots  
- Demo video: *(insert link when ready)*  
- Tools used:
  - n8n
  - OpenAI GPT-4
  - Google Sheets API
  - Gmail / Slack integration
