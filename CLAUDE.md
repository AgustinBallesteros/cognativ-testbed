@AGENTS.md

## First run

If `.claude/TEMPLATE` exists, this is a fresh, unconfigured fork of the cognativ-testbed
whitelabel testbed. Before doing anything else in your first response of the session:

1. Say in one line that this is an unconfigured testbed fork and you need a few details.
2. Invoke the `/new-prototype` skill and follow it — it runs the intake (AskUserQuestion) and
   applies the answers across `package.json`, `styles/globals.css`, `public/favicon.svg`,
   `README.md`, `ONBOARDING.md`, and `pages/index.tsx`.

Do this even if the user opened the session with an unrelated request — ask the intake questions
first, then carry out their request against a properly configured project. The one exception is a
read-only question about the repo, which you can just answer; offer the intake afterward.

Never guess the project name, description, or accent color from the directory name or git remote.

`.claude/TEMPLATE` is deleted at the end of the intake, which retires this section along with it.
