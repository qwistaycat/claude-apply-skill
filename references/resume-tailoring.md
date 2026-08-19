# Resume tailoring

## Source of truth

`<RESUME_TEX>` from the profile. **Read only.** Never edit it, never commit to its repo.
Copy it into a scratch directory per role and tailor there.

A local `pdflatex` build should reproduce the original PDF pixel-identically. Verify that
once before trusting tailored variants.

Output: `<RESUME_OUT_DIR>/<RESUME_FILENAME>`.

## The rubric

Score the base resume against the target company and role on three axes, then rewrite until
**each experience scores 90%+**.

1. **Business fit.** Research the company and the role. What are their business needs,
   leadership principles, and stated values? Does the experience show alignment through
   concrete examples, not adjectives?
2. **Job description match.** Is the experience relevant to the posting's expectations,
   skills, hard requirements, and nice-to-haves? Mirror their vocabulary where it is
   honestly applicable.
3. **Signaled impact.** Measurable, tangible impact on the company or its users. A
   recruiter, an HR screener, and a product lead should each see professional work that
   brought value.

Tailor **bullet points, not just the skills line.** Reordering skills is the cheap move and
it does not move the score.

## The truthfulness bar

Every rewritten claim must remain literally true against the base resume and the profile's
**Confirmed resume facts** section. Rewrite for emphasis, framing, and vocabulary. Do not
invent numbers, scope, seniority, or outcomes.

If you cannot establish that a reworded bullet is still true, ask rather than softening it
into something vague. That question is worth interrupting a run for; nothing else is. When
the answer arrives, add it to the profile's confirmed-facts list so the next run has it.

## Building the PDF

Builds are independent, so run the whole batch in parallel in the background rather than
one per application.

TinyTeX gotcha, if the environment uses it: the `ls-R` index files can be root-owned and
stale, so `kpsewhich` cannot see newly installed packages and `tlmgr` cannot refresh them.
Workaround that works: copy the needed `.sty` files into the build directory next to the
`.tex` (for this resume: `titlesec`, `enumitem`, `fancyhdr`, and `babel/english.ldf`).
`tlmgr` also needs a pinned repo when mirrors have moved past the installed release.

## Checking the result

The Simplify browser extension scores resume-to-JD keyword match directly on the
application page. It rated one untailored resume 57/100 on a product design req. Use it as
a free signal when the panel is visible, but never let it push a claim past what is true.
