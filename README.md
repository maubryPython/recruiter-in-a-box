# recruiter-in-a-box

**Two Claude skills for job hunting: `jobSearch` and `resumeCreate`.**

They work together:

- **job-search** (`jobSearch`) — finds active job postings that match your profile, and
  validates every single one against the employer's own careers site before showing it to you.
  No dead links, no aggregator apply links you can't actually apply through.
- **resume-create** (`resumeCreate`) — turns a job description into a tailored 2-page resume,
  in either a human-readable layout or a keyword-optimized version built to survive applicant
  tracking systems.

They hand off to each other: run `jobSearch`, pick a result, say "prepare a resume for #2" and
the resume skill takes over with the job description already in hand.

These are **templates**. They contain no personal data. You fill in your own profile and work
history once, during first-run setup, and then both skills work from that.

---

## Download

Either clone the repo, or grab
[`recruiter-in-a-box-skills.zip`](recruiter-in-a-box-skills.zip) from the root of this repo
(also attached to the [latest release](../../releases/latest)). The zip contains both skill
folders and this README.

---

## 1. Install

Each skill is a folder containing a `SKILL.md` and a `references/` directory. Copy both folders
into your skills directory:

**Claude desktop app (Cowork) or Claude Code, just for you:**

```
~/.claude/skills/job-search/
~/.claude/skills/resume-create/
```

**Claude Code, shared with a project/repo:**

```
<project>/.claude/skills/job-search/
<project>/.claude/skills/resume-create/
```

On Windows, `~/.claude` is `C:\Users\<you>\.claude`.

**Or on claude.ai:** Settings → Capabilities → Skills → create a skill and upload the folder
as a zip. Do this once per skill.

Restart Claude (or start a new conversation) after copying. Ask "what skills do you have?" to
confirm both show up.

### The docx and xlsx skills

`resumeCreate` builds Word documents and `jobSearch` saves an Excel spreadsheet, so both lean
on Anthropic's `docx` and `xlsx` skills. Those ship with Claude in most setups. If Claude says
it can't find them, ask it to produce the resume as a .docx anyway — it will manage — or
install those two skills from Anthropic's skills repository.

---

## 2. Set up your profile — do this before the first real run

Both skills ship with `[FILL IN]` placeholders and will stop and walk you through setup rather
than guessing. You can either fill the files in by hand or — easier — let Claude do it:

> "Set up the job-search and resume-create skills for me. Here's my current resume."

...and attach your longest, most complete resume. Claude will extract your history into the
master record and ask you the rest.

**Four files to populate:**

| File | What goes in it |
|---|---|
| `job-search/references/candidate-profile.md` | Target roles, locations, salary floor, recency window, priority employers, the role-title vocabulary employers use for your kind of work |
| `resume-create/references/master-resume.md` | Every job, every title, every accomplishment bullet you can remember. This is a *pool*, not a resume — ten pages is fine |
| `resume-create/references/profile-defaults.md` | Contact block, your full skills inventory, primary discipline, file naming, where files get saved |
| `resume-create/references/patterns.md` | Two condensed résumé blocks, authored once and reused — a combined promotion-path entry, and a compact version of your earlier era |

**Be generous with the master record.** Every claim in every resume these skills produce has
to trace back to a line in it. A bullet you leave out is a bullet that can never appear on a
tailored resume — and a keyword you can't back gets reported to you as a gap instead of being
quietly invented. That's the point.

---

## 3. Use it

**Find jobs:**

```
Use jobSearch to find 5 jobs to apply for
```

Add overrides inline: `...remote only`, `...posted in the last 7 days`, `...at Acme`,
`...only people-manager roles`.

Expect it to check in mid-run with a batch of leads before it spends validation effort on them.
Triage there — it saves a lot of time.

**Tailor a resume:**

```
I'm applying for Senior Data Analyst at Acme. Here's the JD: [paste full text]
```

or, straight off a search result:

```
Prepare a resume for #2
```

It will ask which template you want (human-readable, ATS keyword, or both) and a few questions
about emphasis before drafting.

**Other things it handles:** cover letters and LinkedIn "About" rewrites, drawn from the same
master record. Just ask after the resume is done.

---

## 4. What makes these different from asking Claude for a resume

Worth knowing, because it explains the behavior that might otherwise look fussy:

- **Every apply link is the employer's own.** Aggregators (Indeed, LinkedIn, ZipRecruiter,
  Built In, Glassdoor…) are used for *discovery only*. If the skill can't find and fetch the
  posting on the employer's own careers site or ATS, it drops the job and tells you why rather
  than handing you a link that goes nowhere. You'll sometimes get four jobs instead of five.
  That's working as designed.
- **Postings are verified live, not just fetched.** Careers sites routinely return a normal
  200 response with full branding while the page body says the job is gone, so the skill scans
  the text for those markers. It also never trusts an aggregator's posted date in either
  direction — stale-looking reqs get re-checked on the employer's site, because that date is
  wrong surprisingly often.
- **Nothing gets invented on a resume.** A posting keyword that can't be traced to your master
  record is reported to you as a gap — your interview-prep list — instead of being written in.
  Keyword optimization changes the *vocabulary* of a claim, never the claim.
- **The ATS template is deliberately ugly.** Single column, black text, one font, no tables,
  no graphics, company name repeated in every role block. It looks like 1995 on purpose:
  that's what parses cleanly and what a recruiter's boolean search actually matches. Don't
  improve the design.

---

## 5. Optional: run it on a schedule

Claude can run `jobSearch` automatically — weekday mornings, say — and have the results
waiting. Just ask: *"run jobSearch every weekday at 7am and send me the results."*

---

## 6. A browser helps a lot

Most large employers render their careers pages with JavaScript, so a plain fetch returns an
empty shell and search engines lag days behind on fresh postings. A real browser is the only
way to see true posted dates.

If you use Claude Code or the desktop app, the **Claude in Chrome** extension makes `jobSearch`
substantially better:
https://chromewebstore.google.com/detail/fcoeoabgfenejglbffodgkkbkcdhcgfn

Installing isn't enough — the Claude side panel needs to be open and signed in to the same
account. Without a browser the skill still works, but it will tell you up front that coverage
of the big employers will be thin.

---

## 7. Customizing

Both skills are plain Markdown. Edit them. The parts most worth tuning after a few real runs:

- The **employer vocabulary** list in `candidate-profile.md` — add every new title family a run
  surfaces. This is the single biggest lever on search coverage; whole job families stay
  invisible if their titles share no keyword with how you describe yourself.
- The **candidate-specific homonyms** list in `ats-keyword-template.md` — words that recur in
  your history and mean something different in your target function. Catching these is what
  keeps a keyword-optimized resume from collapsing in the screening call.
- The **priority employers** table — five to ten is the right size.

---

## License

MIT — see [LICENSE](LICENSE). Use it, fork it, rewrite it for your own field.

The two skills are plain Markdown with no code and no dependencies. If you improve the
validation rules or the ATS method, a pull request is welcome.
