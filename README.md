# Scrum Plan — What This Is and Isn't

## What this is

A real, honest restructuring of already-built, already-tested work
(`elt-selfservice-analytics/`) into genuine Scrum artifacts: epics, user
stories with real acceptance criteria, story points, two sprints, a
sprint goal per sprint, and a retro that reflects what actually happened
during the build — including a real bug (see BUG-1 in `SCRUM_PLAN.md`)
that was found and fixed, described honestly rather than smoothed over.

The acceptance criteria in this plan are not invented after the fact to
match code that happened to already work — they describe the actual,
verifiable behavior the test suite checks (23 tests, all passing,
`elt-selfservice-analytics/tests/`).

## What this is not

This is not live, team-based Scrum experience, and the CV/cover letter
language built from this should never claim that it is. Specifically:

- **It's solo, not a team.** Real Scrum involves a product owner,
  cross-functional team members, and genuine negotiation over scope and
  priority. This plan has one person doing all the roles, which removes
  the actual hard part of Scrum — coordinating and re-prioritizing with
  other people.
- **It's retroactive, not live.** The work was already done before the
  sprints were defined. A real sprint involves genuine day-by-day
  uncertainty — re-planning when something takes longer than expected,
  discovering a story was mis-scoped mid-sprint, and so on. This plan's
  "Sprint 1 goal" was written knowing Sprint 1 would succeed, because it
  already had.
- **Story points are assigned after the fact.** Real story-point
  estimation happens before work starts and is calibrated against actual
  velocity over multiple sprints. A single retroactive plan can't
  reproduce that calibration loop, and the retro says so directly.

## How to talk about this honestly (e.g. in an interview)

If asked "have you used Scrum," the accurate answer is: "I've structured
a personal project into a real Jira-based Scrum board — epics, sized
user stories with acceptance criteria, two sprints, and a retro — to
practice the mechanics properly, but that was self-run and retroactive,
not live team Scrum. I'm motivated to learn the real thing, especially
the parts a solo retroactive plan can't teach: negotiating scope with a
team and re-planning under real uncertainty." That's accurate, shows
genuine initiative, and doesn't overstate what this is.

## Next step

Copy `SCRUM_PLAN.md` into the "My Scrum Space" Jira board:
1. Create 4 epics matching the 4 sections.
2. Create one story per bullet, with the acceptance criteria as the
   story description and the point value as story-point estimate.
3. Create Sprint 1 (STORY-1 through STORY-4) and Sprint 2 (STORY-5
   through STORY-11), mark all as Done.
4. Add the retro as a Confluence page linked from the Jira project (you
   already have a Confluence space connected — this is a natural fit for
   "Known Issues & Decisions" or a new "Sprint Retro" page).

Once this is actually in Jira, tell me and I'll update the CV/cover
letter language to reference it accurately (e.g. "structured a project
into a 2-sprint Scrum backlog in Jira, including a retro" rather than the
current lighter "motivated to learn Scrum" framing).
