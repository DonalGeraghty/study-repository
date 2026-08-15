# Professional and Interpersonal Skills

Professional skills are observable behaviours that help people make decisions, collaborate, deliver outcomes, and learn. They are not vague personality traits. Communication, judgement, influence, and accountability can be practised and demonstrated with evidence.

For an SDET, these skills are especially important because quality work crosses engineering, product, operations, security, and business boundaries. Finding a risk is only useful if it is explained clearly and helps the team act.

## Competency Map

| Area | Competencies | Evidence to look for |
| --- | --- | --- |
| People excellence | Candid communication, inclusion, collaboration | Shared understanding, constructive feedback, participation, resolved disagreement |
| Strategic excellence | Problem solving, creativity, change leadership | Root-cause analysis, options considered, sound decisions, adoption |
| Operational excellence | Accountability, execution, adaptability | Clear ownership, prioritisation, measurable delivery, learning |
| Technical leadership | Influence, mentoring, quality advocacy | Better decisions, stronger systems, increased team capability |

Seniority changes the scope, not the need for these behaviours. A junior engineer may improve one test or conversation; a senior engineer may align several teams around a quality strategy.

## People Excellence

### Candid Communication

Clear communication makes the important information easy for the audience to understand and act upon.

Useful behaviours include:

- stating the purpose and required decision early;
- separating facts, assumptions, risks, and opinions;
- adapting technical depth to the audience;
- making uncertainty explicit;
- presenting options and a recommendation;
- confirming owners, actions, and deadlines;
- raising concerns early and respectfully.

### Communicating Quality Risk

A concise risk statement answers:

1. What could fail?
2. Who or what would be affected?
3. What evidence supports the concern?
4. How likely and severe is it?
5. Which options are available?
6. What do you recommend?

For example:

> Payment retries can create duplicate charges because the operation lacks an idempotency key. The integration test reproduced this twice under a timeout. I recommend blocking release until the API accepts an idempotency key; otherwise we should disable automatic retry and document the residual risk.

This is more actionable than “the payment tests are failing.”

### Explaining Technical Concepts

When speaking to a non-specialist:

- begin with the user or business consequence;
- use one concrete example;
- avoid unexplained abbreviations;
- compare alternatives using cost, risk, and outcome;
- check understanding rather than asking only “Does that make sense?”

When speaking to engineers, include enough evidence to reproduce or challenge the conclusion.

### Inclusive Collaboration

Inclusion means designing participation so that relevant perspectives can influence the outcome.

- Share context and pre-reading before a decision.
- Invite input from people closest to the problem.
- Make space for quieter or remote participants.
- Attribute ideas and avoid interrupting.
- Explain how feedback affected the decision.
- Record decisions for people who could not attend.
- Challenge exclusionary language or behaviour safely and specifically.
- Consider accessibility, time zones, language, and communication preferences.

In an interview, describe what you actually changed about participation or the solution—not simply that the team was diverse.

### Feedback

Give feedback using specific observations:

1. Describe the situation.
2. State the observable behaviour without assigning motive.
3. Explain its impact.
4. Ask for the other person's perspective.
5. Agree on a concrete next step.

Receive feedback by listening fully, asking for an example, acknowledging impact, and deciding what to change. Agreement with every interpretation is not required, but curiosity is.

## Strategic Excellence

### Problem Solving

Strong problem solving is a visible process:

1. Define the problem and its impact.
2. Gather evidence and reproduce it where possible.
3. Separate symptoms from possible causes.
4. Form and test hypotheses.
5. Compare options and constraints.
6. Implement the smallest safe intervention.
7. Measure the outcome and watch for side effects.
8. Share what was learned.

For flaky tests, repeatedly rerunning the suite is not root-cause analysis. Examine failure signatures, timing, test data, isolation, dependencies, environment resources, and application behaviour.

### Decision Making

A useful decision record contains:

- context and decision owner;
- constraints and success criteria;
- options considered;
- evidence and assumptions;
- trade-offs and risks;
- the decision and review date.

Not every decision needs a formal document. Record choices that are expensive to reverse, affect several teams, or will otherwise lose important context.

### Creativity

Creativity is not novelty for its own sake. It can mean:

- reframing the problem;
- combining existing ideas;
- simplifying a process;
- running a low-cost experiment;
- borrowing a useful practice from another domain;
- removing work rather than automating it.

Explain how ideas were generated, evaluated, tested, and refined. A creative solution that nobody can maintain is not necessarily a successful one.

### Change Leadership

Technical correctness does not guarantee adoption. To introduce a new framework, quality gate, or workflow:

1. Understand the affected groups and their incentives.
2. Describe the problem with evidence.
3. Involve users in shaping the approach.
4. Pilot the change at limited scope.
5. Provide documentation, training, and migration support.
6. Measure both outcome and adoption.
7. Respond to valid resistance and adjust.
8. Remove the old path when the new one is proven.

Resistance may reveal missing requirements, operational cost, or previous failed changes. Treat it as information rather than disloyalty.

## Operational Excellence

### Accountability

Accountability means taking responsibility for the outcome and making progress visible. It does not mean doing all the work alone or hiding problems.

- Clarify the result, constraints, owner, and definition of done.
- Break uncertain work into testable milestones.
- Surface risks and dependencies early.
- Ask for help before a deadline becomes unrecoverable.
- Close loops with stakeholders.
- Own mistakes without shifting blame.
- Follow through on corrective actions.

### Execution and Prioritisation

Prioritisation should reflect value, risk, urgency, effort, and dependencies.

For an SDET, this may mean:

- protecting critical pull-request feedback before expanding browser coverage;
- fixing test isolation before adding parallel workers;
- testing a high-impact failure path before automating many low-risk variations;
- reducing diagnosis time instead of producing a more decorative report;
- declining work that does not support the agreed outcome.

When priorities conflict, state what will be delayed and the consequence. A priority list in which everything is critical is not a decision.

### Adaptability

Adaptability combines learning with controlled change:

- notice when assumptions no longer hold;
- seek new evidence;
- preserve the goal while changing the approach;
- communicate the reason and impact;
- avoid changing direction with every new opinion;
- capture the lesson for future work.

A strong setback story includes the mistake or failed assumption, not merely an external obstacle.

## Technical Leadership Without Authority

Leadership is an action, not only a title. Influence grows from credibility, relationships, evidence, and making change easier.

- Understand other people's goals before proposing a solution.
- Demonstrate a small working improvement.
- Invite critique and share ownership.
- Create templates, examples, and migration paths.
- Mentor through questions and pairing.
- Publicly recognise contributors.
- Measure outcomes rather than claiming adoption.

Escalation is appropriate when risk exceeds the team's authority, a decision is blocked, or safety and ethics are involved. Escalate the issue with evidence and attempted resolutions, not an attack on a person.

## Handling Disagreement

1. Restate the shared objective.
2. Confirm each person's position and underlying concern.
3. Separate evidence from assumptions and preferences.
4. Identify constraints and decision authority.
5. Compare options using agreed criteria.
6. Run an experiment when uncertainty can be reduced cheaply.
7. Record the decision and commit to it unless new evidence appears.

Disagreement can improve a decision. Personal attacks, hidden vetoes, and endless consensus-seeking do not.

## Meetings and Written Communication

### Before a Meeting

- Define the purpose: inform, explore, decide, or plan.
- Invite only required participants.
- Share context and questions in advance.
- Identify the decision owner.

### During a Meeting

- Restate the outcome and time boundary.
- Distinguish discussion from decisions.
- Invite relevant perspectives.
- Park unrelated issues.
- Summarise agreements and unresolved risks.

### After a Meeting

- Record decisions, owners, and dates.
- Share notes with affected people.
- Track actions to completion.

Prefer a short written update when no discussion or decision is needed.

## Building an Interview Story Bank

Do not prepare a separate story for every possible wording. Build six to eight strong examples that can each demonstrate several competencies.

Useful SDET scenarios include:

| Scenario | Possible competencies |
| --- | --- |
| Diagnosed and fixed a flaky suite | Problem solving, ownership, technical depth |
| Reduced execution or diagnosis time | Execution, simplification, measurable impact |
| Challenged an unsafe release decision | Candid communication, risk judgement, influence |
| Introduced a test or delivery practice | Change leadership, collaboration, adaptability |
| Resolved disagreement about test scope | Communication, prioritisation, stakeholder management |
| Made a mistake or backed a poor approach | Accountability, learning, adaptability |
| Mentored or enabled another engineer | Inclusion, leadership, communication |
| Worked through an incident | Calm execution, collaboration, systems thinking |

Avoid using confidential customer data, credentials, unpublished financial information, or blame-focused descriptions.

## STAR-L Answer Structure

STAR-L adds learning to the common Situation, Task, Action, Result structure.

### Situation

Give only the context needed to understand the problem, scale, and stakes.

### Task

State your responsibility, constraints, and intended outcome. Distinguish your ownership from the team's.

### Action

Spend most of the answer here. Explain:

- what you personally did;
- why you chose that approach;
- how you collaborated or influenced;
- what alternatives or risks you considered;
- how you responded when the plan changed.

### Result

Describe the outcome with evidence. Useful measures include time saved, defect reduction, reliability, adoption, customer impact, cost, or risk reduced.

If the result was mixed, say so. Explain what improved and what remained unresolved.

### Learning

State what you learned and what you would repeat or change. This is essential for setback, conflict, and adaptability questions.

## Story Worksheet

```text
Title:
Competencies demonstrated:

Situation:
- Context, scale, and stakes:

Task:
- My responsibility:
- Constraints and success measure:

Action:
- Evidence I gathered:
- Decision and reasoning:
- Collaboration or disagreement:
- Specific actions I took:

Result:
- Outcome and measurement:
- Effect on users, team, or business:

Learning:
- What I would repeat:
- What I would change:

Likely follow-up questions:
```

## High-Value Interview Questions

### People Excellence

- Tell me about a time you explained a complex technical risk to a non-technical audience.
- Tell me about a disagreement where you changed your view or helped the group reach a better decision.
- Describe how you made a project or decision more inclusive.
- Tell me about difficult feedback you gave or received.

### Strategic Excellence

- Tell me about a problem where the initial symptom was not the root cause.
- Describe a decision you made with incomplete information.
- Tell me about a process you simplified or work you eliminated.
- Describe a change you introduced that initially met resistance.

### Operational Excellence

- Tell me about a commitment that became at risk and how you handled it.
- Describe how you prioritised competing quality risks.
- Tell me about a failure or poor decision for which you were accountable.
- Describe a project where new evidence forced you to change approach.

### Technical Leadership

- Tell me about a technical improvement you achieved without formal authority.
- Describe how you raised another engineer's or team's capability.
- Tell me about a time you challenged a release or design decision.
- Describe an incident where you helped people reach a reliable diagnosis.

## Follow-up Questions to Prepare For

Interviewers often probe beyond the rehearsed answer:

- What was your personal contribution?
- What alternatives did you consider?
- Who disagreed, and why?
- How did you measure the result?
- What would the other person say?
- What went wrong?
- What would you do differently?
- How did the change last after you left?

If a story cannot answer these questions, gather more detail or choose a stronger example.

## Answer Quality Rubric

Score each area from 1 to 3:

| Area | 1 | 2 | 3 |
| --- | --- | --- | --- |
| Relevance | Does not answer the question | Partially relevant | Directly demonstrates the competency |
| Ownership | Contribution is unclear | Some personal action | Personal responsibility and collaboration are clear |
| Reasoning | Lists actions only | Gives limited rationale | Explains evidence, alternatives, and trade-offs |
| Result | Vague claim | Concrete but unmeasured | Measured outcome and wider impact |
| Reflection | No learning | Generic learning | Specific change in later behaviour |
| Delivery | Rambling or overly brief | Mostly clear | Concise, natural, and easy to follow |

Use the rubric to improve the story, not to memorise a script.

## Practice Method

1. Write short bullet points rather than a speech.
2. Practise a two-minute version.
3. Answer random follow-up questions.
4. Record yourself and remove unnecessary setup.
5. Check that “I” and “we” accurately represent ownership.
6. Ask a reviewer whether the competency and result are obvious.
7. Practise adapting one story to different questions without forcing it.

## Common Mistakes

- Describing soft skills as traits without an example.
- Spending most of the answer on background.
- Saying only what “we” did when personal contribution is unclear.
- Presenting yourself as the hero and colleagues as obstacles.
- Claiming success without evidence or adoption.
- Avoiding genuine mistakes in a failure story.
- Giving a technical monologue for a collaboration question.
- Treating influence as persuasion rather than listening and adaptation.
- Memorising wording so tightly that follow-up questions cause difficulty.
- Reusing one story for every question regardless of fit.

## Completion Checklist

- [ ] I have six to eight stories covering different competencies.
- [ ] Each story makes my responsibility and actions clear.
- [ ] Results use evidence where available.
- [ ] At least one story covers conflict, failure, inclusion, and change.
- [ ] I can explain what I learned.
- [ ] I can adapt technical detail to the audience.
- [ ] My examples protect confidential information.
- [ ] I can answer unplanned follow-up questions.
- [ ] My day-to-day behaviours match the claims in my answers.

## Related Guides

- [SDET Career Playbook and Interview Roadmap](./sdet-career-playbook.md)
- [Interview Story Workbook](./interview-questions.md)
- [Code Review](../engineering-foundations/code-review.md)
- [Testing](../quality-engineering/testing.md)
- [Technology Stack](../engineering-foundations/technology-stack.md)

Return to [Career](./README.md).
