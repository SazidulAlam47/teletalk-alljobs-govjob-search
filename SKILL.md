---
name: teletalk-alljobs-govjob-search
description: Search Bangladesh government jobs from the Teletalk AllJobs API, filter out excluded keywords, present matching jobs, and track applied job IDs. Use when the user wants gov job search results from alljobs.teletalk.com.bd, wants to exclude roles by keyword, or wants to save applied government job IDs after confirmation.
---

# Teletalk AllJobs Gov Job Search

Use this skill to search and shortlist government jobs from the Teletalk AllJobs API.

## Core flow

1. Ask the user for:
   - a search keyword, such as `electrical`, `civil`, `mechanical`, `computer`, or `engineer`
   - excluded keywords, such as `Sub Assistant`, `Diploma`
2. Save the preferences in `data/preference.json`.
3. Run the search script with the keyword.
4. Filter out any job whose `job_title` contains an excluded keyword.
5. Show the remaining jobs with only these fields:
   - `job_title`
   - `job_title_bn`
   - `org_name`
   - `org_name_bn`
   - `vacancy`
   - `deadline_date`
   - `application_site_url`
6. If the user confirms they applied, save the `job_primary_id` in `data/appliedJobIds.json`.

## Data files

Store skill data in:
- `data/preference.json`
- `data/appliedJobIds.json`

Use this preference shape:

```json
{
  "keyboard": "",
  "excluded": ["", ""]
}
```

Note: keep the field name as `keyboard` to match the skill data format, even though it represents the search keyword.

## Search behavior

Search using this endpoint:

`GET https://alljobs.teletalk.com.bd/api/v1/published-jobs/search?searchKeyword=<keyword>`

Use the response’s `govtJobs` array.

Filter out a job when any excluded keyword matches, case-insensitive, against `job_title` only.

Treat obvious role modifiers like `Sub Assistant` and `Diploma` as excludes when the user asks for them.

Prefer concise results. Do not show extra API fields.

## Confirmation behavior

When the user says they applied:
- append the `job_primary_id` to `data/appliedJobIds.json`
- avoid duplicates

## Script entry point

Use:
- `scripts/teletalk-alljobs-search.js`

The script should:
- read `data/preference.json`
- call the Teletalk API
- filter excluded keywords
- print the compact job list as JSON

## Notes

- Keep the skill focused on Teletalk government jobs only.
- Do not mix this workflow with BDJobs skill data.
- Favor deterministic filtering over model guesses.
