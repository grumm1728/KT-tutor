# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Status

Greenfield. As of 2026-07-30 the repository is empty — no source, no build tooling, no git history. Remote: https://github.com/grumm1728/KT-tutor

Nothing below describes existing code. It describes the intended shape of the project. Update this file as real structure lands, and delete this Status section once the repo has a working build.

## What this project is

A **demonstration** of an LLM-powered knowledge-tracing tutor. It is a visualization/teaching artifact, not a production tutoring system. Two panes, side by side on one page:

- **Left — the student's view.** A mocked-up tutor chat/exercise UI rendered inside a phone outline. This is what a learner would see.
- **Right — the model's view.** A live visualization of the backend knowledge-tracing state: per-skill mastery estimates and how each student response updates them.

The user drives a simulation through this page — stepping through a sequence of interactions and watching the belief state on the right move in response to what happens on the left. **The coupling between the two panes is the whole point of the project.** Any change that lets the phone UI advance without the KT panel visibly reflecting why has broken the demo, even if both panes still render.

## Design constraints that follow from that

- **Every state update must be inspectable.** Prefer showing the intermediate quantities (prior, evidence, posterior) over showing only the final mastery number. A black-box update defeats the purpose.
- **The simulation must be replayable and deterministic.** Given the same interaction sequence, the same KT trajectory. If an LLM call is in the loop, its output must be capturable and replayable from a fixture so the demo does not depend on live inference or a network.
- **The KT model is domain logic, not UI code.** Keep it in its own module with a pure `update(state, observation) -> state` shape and no rendering imports. The right-hand pane should be a view over that module's output, and it should be possible to run the model headlessly over a dataset without touching the frontend.

## Pending inputs (not yet in the repo)

Scott is supplying a research paper and an accompanying dataset from its authors. These define the actual KT model and its parameters. Until they arrive:

- Do not invent a KT formulation and present it as "the" model. If an interim model is needed to build UI against, use standard BKT (with named, obvious parameters) and label it clearly as a placeholder to be swapped.
- When the paper lands, record in this file: which model it specifies, what the parameters mean, and the shape of the dataset.

## Stack

Undecided. Choose with Scott before scaffolding — the demo needs to be shareable in a browser, which is the main constraint. Once chosen, replace this section with the actual install / dev-server / test / lint commands, including how to run a single test.

## Conventions

- The term "knowledge tracing" refers to the backend belief model over student skill mastery. Use "KT state" for that belief, "observation" for a scored student response, and "skill" (not "KC" or "concept") for the unit being traced, unless the incoming paper uses different terminology — if it does, adopt the paper's vocabulary throughout and note the mapping here.
