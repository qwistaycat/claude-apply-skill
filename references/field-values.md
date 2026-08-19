# Field mapping

Which profile key goes in which form field. **The values themselves live in
`~/.claude/apply-profile.md`**, which is untracked. Read that file first. Nothing in this
repository should ever contain a real address, phone number, or self-identification answer.

## Contact and identity

| Form field | Profile key |
|---|---|
| Full name, First / Last name | `FULL_NAME` |
| Email | `EMAIL` |
| Phone | `PHONE` |
| Street address, Address line 1 | `STREET_ADDRESS` |
| City, State, Zip / Postal code | `CITY_STATE_ZIP` |
| LinkedIn profile | `LINKEDIN` |
| Portfolio, Personal website | `PORTFOLIO` |
| GitHub | `GITHUB` |
| How did you hear about us | `HOW_DID_YOU_HEAR` |
| Work authorization, sponsorship now, sponsorship in future | `WORK_AUTH` |
| Salary expectation, desired compensation | `SALARY_ANSWER` |
| Earliest start date, availability | `EARLIEST_START` |

### Address rule

Enter the street address **only when the field is required**. City, state, and zip are
fine anywhere. This is an explicit preference, not a guess.

## Self-identification

Fill these from the profile rather than declining. Declining is the wrong default here.

| Form field | Profile key |
|---|---|
| Preferred pronouns | `PRONOUNS` |
| Gender | `GENDER` |
| Race / ethnicity | `RACE_ETHNICITY` |
| Protected veteran status | `VETERAN_STATUS` |
| Disability status | `DISABILITY_STATUS` |
| SMS / text message consent | `SMS_CONSENT` |

## Education

Most ATSs take two entries. Use `SCHOOL_1` for the most recent.

| Form field | Profile key |
|---|---|
| School, Institution | `SCHOOL_1` / `SCHOOL_2` |
| Degree | `DEGREE_1` / `DEGREE_2` |
| Degree status | `DEGREE_STATUS_1` / `DEGREE_STATUS_2` |
| Major, Field of study, Discipline | `MAJOR_1` / `MAJOR_2` |
| Start and end dates | `DATES_1` / `DATES_2` |
| GPA | `GPA_1` / `GPA_2` |

Degree status is the field most often gotten wrong. A completed degree is Complete even
when the graduation date is in the near future and the ATS defaults to "in progress".

Picklists frequently lack the exact degree or major. The profile records the fallback to
use for each; take the fallback rather than leaving the field blank.

## Work history

The profile's work history table is the canonical form of each employer name and title.
ATS resume parsers routinely stuff an entire resume line into the Employer field. After
any resume upload that re-parses the PDF, walk the work-experience blocks and trim each
Employer back to the profile's name.

## Screener honesty

Answer screeners accurately even when the honest answer costs the application. Check the
profile's `CONTEXT` line for actual years of experience before answering any "do you meet
all basic qualifications" question. A No there is a submitted application, not a blocker,
and does not belong in the final blocked table.

## Things you may never do

Creating an account, entering or choosing a password, and solving a CAPTCHA are all
prohibited. When a flow requires any of them, fill everything else, leave the tab open,
log the row with an empty applied date, and put it in the blocked table.
