# Privacy

This repository is a public architecture report. It was written to be shared, and it was written with the assumption that anything in it will be read by strangers. The following were deliberately left out, and their absence is a feature of this repository rather than an omission.

## Not included

**Private source material.** No slides, readings, transcripts, syllabi, recordings, or any other academic content. The project processes that kind of material; this repository does not contain any of it, and the categories named in the docs are generic.

**User data.** No tasks, deadlines, notes, schedules, generated study material, conversation history, or anything derived from a real person's use of the assistant. The examples in the docs are illustrative phrasings, not records.

**Credentials.** No API keys, bot tokens, secrets files, environment files, or anything that grants access to anything.

**Network configuration.** No addresses of any kind, no hostnames, no private network details, no port numbers, no SSH material or key names, and no account or identity information for the cloud provider or the chat platform.

**Production database.** Not the database, not a snapshot of it, not its schema as deployed, and no identifiers from it. Where the docs mention identifiers conceptually ("pinned ids") no real values appear.

**Detailed deployment configuration.** No service unit files, launch agents, deploy scripts, or the paths, users and permissions they encode. The deployment is described at the level of "what it does and why", not "how to run it".

**Production source code.** This repository contains documentation and diagrams. The pseudocode blocks are conceptual and were written for this report. No functions, modules, prompts or tests were copied from the working system.

**Personal identifiers.** No names, initials, institutions, programmes, identifiers, addresses, usernames, or local file paths.

## What is included

Original prose describing the architecture and the decisions behind it, and original SVG diagrams drawn for this report. That is all.

## Why the line is drawn here

The interesting part of this project is its shape: what owns state, what stays awake, how a message becomes a typed intent, how notes follow their sources, how deadlines are resolved from disagreeing evidence. None of that requires a single private detail to explain. Everything that would require one has been left out.

If you are building something similar and want to compare notes on the design, the docs are meant to be enough to have that conversation.
