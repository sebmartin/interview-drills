## interview-drill

This repo hosts the `interview-drill` skill (`.claude/skills/interview-drill/SKILL.md`). Every practice **campaign** lives under `campaigns/<name>/` — but `campaigns/` is a plain directory (not a repo itself); each campaign inside it is its **own independent git repo** (gitignored here except `campaigns/README.md`, not otherwise tracked by this repo at all), keeping personal/job-search-sensitive campaign content out of this generic skill's own shareable history, and keeping every campaign fully isolated from every other. See `README.md` for the shape.

### Active campaign

There is no fixed "current campaign" file to read state from mid-session — once a campaign is resumed or created, state it plainly ("Working on campaign: `<name>`") and keep operating on `campaigns/<name>/` for the rest of the session.

At the **start** of a session, if the human's request looks drill-related (mentions a drill, grading, a campaign name, or otherwise clearly means to use this skill) and no campaign has been made active yet this session:

1. Read `.last-campaign` at the repo root, if it exists (plain text, one line, a campaign name).
2. If present, ask: "Resume campaign `<name>` (last active), or pick another?" — don't just silently resume, and don't block on this if the human's request already named a different campaign explicitly (in that case, just use the named one and skip the ask).
3. Once a campaign is chosen (resumed or created), overwrite `.last-campaign` with its name.

This is a light nudge, not a gate — if there's only one campaign, or the human clearly already said which one, don't bother asking.

`.last-campaign` is gitignored — it's a local session convenience, not shared project state.
