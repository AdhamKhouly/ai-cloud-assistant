# Design decisions

The rules the project ended up following, with what each one cost and why I kept it anyway. Most of these were not decided up front. They were the result of something going wrong in a way I did not want to happen again.

## 1. One canonical state, one writer

**Decision.** There is exactly one writable store of tasks, deadlines and source records, and it lives on the cloud control plane. The local worker and the sync process read from it and submit work to it; neither keeps its own writable copy.

**Why.** For a short while I had two databases that were supposed to be the same. They were not, and I was the reconciliation process. Removing the second writer removed the job.

**Cost.** The local worker cannot do anything useful if the cloud is unreachable. I accepted that: a degraded assistant that tells one story beats two assistants telling two.

## 2. Deterministic first, model when useful

**Decision.** Every message is interpreted into a typed intent first. If a deterministic route exists for that intent, it runs and the model is not consulted. The model is used for synthesis, explanation and the rare interpretation that the deterministic layer cannot make.

**Why.** Deadline questions have known answers in structured state. Asking a model to guess them is slower, less reliable and harder to test. Deterministic routes are also where safety lives: a mutation whose arguments were chosen by a model is a mutation I cannot reason about.

**Cost.** A lot of small regression tests. Natural language is spiky, and every phrasing that should take a deterministic route needs a case that proves it does.

## 3. Reading is not writing

**Decision.** Read paths answer freely. Anything that changes state goes through resolve, preview, confirm, apply. A change proposed from inside the model loop is refused and handed back to the typed path.

**Why.** The first time a scripted model answered "tidy up my task list" by completing three tasks with no preview, I understood why this needed to be a rule rather than a habit.

## 4. Pin before confirming

**Decision.** When a request selects a set ("all overdue tasks"), the set is resolved at preview time and its identifiers are pinned into the pending action. Confirming applies the pinned set, never a fresh evaluation of the same words.

**Why.** Between preview and confirmation, state can change. The user confirmed what they saw.

## 5. Once means once

**Decision.** Every applied change is claimed under a unique token before it runs. Inbound messages, worker job leases, confirmations and sync batches each have their own exactly-once boundary.

**Why.** I learned that "run this once" sounds easy until retries, duplicate deliveries, and confirmation buttons get involved. The live case was a Confirm button and a typed "confirm" five seconds apart, applied twice. Now the second one is recognised and answered with the truth about the first.

## 6. Generated output never becomes input

**Decision.** Weekly notes, quizzes and summaries are artifacts with versions and provenance. They are never registered as sources, never retrieved as evidence, and never used to build the next version of themselves.

**Why.** Summaries of summaries drift. The source pool is the original material only.

## 7. Deletion is an event

**Decision.** A source disappearing is recorded as a soft delete with history, not as an absence. Dependents are flagged, the affected week goes stale, and a returning file reactivates the same identity.

**Why.** An absence is easy to apply without thinking. I applied one without thinking. Treating it as data made the next one survivable.

## 8. Provenance everywhere

**Decision.** Extracted facts point at source records. Artifacts record the sources and hashes they were built from. Answers cite what they drew on.

**Why.** The moment two sources disagreed, "where did this come from?" became the only question that mattered, and I could not answer it.

## 9. A date with no time is a date with no time

**Decision.** A deadline known only by date is stored and displayed as date-only. An end-of-day placeholder exists for ordering and is never shown, notified or reasoned about as a real time.

**Why.** An invented 11:59 PM looks exactly like a real one. The user cannot tell which is which, so the system must not produce the fake one.

## 10. Say what you did not do

**Decision.** When the assistant cannot resolve something, it says so, in specific terms: which part of the request it could not resolve, and that nothing was changed. When evidence does not cover a question, it says the evidence does not cover it.

**Why.** A confident wrong answer costs more than an honest "I could not find that." This applies to state too: the assistant never claims a task is open or closed unless it has just read that from the store.

## 11. Version pins between cloud and worker

**Decision.** The worker advertises the code version it loaded; jobs are pinned to a version; a deploy restarts every active service and the worker, then checks they agree.

**Why.** Running processes do not notice files changing under them. An old worker once answered for hours after a deploy that every log said had succeeded.

## 12. Keep it small enough to draw

**Decision.** One database, one router, one worker, one sync process, one deploy script. No orchestration, no queue service, no service mesh.

**Why.** I am one person learning this. Being able to draw the whole system on one page has been worth more than any individual capability I gave up.
