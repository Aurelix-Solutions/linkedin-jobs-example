# LinkedIn Jobs Scraper: Python example

**LinkedIn Jobs by Country Filter to CSV, No Login.** Public LinkedIn job postings by keyword and country: title, company, location, seniority, job type, salary text, description. Price, example input and output, and FAQ: [LinkedIn Jobs Country Filter Scraper on the Aurelix tools site](https://aurelix-solutions.github.io/tools/linkedin-jobs/). Run it on the Apify Store: [apify.com/aurelix/linkedin-jobs](https://apify.com/aurelix/linkedin-jobs).


**The problem:** You need LinkedIn job postings for one country in a spreadsheet, without logging in to LinkedIn or copying them by hand.

**The tool:** [LinkedIn Jobs Scraper](https://apify.com/aurelix/linkedin-jobs), an Apify Actor by Aurelix. This repository is a small, runnable Python example for it.

**The price:** $1.50 per 1,000 jobs on Apify's free plan and $1.05 on paid plans, plus $0.00005 per run; you pay only for results. Apify's free plan includes $5 of credit a month.

**Try it** (needs an Apify API token from Console > Settings > API & Integrations):

```bash
pip install -r requirements.txt
export APIFY_TOKEN=your_token_here
python main.py
```

[![LinkedIn Jobs Country Filter Scraper: 75-second demo video](https://api.apify.com/v2/key-value-stores/twkCCtTWzsi8fVbx5/records/linkedin-jobs-demo-thumb-v1.png)](https://api.apify.com/v2/key-value-stores/twkCCtTWzsi8fVbx5/records/linkedin-jobs-demo-v1.mp4)

**[▶ Watch the 75-second demo](https://api.apify.com/v2/key-value-stores/twkCCtTWzsi8fVbx5/records/linkedin-jobs-demo-v1.mp4)**

## What it does

LinkedIn Jobs Scraper turns public LinkedIn job postings into clean rows: title, company, location, salary where LinkedIn shows it, seniority, job type, posted date, job link and the full description. Search by keyword and country (LinkedIn's exact country id, so results stay in the country you picked), or paste LinkedIn job search URLs. Jobs that fail your filters are dropped and never charged.

## Why this over alternatives

Compared with the leading alternatives on the Apify Store (full table on the Store page):

- 25% under the most-used LinkedIn jobs scraper on the free plan ($1.50 vs $2.00 per 1,000).
- LinkedIn's exact country id with other-country results dropped, job type and level filters re-checked on each posting, and filtered-out jobs not charged. One rival is cheaper per job ($0.40).

## When to use it

- Recruiters and staffing agencies tracking new roles in one country
- Job boards and newsletters backfilling fresh postings daily with the new-jobs-only mode
- Market researchers measuring hiring demand by title, city or seniority
- Sales teams spotting companies that are hiring for a role they sell to

## Input example

This is the `linkedin-data-engineer-jobs-canada` input used in `main.py`:

```json
{
  "keywords": [
    "data engineer"
  ],
  "country": "CA",
  "datePosted": "pastWeek",
  "maxResultsPerSearch": 50
}
```

Every input field is documented on the [Store page](https://apify.com/aurelix/linkedin-jobs).

## How to run

1. Get an Apify API token from Apify Console (Settings, API & Integrations).
2. Install and run:

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
export APIFY_TOKEN=your_token_here
python main.py
```

The script starts a run of `aurelix/linkedin-jobs`, prints a few fields from the first results and writes every result to `results.csv`. Lists and nested objects are stored in the CSV as JSON text. Each run is billed to your Apify account (see Pricing below).

## Sample output

One record as returned by a real run (long text is shortened):

```json
{
  "job_id": "4466544261",
  "title": "Data Engineer I",
  "company": "Docebo",
  "company_url": "https://www.linkedin.com/company/docebo",
  "location": "Toronto, Ontario, Canada",
  "countries": [
    "CA"
  ],
  "posted_date": "2026-09-15",
  "posted_ago": "1 month ago",
  "job_url": "https://www.linkedin.com/jobs/view/4466544261/",
  "seniority": "Entry level",
  "employment_type": "Full-time",
  "industries": "Software Development",
  "applicants": "Over 200 applicants",
  "salary_text": "CA$68,600.00/yr - CA$91,500.00/yr",
  "description_text": "Artificial Intelligence. Actual Impact.\n\nAt Docebo, we’re using AI to change how people learn at work—and we mean actually change it. We’re an AI-powered learning platform that helps organizations create, deliver, and manage training all in one place. But our real mission goes deeper: we help teams ... [6951 more characters]",
  "description_html": "<strong>Artificial Intelligence. Actual Impact.<br/><br/></strong>At Docebo, we’re using AI to change how people learn at work—and we mean actually change it. We’re an AI-powered learning platform that helps organizations create, deliver, and manage training all in one place. But our real mission go... [7432 more characters]",
  "search_country": "CA",
  "source_url": "https://www.linkedin.com/jobs/view/4466544261/",
  "scraped_at": "2026-11-04T11:17:58Z"
}
```

## Export to CSV

`main.py` writes `results.csv` with the `csv` module. You can also download any run's results directly:

```
https://api.apify.com/v2/datasets/<datasetId>/items?format=csv&clean=true
```

Send your token in an `Authorization: Bearer <token>` header (or `?token=`) if the dataset is not readable without one. Other formats: `format=json`, `format=xlsx`.

## Google Sheets

- Simplest: use Apify's Google Sheets integration on the Actor or a saved task, so each run's results land in a sheet with no token in the sheet itself.
- Quick pull of one run: `=IMPORTDATA("https://api.apify.com/v2/datasets/<datasetId>/items?format=csv&clean=true")`. This only works without a token if your account lets anyone with the dataset ID read it. Do not paste an API token into a sheet formula: anyone who can view or copy the sheet can read it and use your account.

## n8n

- Apify node: choose "Run an Actor", enter `aurelix/linkedin-jobs`, paste the input above, then read the run's dataset items.
- HTTP Request node: `POST https://api.apify.com/v2/acts/aurelix~linkedin-jobs/run-sync-get-dataset-items` with header `Authorization: Bearer <token>` (stored as an n8n credential) and the input as the JSON body. The response is the list of results. This endpoint waits for the run to finish, so for large runs start the run asynchronously and fetch the dataset when it ends.

## Limits

- LinkedIn shows at most about 1,000 jobs per search; split broad searches by keyword or location.
- The Actor always uses Apify's datacenter proxy; there is no residential option. LinkedIn rate-limits datacenter addresses, so big runs can stop early (the status message says which searches did).
- Keywords match whole words with simple plurals, a few synonyms (nurse: RN, LPN, LVN; engineer: eng; developer: dev, programmer) and, in titles, a shared stem of 5+ letters ('actuary' finds 'Actuarial'). With details off only the title is checked, so postings whose title does not relate are skipped.
- Posted date is a date only; LinkedIn shows no time.
- Salary appears only where LinkedIn displays it on the public page.
- Workplace type (remote/hybrid/on-site) is sent to LinkedIn as a filter but cannot be re-checked, because the public page does not show it.
- LinkedIn can change its public pages; the daily health run catches breakage within 24 hours.

## Pricing

At the time of writing:

Pay per event: you pay only for what the run returns, plus a small start fee. Bronze, Silver, Gold and higher Apify plans pay less per result.

| Event | Free plan | Paid plans (Bronze, Silver, Gold) |
|---|---|---|
| `job-result` | $1.50 per 1,000 jobs | $1.05 per 1,000 jobs |
| `apify-actor-start` | $0.00005 per run | $0.00005 per run |

Example: 1,000 jobs cost $1.50 on the free plan and $1.05 on a paid plan, plus the start fee. Apify's free plan includes $5 of credit a month, enough for about 3,300 jobs.

Apify datacenter proxy is included in the price; no other proxy is used. Dropped and duplicate jobs are never charged.

Check the [Store page](https://apify.com/aurelix/linkedin-jobs) for current prices.

## Links

- Actor: https://apify.com/aurelix/linkedin-jobs
- Questions, bugs and feature requests: the Issues tab on the Store page.

## Licence

The example code in this repository is released under the MIT licence (see `LICENSE`). The Actor itself runs on Apify and is billed through your Apify account.
