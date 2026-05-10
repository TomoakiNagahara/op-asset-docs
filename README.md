# asset/docs

This repository contains documentation written for AI-oriented consumption.

The documents in this repository are intended to help AI agents understand the design, behavior, workflow, and operational rules of the ONEPIECE Framework and related repositories.

Because many of these documents are written or expanded by AI, they may still contain mistakes, gaps, or wording that does not perfectly match the intended framework design.

They should be treated as useful working documents, but also as documents that may require human review and correction.

Japanese translations of framework-level documents are stored next to the English file and use the suffix:

- `.ja.md`

Useful entry points include:

- `important-gaps.md`
- `to-be.md`

## About As-Is and To-Be

As-Is and To-Be should not be mixed carelessly in one place.

The practical guideline is:

- local technical documents should primarily describe As-Is
- local documents may still keep short `[DOC-FUTURE]` notes when necessary
- broader future direction should also be collected in `to-be.md`

The reason is that As-Is and To-Be answer different questions:

- As-Is: what the framework does now
- To-Be: what the framework should become later

Keeping a separate To-Be document makes it easier for both humans and AI to tell:

- what is already true
- what is only planned
- what is a current bug or gap
- what is a future design direction
