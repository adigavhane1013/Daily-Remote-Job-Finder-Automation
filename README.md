# Daily Remote Job Finder Automation

An automated workflow built using **n8n** that fetches remote job listings from 6 job boards, filters by target job role, removes duplicates, stores new listings in **MySQL**, and sends a formatted HTML email digest directly to your inbox — fully automated, every 10 hours.

---

## 🚀 Features

- **Automatic Job Aggregation**
  Fetches job listings from 6 sources simultaneously:
  - WeWorkRemotely
  - RemoteOK
  - Himalayas
  - Jobspresso
  - Working Nomads
  - Remotive

- **Role-Based Keyword Filtering**
  Filters jobs by target role keywords (e.g. AI Engineer) so only relevant listings move forward. Easily configurable for any role.

- **Smart Two-Level Deduplication**
  - Within-run: removes duplicate jobs appearing across multiple feeds in the same run
  - Cross-run: compares job links against MySQL history to ensure zero repeated notifications

- **MySQL Integration**
  - Stores every new job with a timestamp
  - Unique index on link column blocks duplicates at the database level
  - Serves as permanent historical record across all runs

- **Responsive HTML Email Alerts**
  - Clean formatted job digest
  - Job title, company, location, source badge, and direct Apply Now button per listing
  - Delivered via Gmail API
  - Error fallback alert if Gmail send fails

- **Scheduled Execution**
  Runs every 10 hours automatically using n8n's built-in Schedule Trigger.

---

## 🛠️ Tech Stack

- **n8n** — Workflow Automation
- **RSS Feed Parsing** — Data ingestion from 6 job boards
- **MySQL** — Job storage and deduplication database
- **Gmail API** — Email delivery
- **JavaScript** — Custom HTML email builder inside n8n Code node

---

## 📌 How It Works

1. **Schedule Trigger**
   Workflow fires automatically every 10 hours.

2. **Fetch RSS Feeds**
   All 6 job boards are fetched simultaneously. Each node has retry logic (3 attempts, 5s wait) and an error output path so one failing feed does not block the rest.

3. **Merge All Jobs**
   Outputs from all 6 feeds are combined into one unified list.

4. **Normalize Job Data**
   Standardizes every job into consistent fields regardless of source:
   - Job Title
   - Company
   - Location (defaults to Remote)
   - Source
   - Link
   - Published Date (ISO format)

5. **Filter Valid Jobs**
   Drops any job missing a title or link.

6. **Filter By Job Role**
   Keeps only jobs whose title contains the configured keyword (e.g. AI Engineer). Everything else is dropped here.

7. **Remove Duplicates**
   Removes within-run duplicate entries based on job link.

8. **Read Existing Links from MySQL**
   Fetches all previously stored job links from the database (runs once per execution).

9. **Find New Jobs Only**
   Anti-join comparison — keeps only jobs whose links do not exist in MySQL.

10. **If New Jobs Found**
    Branches into two paths — new jobs path or silent no-op if nothing new.

11. **Add Timestamp**
    Stamps each new job with the current datetime.

12. **Insert Into MySQL**
    Saves all new jobs to the database. Uses INSERT IGNORE to silently skip any duplicate links. Blocks null/undefined rows via WHERE conditions.

13. **Build Email HTML**
    JavaScript code node builds a fully responsive HTML email with source-coded badges and Apply Now buttons for every job.

14. **Send Gmail Alert**
    Sends the email digest to the configured address. On failure, triggers a separate Gmail Failure Alert notification.

---

## 📂 Workflow Structure

```
Schedule Trigger
 ├── Fetch RSS Feeds (WeWorkRemotely, RemoteOK, Himalayas, Jobspresso, WorkingNomads, Remotive)
 ├── Merge All Jobs
 ├── Normalize Job Data
 ├── Filter Valid Jobs
 ├── Filter By Job Role
 ├── Remove Duplicates
 ├── Read Existing Links (MySQL)
 ├── Find New Jobs Only
 ├── If New Jobs Found
 │     ├── Add Timestamp
 │     ├── Insert Into MySQL
 │     ├── Build HTML Email
 │     └── Send Gmail Alert
 │           └── Gmail Failure Alert (on error)
 └── No New Jobs (Fallback)
```

---

## 🗄️ MySQL Setup

Run this once in MySQL Workbench before activating the workflow:

```sql
CREATE DATABASE IF NOT EXISTS remote_job_finder
  CHARACTER SET utf8mb4
  COLLATE utf8mb4_unicode_ci;

USE remote_job_finder;

CREATE TABLE IF NOT EXISTS remote_jobs (
  id             INT AUTO_INCREMENT PRIMARY KEY,
  date_added     DATETIME        NOT NULL,
  job_title      VARCHAR(500)    NOT NULL,
  company        VARCHAR(255)    NOT NULL DEFAULT 'N/A',
  location       VARCHAR(255)    NOT NULL DEFAULT 'Remote',
  source         VARCHAR(100)    NOT NULL,
  link           TEXT            NOT NULL,
  published_date VARCHAR(50)         NULL,
  UNIQUE INDEX uq_link (link(500)),
  INDEX idx_source     (source),
  INDEX idx_date_added (date_added)
) ENGINE=InnoDB
  DEFAULT CHARSET=utf8mb4
  COLLATE=utf8mb4_unicode_ci;
```

---

## 🧩 Setup Instructions

### 1. Install n8n
Self-host or use n8n Cloud.

### 2. Import the Workflow
Upload the provided JSON file into your n8n instance via **Settings → Import Workflow**.

### 3. Configure Credentials
You will need:
- **MySQL** — host, port (3306), database (`remote_job_finder`), username, password
- **Gmail OAuth2** — connect and complete Google authorization

### 4. Set Your Email
Inside both Gmail nodes, set:
```
sendTo: your-email@gmail.com
```

### 5. Set Your Target Job Role
Open the **Filter By Job Role** node and update the keyword to your desired role.

### 6. Activate the Workflow
Toggle the workflow to **Active**. It will run automatically every 10 hours.

---

## 📝 Future Improvements

- Store target job roles in MySQL so they can be updated without touching the workflow
- Add Slack or Telegram notifications as an alternative to email
- Add a relevance scoring system to rank jobs by keyword match in description
- Decrease schedule interval to every 2-3 hours for faster alerts

---

## 🔗 Author

**Aditya Gavhane**
Automating workflows, building AI agents, and streamlining real-world tasks.


