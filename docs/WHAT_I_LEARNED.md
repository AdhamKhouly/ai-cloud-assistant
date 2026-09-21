# What I learned

Personal, but not private. These are the things this project taught me that I did not know when I started, in roughly the order I learned them.

## The LLM was not the hardest part

I originally thought the model would be the hard part, and that most of the work would be prompt writing. For the first week the interesting question was "what should the prompt say?" After that it was "where does this fact live, who is allowed to change it, how do I know it is still true, and what happens when two things disagree?" The model was rarely the bottleneck. The system around it almost always was. An AI assistant is mostly a systems problem once it becomes useful.

## File synchronization is easy until deletion becomes a meaningful event

Copying new files is the easy half. The hard half is what a missing file means. Was it moved? Renamed? Withdrawn? Accidentally dragged into the wrong folder? A sync process that treats every absence as "delete it on the other side" is correct in the narrow sense and dangerous in every other sense.

I also learned that "the file disappeared" is technically an event, which is less funny when the event is your entire project. Backups, version control and a recoverable deployment are not later problems.

## Generated notes should not become their own source

This one sounds obvious written down. It was not obvious while building. Generated notes look like good source material, because they are well organised and on topic. Letting them back into the pool produces notes that drift a little more from the real material with every rebuild.

## State ownership matters surprisingly quickly

I expected this to be a problem for big systems. It showed up with one laptop and one VM. Decide who is allowed to write. Make everyone else read. When I had two writable copies of the same state I spent more time reconciling them than building anything. One writer, one truth, and a clear path for everyone else to submit changes through.

## A cloud VM being always on changes what an assistant can be

Reminders that fire on time, rebuilds that happen overnight, and a bot that answers at 2 a.m. all depend on something being awake. That is the real value of the cloud for a project like this. It is not compute; it is availability.

## Cloud adoption is a sorting exercise, not a migration

I did not move everything to the cloud. I sorted responsibilities by whether they needed to stay awake or needed to be near the files, and put each one where it belonged. The result is a hybrid system that is more capable than either half alone.

## Deterministic routing can be more valuable than another model call

Half the questions I ask have exact answers in structured data. Routing those to code instead of a model made the assistant faster, made its answers predictable, and made the mutations safe, because no model was choosing what to change. It also made the whole thing testable, which I underrated for far too long.

## Confirmations matter when natural language can change data

"Mark those done" is a sentence a person can say carelessly. A system that acts on it carelessly is a liability. Preview what will change, pin exactly those items, ask, then apply once. The extra message is worth it every time.

## Confirmation buttons need the same engineering care as prompts

This one surprised me. I spent a long time on what the model should say and almost no time on what happens when a person presses Confirm twice, or presses the button and also types "confirm", or presses it after the action already happened. Every one of those occurred. A button is an input like any other, and the same questions apply: what exactly does it mean, what state is it acting on, and what happens if it arrives twice?

## Provenance becomes important the moment two sources disagree

Until then, nobody asks where a fact came from. After that, it is the only question. Recording provenance from the start is cheap; adding it after the fact means re-extracting everything.

## Natural language is full of annoying edge cases

My test suite contains sentences I would struggle to explain out of context. Each one is a phrasing that once went to the wrong place: "yes" versus "yes, and also", "mark everything overdue complete" versus "I haven't completed everything overdue", my name versus the name of something in a reading. The tests are unglamorous and they are the reason I can change the router without breaking it.

## Tests became documentation for things I had broken before

I did not start this project intending to write many tests. The private implementation eventually grew past 2,000 of them, and the honest reason is that each strange sentence, each duplicate press, each disappearing file got a test after it broke something once. Reading the test names now is a fairly complete history of what went wrong. This was my first real exposure to tests as a record rather than a chore.

## Once means once, and it is harder than it sounds

Retries, duplicate deliveries, two entry points racing for the same confirmation, a nervous double-click. Every one of these is a way to do a thing twice. Exactly-once is not a property you get; it is one you build at each boundary where a duplicate can arrive.

## Git started feeling much less optional

Version control is not a place to put code. It is the answer to "what was running yesterday?" and "can I get back to it?" Git felt like ceremony while the project was small. It stopped feeling optional the moment I had built something I actually cared about and then learned, the way most people do, that the recommended way is the way you do it beforehand.

## I did not start with the architecture

Nothing about the final shape was planned on day one. It went roughly: Discord bot, then structured tasks, then source ingestion, then living notes, then a cloud control plane, then a local worker, then deterministic routing, then safer state changes, then testing the weird edge cases. Each step was a reaction to the previous one not being enough. I am still learning this, but I now think that order matters more than the final diagram: the diagram only makes sense because of the problems that produced it.

## Small enough to draw

The best architectural property this system has is that I can draw all of it on one page. Every time I was tempted to add a component, I asked whether I could still draw it. Usually the answer was that I did not need the component.

## What I would do differently

- Record provenance from day one.
- Treat deletion as an event from day one.
- Write the deterministic routes before the model loop, not after.
- Set up backups before there is anything worth backing up.
- Keep the confirmation flow narrow and boring. Boring is the point.

## What I am still learning

Distributed state is hard even at the scale of one VM and one laptop. Natural language is spikier than any test suite can fully cover. And there is a version of every rule above that I will discover I got slightly wrong, probably soon. That is fine. This project made ideas like state ownership, routing, cloud workers, and agent harnesses much less abstract, and it did that by making me get them wrong first.
