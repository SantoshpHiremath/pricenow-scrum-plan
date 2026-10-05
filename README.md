# Scrum Plan

A Scrum plan for my ELT and self-service analytics project
(`elt-selfservice-analytics/`): epics, user stories with acceptance criteria,
story points, two sprints, a sprint goal per sprint, and a retro. The full
plan is in `SCRUM_PLAN.md`.

## What it does

I structured the already-built, already-tested project into Scrum artifacts:

- 4 epics: ELT data pipeline, self-service analytics layer, predictive
  modeling (cancellation risk), and CI & verification.
- User stories with acceptance criteria and story points, grouped into two
  sprints with a sprint goal each.
- A retro that reflects what happened during the build, including a bug
  (BUG-1 in `SCRUM_PLAN.md`) that I found and fixed.

The acceptance criteria describe the behavior the test suite verifies (23
tests, all passing, in `elt-selfservice-analytics/tests/`).

## Scope

The plan is solo and retrospective: I wrote the sprints after the work was
done, so the story points and sprint goals reflect the delivered scope. That
makes it a good template for running the same structure live with a team,
where estimation is calibrated against velocity over several sprints and
scope is negotiated with a product owner.

## Results

- Sprint 1: 12 points delivered.
- Sprint 2: 18 points delivered, including 3 points of bug-fix work (BUG-1)
  pulled in mid-sprint.
- The retro records what went well (the Load/Transform boundary held up) and
  what I would change (a definition-of-ready check on the data generator
  would have caught BUG-1 a sprint earlier).

## Project structure

```
README.md        overview of the plan
SCRUM_PLAN.md    epics, stories, acceptance criteria, sprints, retro
```

## Running it

To load the plan into a Jira board such as "My Scrum Space":

1. Create 4 epics matching the 4 sections.
2. Create one story per bullet, with the acceptance criteria as the story
   description and the point value as the story-point estimate.
3. Create Sprint 1 (STORY-1 through STORY-4) and Sprint 2 (STORY-5 through
   STORY-11), and mark all as Done.
4. Add the retro as a Confluence page linked from the Jira project (for
   example under "Known Issues & Decisions" or a new "Sprint Retro" page).

## Possible extensions

- Run the next feature as a live sprint, estimating points before the work
  starts and comparing estimate to actual at the retro.
- Track velocity across several sprints to calibrate story points.
