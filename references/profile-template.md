# Profile template

Copy this to `~/.claude/apply-profile.md` and fill it in. That path is outside this
repository on purpose, so real values can never be committed.

```bash
cp references/profile-template.md ~/.claude/apply-profile.md
chmod 600 ~/.claude/apply-profile.md
```

---

## Identity

| Field | Value |
|---|---|
| FULL_NAME | |
| EMAIL | |
| PHONE | |
| STREET_ADDRESS | |
| CITY_STATE_ZIP | |
| LINKEDIN | |
| PORTFOLIO | |
| GITHUB | |
| HOW_DID_YOU_HEAR | |
| WORK_AUTH | e.g. US citizen, authorized to work, no sponsorship now or in future |
| SALARY_ANSWER | e.g. Negotiable |
| EARLIEST_START | |

## Self-identification

Fill these in if you want them answered. Leave a row blank to decline that question.

| Field | Value |
|---|---|
| PRONOUNS | |
| GENDER | |
| RACE_ETHNICITY | |
| VETERAN_STATUS | |
| DISABILITY_STATUS | |
| SMS_CONSENT | |

## Education

| Field | Value |
|---|---|
| SCHOOL_1 | |
| DEGREE_1 | include the picklist fallback, e.g. "Master's; if no HCI option use M.S." |
| DEGREE_STATUS_1 | Complete or In progress |
| MAJOR_1 | include the picklist fallback |
| DATES_1 | |
| GPA_1 | |
| SCHOOL_2 | |
| DEGREE_2 | |
| DEGREE_STATUS_2 | |
| MAJOR_2 | |
| DATES_2 | |
| GPA_2 | |

## Work history

Canonical employer names and titles, used to repair ATS resume-parser output.

| Employer | Title | Dates |
|---|---|---|
| | | |

## Role filter

| Field | Value |
|---|---|
| TARGET_TITLES | |
| LOCATION_PREF | |
| SENIORITY | |
| HARD_EXCLUDE | |
| CONTEXT | actual years of experience, for answering screeners honestly |

## Locations

| Field | Value |
|---|---|
| SHEET_ID | from the tracker sheet URL |
| SHEET_GID | from the tracker sheet URL |
| SHEET_TAB_NAME | |
| DRIVE_FOLDER_ID | from the Drive folder URL |
| DRIVE_FOLDER_NAME | |
| GOOGLE_ACCOUNT | include the browser account index, e.g. u/2 |
| RESUME_TEX | path to the base resume source, treated as read only |
| RESUME_OUT_DIR | where tailored PDFs are written |
| RESUME_FILENAME | naming pattern, e.g. "Name - Resume - <Company>.pdf" |

## Confirmed resume facts

Details that are true and useful when tailoring but are not obvious from the base resume
text. Each one must be something you have actually confirmed.

- 

## Known constraints

- 
