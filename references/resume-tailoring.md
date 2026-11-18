# Resume tailoring

## Source of truth

The profile supplies the base resume as one of two keys. **Read only** either way. Never
edit it, never commit to its repo. Copy it into a scratch directory per role and tailor
there.

- `<RESUME_TEX>`: LaTeX source. Preferred when available, because edits keep the original
  layout exactly. If both keys are set, use this one.
- `<RESUME_PDF>`: a PDF with no source. Use when `<RESUME_TEX>` is empty.

With `<RESUME_TEX>`, a local `pdflatex` build should reproduce the original PDF
pixel-identically. Verify that once before trusting tailored variants.

With `<RESUME_PDF>`, a PDF cannot be edited faithfully in place, so tailoring means
rebuilding it:

1. Extract the text (`pdftotext -layout`) and render page images (`pdftoppm -r 100 -png`).
   Read both: the text for exact wording, the images for layout, fonts, and section order.
2. Write the content to a scratch source file (HTML or LaTeX) that matches the original
   layout as closely as you can, and render it to PDF.
3. Do this once for the base and compare it to the original page by page. Confirm nothing
   was dropped (employers, dates, titles, links, education) and it still fits the same page
   count before trusting tailored variants. Reuse that scratch source for each role.
4. If extraction is garbled (scanned or image-only PDF), stop and ask for a text-based PDF
   or the source. Do not retype a resume from a guess.

Treat the extracted text as the base for the truthfulness bar below, exactly as the `.tex`
would be.

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

Every rewritten claim must remain literally true against the base resume (the `.tex` or the
PDF text) and the profile's
**Confirmed resume facts** section. Rewrite for emphasis, framing, and vocabulary. Do not
invent numbers, scope, seniority, or outcomes.

If you cannot establish that a reworded bullet is still true, ask rather than softening it
into something vague. That question is worth interrupting a run for; nothing else is. When
the answer arrives, add it to the profile's confirmed-facts list so the next run has it.

## Building the PDF

Builds are independent, so run the whole batch in parallel in the background rather than
one per application. The LaTeX notes below apply only on the `<RESUME_TEX>` path, or if you
chose LaTeX to rebuild a PDF-only resume.

TinyTeX gotcha, if the environment uses it: the `ls-R` index files can be root-owned and
stale, so `kpsewhich` cannot see newly installed packages and `tlmgr` cannot refresh them.
Workaround that works: copy the needed `.sty` files into the build directory next to the
`.tex` (for this resume: `titlesec`, `enumitem`, `fancyhdr`, and `babel/english.ldf`).
`tlmgr` also needs a pinned repo when mirrors have moved past the installed release.

## Checking the result

The Simplify browser extension scores resume-to-JD keyword match directly on the
application page. It rated one untailored resume 57/100 on a product design req. Use it as
a free signal when the panel is visible, but never let it push a claim past what is true.
