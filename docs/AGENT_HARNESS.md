# What I mean by an agent harness

When I started, "AI agent" meant something like this to me:

```text
prompt + model + tools = agent
```

Give a model a good prompt, hand it some tools, let it loop. That is not wrong, exactly. It is just the smallest possible description, and it leaves out most of the work.

The harness is my name for everything around the model that decides when it runs, what it sees, what it may touch, and what happens to what it says. An analogy that helped me: the LLM is the reasoning engine, but the harness is the part that gives it rules, tools, memory boundaries, and somewhere safe to operate. A very capable engine with no chassis is still not a car.

## What the harness does in this project

**Intent routing.** Every message is interpreted into one typed intent before anything else happens. The interpretation is deterministic where it can be, and it is allowed to say "I am not sure" and escalate to one narrowly scoped model call whose only job is to fill in the structure, never to answer.

**Deterministic shortcuts.** Deadline reads, day-status checks, task completions, conversational pings and a few other shapes never reach the model. They are resolved from structured state and formatted by code. The model is not consulted about things the database already knows.

**Tool selection.** When the model does run, it chooses from a small set of tools with declared read/write behaviour. Read tools run freely. Write tools are refused when the request originated inside the model loop; a change has to come through the typed path with a preview and a confirmation.

**Evidence retrieval.** Knowledge questions retrieve from registered sources first, with the course, the week and the source type narrowing the search. The model is given evidence and asked to answer from it, and it is told when the evidence does not cover the question so it can say so instead of improvising.

**Structured state.** Tasks, deadlines, sources and weekly build state live in one canonical store the model cannot write to directly. Anything it proposes is handed back to the typed architecture to be resolved, previewed and confirmed.

**Confirmation before mutation.** Described in the README and in the design decisions. Preview, pin, confirm, apply once.

**Logging and model-call boundaries.** Every turn logs which route it took, how many model calls it made, and how long each part took. A turn that should be deterministic and shows one model call is a bug, and the log is how I find it. A shadow record compares what the typed intent expected with the route that actually ran, so a disagreement between the two shows up as data rather than as a vague feeling that something was off.

**Source provenance.** Extracted facts point at the source record they came from. When a source changes, the harness knows which facts and which weekly artifacts are now suspect.

**Worker coordination.** Jobs are leased to one worker at a time with a version pin, so two workers cannot claim the same job and an out-of-date worker cannot claim any.

**Version checks.** The cloud services and the local worker each advertise the code version they actually loaded. A deploy restarts what changed and reconciles until they agree.

## Why this changed how I think about agents

The model improved over the life of this project without me doing anything. Newer versions arrived; they were better. The harness only improved because I kept finding, one strange answer at a time, what was missing around the model:

- the assistant that answered a greeting with a course-material search, because a nickname reached the knowledge route as a topic;
- the assistant that treated "I did not finish X" as an instruction to change X, because a negation reached a mutation path;
- the assistant that applied one confirmation twice, because a button and a typed word arrived five seconds apart.

None of those were model problems. They were harness problems, and fixing them meant adding structure, not adding prompt text.

So my working definition now is: an agent is a model plus the system that makes the model safe to be useful. The second half is where most of the engineering is, and it is the half I did not know existed when I started.

## A small example

```text
"mark my overdue tasks complete"

  interpret   -> action: bulk complete, quantifier: all, filter: overdue
  route       -> deterministic set completion (no model)
  resolve     -> the open tasks whose deadline is in the past, for this user
  preview     -> list them, pin their ids, ask for confirmation
  confirm     -> claim the token, apply once, record what was applied
  reply       -> what changed, in one message
```

Zero model calls. Every step is testable in isolation. That is the harness doing its job.
