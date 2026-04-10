# Teletalk AllJobs Gov Job Search (OpenClaw Skill)

Search Bangladesh government jobs from Teletalk AllJobs, filter out unwanted roles using excluded keywords, and track applied job IDs.

## ClawHub

Project page: https://clawhub.ai/sazidulalam47/teletalk-alljobs-govjob-search

## What This Skill Does

This skill is built for an OpenClaw workflow where the user provides:

- a search keyword (stored as `keyboard` in preferences)
- a list of excluded keywords

The script then:

1. Reads preferences from `data/preference.json`
2. Calls the Teletalk search API
3. Filters out jobs matching excluded keywords (case-insensitive)
4. Prints a compact JSON result list

The skill can also store applied job IDs in `data/appliedJobIds.json` after user confirmation.

## Project Structure

```text
teletalk-alljobs-govjob-search/
├─ SKILL.md
├─ README.md
├─ data/
│  ├─ preference.json
│  └─ appliedJobIds.json
└─ scripts/
   └─ teletalk-alljobs-search.js
```

## Requirements

- Node.js 18+ recommended
- Internet access (for Teletalk API requests)

No external npm packages are required.

## Data Files

### `data/preference.json`

Current expected shape:

```json
{
    "keyboard": "",
    "excluded": []
}
```

Notes:

- Keep the field name `keyboard` exactly as-is for compatibility with this skill.
- `keyboard` is treated as the search keyword.
- `excluded` should be an array of strings.

Example:

```json
{
    "keyboard": "electrical",
    "excluded": ["Sub Assistant", "Diploma"]
}
```

### `data/appliedJobIds.json`

Stores job IDs that the user has already applied to:

```json
[]
```

When integrated into an OpenClaw flow, append `job_primary_id` values and avoid duplicates.

## How Search Works

### API endpoint

The script calls:

`GET https://alljobs.teletalk.com.bd/api/v1/published-jobs/search?searchKeyword=<keyword>`

and uses `govtJobs` from the response.

### Filtering behavior

For each job, a searchable text blob is created from:

- `job_title`
- `job_title_bn`
- `org_name`
- `org_name_bn`
- `vacancy`

If any excluded keyword appears in that combined text (case-insensitive), the job is removed.

## Script Output

The script prints an array of objects containing these fields:

- `job_primary_id`
- `job_title`
- `job_title_bn`
- `org_name`
- `org_name_bn`
- `vacancy`
- `deadline_date`
- `application_site_url`

`deadline_date` is formatted in Asia/Dhaka timezone and printed with `BST` suffix.

## Run the Script

From the project root:

```powershell
node .\scripts\teletalk-alljobs-search.js
```

Or from `scripts/`:

```powershell
node .\teletalk-alljobs-search.js
```

## Typical OpenClaw Flow

1. Ask user for keyword and excluded terms.
2. Save them into `data/preference.json`.
3. Run the script.
4. Show compact job results.
5. If user confirms they applied, append `job_primary_id` to `data/appliedJobIds.json` (no duplicates).

## Troubleshooting

### Error: `Missing preference.keyboard`

Cause:

- `data/preference.json` has empty or missing `keyboard`.

Fix:

- Set a non-empty string in `keyboard`.

### Empty result list

Possible causes:

- keyword has no matching jobs
- excluded keywords are too broad

Fix:

- try a broader keyword
- reduce exclusions

### Network/API issues

Cause:

- internet/DNS/TLS issue or remote API temporary problem

Fix:

- retry later
- verify internet connection

## Notes

- This skill is focused only on Teletalk government jobs.
- Keep it separate from BDJobs or other job-source workflows.
- Filtering is deterministic and text-based; no model guessing is used.
