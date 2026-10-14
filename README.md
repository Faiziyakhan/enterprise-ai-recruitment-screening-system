# Enterprise AI Recruitment & Candidate Screening System

An autonomous, multi-stage ATS (Applicant Tracking System) recruitment pipeline built with **n8n**, **Google Gemini Vision**, **LangChain** structured output parsing, **Google Sheets** CRM, and **Gmail** automation.

The system ingests candidate applications with resume attachments via Gmail polling, extracts structured credentials using multimodal vision AI, executes automated candidate scoring against hiring rubrics, performs CRM deduplication (Update vs Append), dispatches candidate outcome emails (Shortlist / Hold / Reject), and notifies HR with an executive candidate summary.

---

## Overview

Traditional resume screening is manual, slow, and prone to inconsistent evaluation standards. Handling high-volume job applications often causes candidate ghosting, delayed interview cycles, and fragmented applicant tracking.

This enterprise pipeline automates the full candidate lifecycle:

1. **Multimodal Ingestion:** Ingests emails with resume attachments (PDF, DOCX, PNG, JPG) and routes them through a media switch.
2. **Vision-Powered ATS Parsing:** Employs Gemini Multimodal Vision to extract authentic candidate credentials while filtering out placeholders and styling artifacts.
3. **Structured Evaluation & Scoring:** Uses LangChain with Google Gemini and a strict JSON schema parser to evaluate candidates across 5 core dimensions.
4. **CRM Sync & Intelligent Deduplication:** Checks Google Sheets for existing candidate records using email lookups to prevent duplicate rows (updates existing records or appends new applicants).
5. **Dynamic Candidate Routing & HR Escalation:** Dispatches tailored HTML status emails to applicants based on decision thresholds and alerts hiring managers immediately with full scorecards.

---

## Workflow Architecture

```text
Incoming Application Email (Gmail Trigger - Label Filtered)
                 ↓
      Get Message & Download Attachments
                 ↓
      MIME Type Router (Switch: PDF | DOCX | PNG | JPG)
                 ↓
  [Gemini Vision Model: Analyze an Image]
                 ↓
      JavaScript Sanitizer & JSON Normalizer
                 ↓
        [AI Recruitment Specialist Agent]
     (Gemini Chat Model + Structured Output Parser)
                 ↓
   Evaluation Schema: Score, Dimension Breakdown, Recommendation
                 ↓
     Google Sheets CRM: Check Existing Candidate (Email Lookup)
            ┌────┴────┐
    [Exists: True]   [Exists: False]
            │                 │
     Update Row        Append Row
            └────┬────┘
                 ↓
     Recommendation Router (IF Conditions)
     ┌───────────┼───────────┐
[Shortlist]    [Hold]     [Reject]
     │           │           │
Shortlist Email Hold Email Reject Email
     └───────────┬───────────┘
                 ↓
  Send HR Executive Summary Notification
```

---

## Key Capabilities & Technical Highlights

- **Multimodal Document Processing:** Directly parses resume images and formatted documents using Google Gemini Vision, accurately extracting text, technical proficiencies, tenures, and institutional backgrounds.
- **CRM Deduplication Engine:** Before inserting candidate data, the workflow queries the Google Sheets CRM (`AI Lead CRM`) on the Email key. If a record already exists, it updates the row; otherwise, it appends a new entry.
- **Automated Candidate Status Communication:** Formats and dispatches responsive HTML emails tailored to three candidate outcomes:
  - **Shortlisted:** Congratulatory notice outlining interview next steps.
  - **Hold:** Status update requesting clarification or noting extended review.
  - **Reject:** Polite, encouraging feedback closing the application loop.
- **Internal HR Dispatch:** Automatically sends an executive notification email to the recruiting team containing extracted metrics, scorecards, and AI assessment reasoning for instant action.

### 5-Dimensional Candidate Scoring Rubric

| Dimension | What It Measures |
|-----------|------------------|
| **Candidate Score** | Aggregate index out of 100 |
| **Experience Score** | Tenure depth and industry relevance |
| **Skills Score** | Alignment with required technology stack |
| **Education Score** | Degree relevance and pedigree |
| **Communication Score** | Clarity and presentation structure |

---

## Structured Data Contracts

### 1. ATS Extraction Schema (Gemini Vision)

```json
{
  "full_name": "DAVID WILSON",
  "email": "david.wilson@example.com",
  "phone": "Not specified",
  "location": "Not specified",
  "current_role": "Lead Machine Learning Engineer",
  "total_experience_years": 12,
  "skills": ["Python", "PyTorch", "TensorFlow", "LangChain", "RAG", "AWS", "Azure", "GCP", "Docker", "Kubernetes", "SQL", "CI/CD"],
  "education": "M.Sc. Artificial Intelligence",
  "certifications": [],
  "projects": [],
  "companies_worked": [],
  "key_achievements": "Built enterprise AI search platform for 3M+ users; reduced inference cost by 35%",
  "candidate_summary": "Lead Machine Learning Engineer with 12+ years of experience in AI, LLMs, RAG, and MLOps."
}
```

### 2. Candidate Evaluation Schema (Structured Output Parser)

```json
{
  "candidate_score": 95,
  "experience_score": 98,
  "skills_score": 96,
  "education_score": 92,
  "communication_score": 90,
  "recommendation": "Shortlist",
  "reason": "David Wilson is an exceptional Lead Machine Learning Engineer with 12 years of experience and a specialized M.Sc. in Artificial Intelligence. Demonstrates rare deep technical expertise in LLMs, RAG, and cloud infrastructure with proven impact scaling platforms to 3M+ users."
}
```

---

## Technology Stack

- **Orchestration:** n8n (scheduled polling, branching logic, JavaScript transformers)
- **Multimodal AI:** Google Gemini Vision (`models/gemini-3-flash-preview` / `@n8n/n8n-nodes-langchain.googleGemini`)
- **Evaluation Agent:** LangChain Agent with Google Gemini Chat Model (`@n8n/n8n-nodes-langchain.agent`)
- **Structured Validation:** LangChain Structured Output Parser (`@n8n/n8n-nodes-langchain.outputParserStructured`)
- **Applicant Tracking CRM:** Google Sheets API (deduplication, row lookups, upsert workflows)
- **Email Dispatch:** Gmail OAuth2 API (label triggers, candidate updates, HR notifications)

---
## Evidence & Verified Visual Proof

### 1. End-to-End Candidate Case Study: David Wilson (Lead ML Engineer)
| Ingested Resume Document | Candidate Shortlist Notification Email |
| :---: | :---: |
| ![Ingested Resume](Screenshot%202026-07-23%20141251.png) | ![Shortlist Notification](Screenshot%202026-09-16%20005405.png) |

| Automated HR Executive Alert Email | Live Applicant Tracking CRM Sync (Google Sheets) |
| :---: | :---: |
| ![HR Notification Email](Screenshot%202026-09-16%20005611.png) | ![Google Sheets CRM](Screenshot%202026-10-12%20180125.png) |

---

### 2. Comprehensive Evaluation Outcomes (Hold & Reject Pathways)
| Under Review / Hold Status Email | Rejection Notice (Polite Follow-up) | Rejection Assessment (Low Rubric Score) |
| :---: | :---: | :---: |
| ![Hold Status Email](Screenshot%202026-09-16%20005927.png) | ![Reject Polite Notice](Screenshot%202026-09-16%20010350.png) | ![Reject Evaluation Email](Screenshot%202026-09-16%20010135.png) |

---

### 3. Workflow Architecture & Execution Canvas
| Full End-to-End Orchestration Topology (with Deduplication & HR Notify) |
| :---: |
| ![Complete n8n Architecture](Screenshot%202026-07-22%20001255.png) |

| Core Scoring & Decision Routing Canvas |
| :---: |
| ![Core Decision Canvas](Screenshot%202026-07-16%20181422.png) |

---

## Data & Demo Notice

All candidate CVs, applicant contact information, email identifiers, and scores displayed in screenshots represent **simulated evaluation records** designed for testing and portfolio demonstration purposes.

---

## Security

Confidential API credentials, Google Service Account keys, and OAuth authorization tokens are kept securely within the n8n environment; only internal system references and non-sensitive workflow structures are included in the template export.

---

## Project Status

**Completed — Portfolio Project**

---

## Author

**Faiziya Khan**

AI Automation Engineer | n8n | AI Agents | RAG | Workflow Automation

📧 Email: khanfaiziya2@gmail.com
