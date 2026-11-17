# /apply

A Claude Code skill for running a daily job-application batch end to end: source roles,
tailor a resume per role, fill and submit the application, upload the tailored PDF to
Google Drive, log every submission to a Google Sheets tracker, then report once with a list
of what is blocked and why.

Built from a manual run of roughly 40 product design applications, so the reference files
are the actual working recipes rather than a guess at what should work. The per-ATS notes
are the part most worth stealing.

## Install

```bash
git clone https://github.com/qwistaycat/claude-apply-skill.git ~/.claude/skills/apply
```

Then create your profile, which is where every personal value lives:

```bash
cp ~/.claude/skills/apply/references/profile-template.md ~/.claude/apply-profile.md
chmod 600 ~/.claude/apply-profile.md
```

Fill that file in, start a new Claude Code session, and run `/apply`. The skill listing
loads at startup, so it will not appear in an already-running session.

## No personal data in this repository

`~/.claude/apply-profile.md` sits **outside** the repository on purpose. It holds the name,
address, phone number, self-identification answers, sheet and Drive ids, and resume paths.
Nothing here should ever contain a real value; the skill and its references use
`<PLACEHOLDER>` keys that resolve against that file.

If you fork this, keep it that way. An application profile is exactly the pile of data you
do not want in a public git history.

## Layout

| File | Contents |
|---|---|
| `SKILL.md` | The six-phase workflow and the run contract |
| `references/profile-template.md` | The blank profile to copy and fill in |
| `references/ats-recipes.md` | Per-ATS fill recipes: Ashby, Greenhouse, Lever, Gem, Workable, Phenom, Pinpoint, Avature, iCIMS, Workday, Indeed |
| `references/sheet-io.md` | Reading, writing, and verifying a Google Sheets tracker through a logged-in browser, plus Drive uploads |
| `references/resume-tailoring.md` | The three-part scoring rubric, the LaTeX build, and the PDF-only path |
| `references/field-values.md` | Which profile key goes in which form field |

## What it does not do

Three actions are off limits to the agent, and they are the only legitimate blockers:
creating an account, entering a password, and solving a CAPTCHA. iCIMS and most Workday
tenants require all three, so those postings always come back in the blocked report for a
human to finish. Everything else the run resolves on its own.

It also will not invent resume claims. Tailoring rewrites emphasis and vocabulary against a
rubric; anything it cannot verify as true, it asks about instead of softening.

## Requirements

- Claude Code with browser control (the `claude-in-chrome` MCP server) against a Chrome
  profile already signed in to Google
- A Google Sheets tracker with the column layout described in `SKILL.md`
- A Google Drive folder for tailored resumes
- A base resume, as either LaTeX source (`RESUME_TEX`) or a text-based PDF (`RESUME_PDF`).
  `pdflatex` is needed for LaTeX. LaTeX source gives the most faithful tailored copies; a
  PDF is rebuilt from its extracted text

## License

MIT
