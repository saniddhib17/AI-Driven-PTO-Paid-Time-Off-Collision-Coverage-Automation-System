# 🤖 AI PTO Coverage Engine – Smart HR Operations Automation (n8n + Gemini + Google Sheets + Gmail)

> This project automates the end-to-end process of detecting PTO overlaps, deterministically assigning coverage, generating AI-powered explanations, and communicating the final decision to both HR and the absent employee. The goal is to ensure business continuity, reduce administrative overhead for HR, and provide clear communication to employees.

## 🚀 Project Overview

The AI PTO Coverage Engine is an autonomous **n8n pipeline** triggered by updates in a **Google Sheet** (the HR digest).

The pipeline executes the following steps automatically:

* **Detects PTO Collisions** by grouping absences by date and team.
* **Selects Candidates** based on availability, low workload, and team alignment.
* **Calculates a Deterministic Score** for candidates, prioritizing skill match, past coverage experience, and low criticality.
* **Generates AI-powered Explanations** using **Google Gemini** for the coverage assignment decision.
* **Logs the Final Decision** (including HR manual overrides) to a Google Sheet dashboard.
* **Sends Email Alerts** to the absent employee and HR with the final coverage assignment and explanation.

---

## ⚙️ Key Features

### 1. Automated Data Ingestion & Collision Detection
| Source | Function | Key Actions |
| :--- | :--- | :--- |
| **Google Sheets Trigger** | Initiates workflow upon PTO submission. | Reads all employee data (PTO dates, skills, workload). |
| **Logic Nodes** | Collision Detection | Groups PTO entries by `date` and `team` to identify overlaps. |

### 2. Deterministic Coverage Scoring
A custom scoring logic prioritizes the best candidate to minimize disruption:

* **Skillset Match:** Heavily weighted (100 points per overlapping skill).
* **Past Experience:** Weighted score based on `past_coverage_score`.
* **Workload:** Favors candidates with workload `< 0.9`.
* **Criticality:** Prefers candidates with **Low** criticality.
* **HR Override:** Allows HR to manually override the deterministic assignment.

### 3. 🤖 AI-Generated Explanation
**Google Gemini** generates a professional, factual 2–3 sentence explanation for the coverage decision, summarizing the *why* based on the scoring factors.

> **Prompt Context:** Includes the absent employee, team, PTO date, deterministic recommendation, HR override (if applicable), and key reason factors (skills, workload).

### 4. 📧 Notifications & Logging
The workflow ensures transparent communication and logging:

* **Employee Email:** Notifies the absent employee (`target`) of the assigned coverage (`final_cover`).
* **HR Email:** Provides HR with a full summary, including the AI explanation, deterministic recommendation, and any manual override for review.
* **Dashboard Logging:** Appends the final decision, explanation, and risk level (`Low` or `High` based on candidate pool size) to a Google Sheet dashboard.

---

## 🧩 Workflow Architecture

The entire process is orchestrated as a sequential n8n workflow, starting from the Sheet Trigger and ending with notifications and logging.


The core nodes involved are:

1.  **Google Sheets Trigger/Read:** Data source for employee details and PTO status.
2.  **Code/Function Nodes:** Manages grouping, candidate filtering, and deterministic scoring.
3.  **Message a model (Google Gemini):** Generates the coverage explanation.
4.  **Google Sheets Append/Update:** Logs the final assignment to the HR Dashboard.
5.  **Gmail Nodes:** Sends separate, tailored emails to HR and the absent employee.

---

## 📐 Deterministic Scoring Logic

The scoring function is weighted to prioritize job-critical factors like skill overlap and past experience.

The score computation inside the `Deterministic Scoring` code node uses the following weighted factors:

| Factor | Weight/Value | Priority |
| :--- | :--- | :--- |
| **Skillset Match** | 100 per matching skill | Highest (Ensures functional coverage) |
| **Past Coverage Score** | 50 per point | High (Favors experienced candidates) |
| **Workload** | $(1 - \text{Workload}) \times 40$ | Medium (Favors candidates with capacity) |
| **Criticality** | Low (40), Medium (20), High (0) | Medium (Avoids pulling critical resources) |

The candidate with the highest total score is selected as the deterministic cover.

---

## 🛠️ Tech Stack

| Technology | Role |
| :--- | :--- |
| **n8n** | Core workflow automation and orchestration engine. |
| **Google Sheets** | Data source (HR Digest) and Data sink (Dashboard). |
| **Google Gemini (LLM)** | Natural-language generation of coverage explanations. |
| **Gmail** | Sending automated, professional email notifications. |
| **JavaScript (n8n Code Nodes)** | Business logic, filtering, grouping, and scoring. |

---

## 🏁 Results

This automated PTO coverage workflow:

* **Ensures Business Continuity** by rapidly assigning qualified coverage.
* **Reduces HR Administrative Time** spent manually identifying candidates and sending notifications.
* **Increases Transparency** through clear, AI-generated explanations for coverage decisions.
* **Provides Data-Driven Auditability** via the Google Sheets Dashboard log.
