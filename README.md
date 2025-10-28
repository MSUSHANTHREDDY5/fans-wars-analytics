# FanBase Analyzer (Reddit-Gmail)

> **Autonomously compares fanbases of two celebrities using Reddit data and AI-driven sentiment analysis — powered by n8n, OpenAI, and automated email reporting.**

---

## Overview

The **Fan Wars Analyzer** is an *Agentic AI* workflow that dynamically analyzes and compares the fanbase activity between any two celebrities based on real Reddit discussions.  
Users simply send an input like `"Messi vs Ronaldo"` along with their email — the system automatically fetches posts, analyzes engagement and sentiment, considers recent real-world context (like a match, award, or controversy), and sends a structured comparison report to the user’s inbox.

---

## Highlights

- **Fully Agentic Workflow:** Makes decisions based on data validity, automatically handles safe/unsafe inputs, and self-directs output paths.  
- **Dynamic Input Handling:** Accepts any user-provided celebrity pair and email through a single Webhook call.  
- **Context-Aware AI Reasoning:** Incorporates *real-world incidents* from each celebrity’s domain (e.g., sports, film, awards).  
- **Validation Guardrails:** Detects insufficient or unsafe Reddit content before AI processing.  
- **Automated Email Reporting:** Sends personalized results with key metrics and analysis.  
- **Built Entirely in n8n** with Reddit, OpenAI, and Gmail integrations.

---

## High-Level Flow

**Webhook Input → Validate & Parse → Fetch Reddit Data → Clean & Merge → Analyze via LLM → Send Email**

Two main paths:
1. ✅ **Valid Data Path** → Full AI Analysis → Email Report  
2. ❌ **Invalid Data Path** → Skip AI → Send Polite Validation Message



