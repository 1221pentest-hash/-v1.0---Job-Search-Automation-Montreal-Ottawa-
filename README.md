
# 🔍 Job Search Automation

An automated job search pipeline built with [n8n](https://n8n.io) that scrapes IT job listings from multiple Canadian sources, scores them by relevance, saves them to Google Sheets, and sends alerts via Gmail and Telegram — twice a day, hands-free.

> **Focused on Montreal & Ottawa** — built to cut through the noise and surface only relevant Canadian opportunities.

---

## 📸 What It Does

```
Every 12 hours (or manually):

JobBank Montreal ──┐
JobBank Ottawa   ──┤
Adzuna Montreal  ──┼──▶ Merge ──▶ Normalize ──▶ Score ──▶ Filter ──▶ Google Sheet
Adzuna Ottawa    ──┤                                                 ──▶ Gmail Alert
Remotive (CA)    ──┘                                                 ──▶ Telegram Bot
```

### Scoring System (0–10)
| Score | Label | Meaning |
|-------|-------|---------|
| 7–10  | 🔥 Hot   | High priority — apply today |
| 5–6   | 👍 Good  | Worth reviewing |
| 2–4   | ✅ Ok    | Saved but lower priority |
| 0–1   | *(filtered out)* | Irrelevant |

**Score factors:**
- **+3** Montreal / Ottawa / Gatineau location
- **+1** Remote / Canada-wide
- **+3** Salary ≥ $100k
- **+2** Salary ≥ $70k
- **+1** High-value keywords (senior, cloud, AWS, devops, python…)
- **+2** Preferred companies (Shopify, Ubisoft, CGI, Genetec…)
- **−2** Junior / entry-level / unpaid

### Geographic Filtering
European jobs are **automatically excluded** — the normalize step blocks locations from Germany, France, UK, Netherlands, Belgium, and 20+ other European cities, so your sheet stays clean.

---

## 📋 Google Sheet Columns

| Column | Description |
|--------|-------------|
| Date Found | ISO date when the job was scraped |
| Title | Job title |
| Company | Employer name |
| Location | City / region |
| Salary | Range or "Not listed" |
| Score | 0–10 relevance score |
| Label | Hot / Good / Ok |
| Source | Adzuna / JobBank / Remotive |
| URL | Direct link to job posting |
| Applied | Yes / No (fill manually) |
| Notes | Your personal notes |
| Follow Up | Follow-up date or status |

---

## 🚀 Setup

### Prerequisites
- [n8n](https://docs.n8n.io/hosting/) (self-hosted or n8n.cloud)
- A Google account (for Sheets + Gmail)
- A Telegram account (optional, for mobile alerts)
- Free [Adzuna API key](https://developer.adzuna.com)

### Step 1 — Clone the repo
```bash
git clone https://github.com/YOUR_USERNAME/job-search-automation.git
cd job-search-automation
```

### Step 2 — Configure environment
```bash
cp .env.example .env
# Edit .env with your actual credentials
```

### Step 3 — Set up Google Sheet
1. Create a new Google Sheet
2. Set the first row headers exactly as listed in the columns table above
3. Copy the Sheet ID from the URL into your `.env`

### Step 4 — Import the workflow
1. Open n8n → **Workflows** → **Import from file**
2. Select `workflow/job-search-automation.json`
3. Configure credentials for:
   - **Google Sheets** (OAuth2)
   - **Gmail** (OAuth2)
   - **Telegram** (Bot API token)
4. Set your environment variables in n8n under **Settings → Variables**

### Step 5 — Activate
- Toggle the workflow to **Active**
- It will run every 12 hours automatically
- Or click **Execute workflow** to run immediately

---

## 🛠️ Customization

### Change job keywords
Edit the `HTTP-Adzuna-Montreal` and `HTTP-Adzuna-Ottawa` node URLs — change the `what=` parameter:
```
what=cybersecurity        # security jobs
what=data+engineer        # data engineering
what=product+manager      # product management
```

### Add more cities
Duplicate the Adzuna nodes and change the `where=` parameter:
```
where=Toronto
where=Vancouver
where=Quebec+City
```

### Adjust scoring weights
Open the `Code-Scoring` node and edit the score values to match your priorities.

### Change the schedule
Edit the `Schedule Trigger` node — default is every 12 hours.

---

## 📁 Repository Structure

```
job-search-automation/
├── workflow/
│   └── job-search-automation.json   # n8n workflow export
├── web/
│   └── index.html                   # Local dashboard (open in browser)
├── docs/
│   └── sheet-template.md            # Google Sheet setup guide
├── .env.example                     # Environment variables template
├── .gitignore
└── README.md
```

---

## 🔒 Security Notes

- **Never commit `.env`** — it's in `.gitignore` by default
- Rotate your Adzuna keys periodically
- The Google Sheet ID is not secret but the Sheet itself should have proper access controls
- Telegram bot token should be treated as a password

---

## 📊 Sample Output

**Telegram alert:**
```
🔍 Job Alert – 2026-06-03

📊 Found 47 new listings

🔥 Hot Jobs:
• IT Systems Architect @ Targeted Talent — Ottawa | $145,600 - $187,200
• Senior DevOps Engineer @ Shopify — Remote | $120,000 - $160,000

👍 Good Matches:
• IT Service Desk Manager @ Equest — Ottawa | Not listed
• Product Manager, IT @ Brookfield — Ottawa | Not listed
```

---

## 🤝 Contributing

PRs welcome! Ideas for improvement:
- [ ] LinkedIn scraping via RapidAPI
- [ ] Indeed Canada integration
- [ ] AI-powered cover letter generator per job
- [ ] Duplicate detection across runs
- [ ] Web dashboard with filtering/search

---

## 📄 License

MIT — do whatever you want with it.

## Screenshots

### Workflow
![n8n Workflow](screenshots/workflow.png)

### Google Sheet
![Job Tracker](screenshots/sheet.png)

### Telegram Alert
![Telegram Bot](screenshots/telegram.png)

---

*Built with n8n • Adzuna API • Google Sheets • Telegram*
