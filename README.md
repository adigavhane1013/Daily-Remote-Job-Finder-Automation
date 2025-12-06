# Daily-Remote-Job-Finder-Automation
Alright, here’s a **clean, professional, sharp README** that actually makes your project look like a real automation tool instead of some hobby script. It explains the project, the workflow, the architecture, and how to run it — the way recruiters and devs expect.

Use this as your **README.md** in your repo.

---

# **Daily Remote Job Finder Automation**

An automated workflow built using **n8n** that collects remote job postings from multiple job boards, filters duplicates, stores new listings in Google Sheets, and sends formatted HTML email digests directly to your inbox.

This system runs on a scheduled interval and keeps you updated with the latest remote jobs without manual searching.

---

## 🚀 **Features**

* **Automatic Job Aggregation**
  Fetches job listings from:

  * WeWorkRemotely
  * Remote.co
  * Himalayas

* **Smart Filtering**

  * Normalizes job data
  * Removes duplicates
  * Detects only *new* job postings
  * Compares against previously stored links in Google Sheets

* **Google Sheets Integration**

  * Stores each job entry with timestamp
  * Serves as a historical database
  * Prevents re-notification of old jobs

* **Responsive HTML Email Alerts**

  * Clean, readable job digest
  * Includes job title, company, location, source, and direct apply link
  * Delivered via Gmail API

* **Scheduled Execution**
  Runs every few hours automatically using n8n’s built-in scheduler.

---

## 🛠️ **Tech Stack**

* **n8n (Workflow Automation)**
* **RSS Feed Parsing**
* **Google Sheets API**
* **Gmail API**
* **JavaScript (Custom HTML Email Builder)**

---

## 📌 **How It Works**

1. **Schedule Trigger**
   The workflow runs at your preferred interval (e.g., every 2 hours).

2. **Fetch RSS Feeds**
   Jobs are pulled from multiple remote job platforms.

3. **Split & Normalize Data**
   Each job entry is standardized into fields like:

   * Job Title
   * Company
   * Location
   * Source
   * Published Date
   * Apply Link

4. **Duplicate Removal**
   Removes repeated entries within the same run.

5. **Compare With Google Sheets**
   Checks if the job’s link already exists in your historical dataset.

6. **Identify New Jobs**
   Only jobs not previously seen are kept.

7. **Append to Google Sheets**
   New jobs are stored with:

   * Timestamp
   * Job Title
   * Company
   * Location
   * Source
   * Apply Link

8. **Generate Email Digest**
   A clean, formatted HTML email is created with JavaScript.

9. **Send Email via Gmail API**
   Sends a remote job summary straight to your inbox.

---

## 📂 **Workflow Structure**

```
Schedule Trigger
 ├── Fetch RSS Feeds (WWR, Remote.co, Himalayas)
 ├── Split Job Items
 ├── Merge All Jobs
 ├── Normalize Job Data
 ├── Filter Valid Jobs
 ├── Remove Duplicates
 ├── Read Google Sheets (existing links)
 ├── Find New Jobs Only
 ├── If New Jobs Found
 │     ├── Add Timestamp
 │     ├── Append to Google Sheets
 │     ├── Build HTML Email
 │     └── Send Email Alert
 └── No New Jobs (Fallback)
```

---

## 📧 **Email Preview**

* Clean HTML Table
* Job Title
* Company
* Location
* Source (with badges)
* “Apply Now” button

Looks like a proper job newsletter, but completely automated.

---

## 🧩 **Setup Instructions**

### 1. Install n8n

Self-host or use n8n Cloud.

### 2. Import the Workflow

Upload the provided JSON file from this repo into your n8n instance.

### 3. Configure Credentials

You will need:

* **Google Sheets OAuth**
* **Gmail OAuth**

Give permissions for:

* Reading/writing Google Sheets
* Sending emails via Gmail

### 4. Create a Google Sheet

Add a sheet with columns:

* Date Added
* Job Title
* Company
* Location
* Source
* Link

### 5. Set Your Email

Inside the Gmail node, set:

```
sendTo: your-email@gmail.com
```

### 6. Enable the Workflow

Turn on the workflow so it runs automatically.

---

## 📝 **Future Improvements**

* Add AI ranking to sort jobs by suitability
* Integrate more job boards
* Add Slack/Telegram alerts
* Add natural language filters (e.g., “software”, “AI”, “Python”)

---

## 🤝 **Contributing**

If you want to optimize workflows or add more job sources, feel free to open a pull request.

---

## 🔗 **Author**

**Aditya Gavhane**
Automating workflows, building AI agents, and streamlining real-world tasks.


