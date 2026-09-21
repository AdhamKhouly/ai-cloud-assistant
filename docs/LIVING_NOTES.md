# Living weekly notes

Most note-generation tools I had seen worked like a photocopier: feed the material in once, get a summary out, done. The summary is frozen at the moment it was made. My material was not frozen. Slides got re-uploaded with corrections, transcripts arrived days after the class, readings appeared late, and occasionally something was withdrawn.

So the notes in this project are rebuilt whenever the material for a week changes. I call them living notes because they follow the sources.

![Living notes loop](../assets/living-notes-loop.svg)

## The loop

```text
source added / changed / removed
  -> registry updates the source record
  -> the affected week is marked stale
  -> scheduler picks up the stale week
  -> weekly synthesis runs from the CURRENT set of sources
  -> a new version of the notes is stored, the old one kept
```

Concretely: a week starts with slides and a transcript, so the first notes are built from those two. A reading appears later; the registry notices, the week goes stale, and the next rebuild uses slides plus transcript plus reading. If the reading is later removed, the next rebuild no longer uses it. Nothing has to be re-run by hand and nothing silently keeps using a source that is gone.

## What counts as a source

Generic categories of eligible material: slides, transcripts, readings, cases, notes handed out in class, glossaries, and locally transcribed recordings. Each is registered with a category, a week where one can be determined, a content hash, and a presence flag. Material that cannot be placed in a week is registered as course-wide and marked for review rather than guessed into a week.

## What does not count as a source

The generated notes themselves. This turned out to be the most important rule in the whole feature.

Generated study material (weekly notes, practice quizzes, summaries) is stored as an *artifact* with a version number and a record of which sources it was built from. It is output. It never re-enters the source pool, it is never retrieved as evidence for a knowledge question, and it never contributes to the next rebuild. Without that rule the assistant would eventually be summarising its own summaries, and each generation would drift a little further from the material. The diagram marks this as "generated notes != source truth" and I mean it literally.

## Provenance

Every artifact version records the set of sources it was built from and their hashes at the time. That gives me two things:

- when a source changes I know exactly which weeks to mark stale, rather than rebuilding everything;
- when notes look wrong I can see which version of which source produced them.

Extracted content keeps a pointer back to its source record too, and answers to knowledge questions cite the source they drew on. Material changes later; the assistant needs to know what it knew and where it learned it.

## Rendering

The canonical content of an artifact is stored as text in the state. Document renderings (a formatted notes file, a printable quiz) are generated from that content and tied to the artifact version, so a rendering can never be newer than the notes it renders. Charts inside notes are only produced from data that actually appears in the sources; the assistant does not draw a graph to look helpful.

## Things I got wrong first

- **Rebuilding everything on every change.** Cheap when there were three sources. Not cheap by week six. Marking only the affected week stale was the fix.
- **Letting notes cite notes.** See above. It read fine for a while, then it did not.
- **Trusting the filename for the week.** Filenames lie, or at least improvise. Week assignment now comes from the registry's classification, which can be corrected once and stays corrected.
- **Treating a removed source as "nothing to do."** A removed source means the week that used it is stale. Absence is a change.

## Why I like this feature

It is the part of the project that feels least like a chatbot and most like an assistant. I do not ask it to make notes; it keeps the notes true to whatever material exists right now, and I ask it questions about the week. The chat is the interface. The loop is the product.
