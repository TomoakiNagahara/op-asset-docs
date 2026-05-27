# CI/CD Concept

## Scope

This document records the intended CI/CD concept of the ONEPIECE Framework.

It describes the framework's own vocabulary and operating model. When this differs from common industry usage, treat the difference as a ONEPIECE Framework terminology boundary.

## ONEPIECE CI/CD Model

In the ONEPIECE Framework, CI and CD are separate concepts.

They are also separate actions:

- CI can be run without CD.
- CD can be controlled separately from CI.
- CI responsibility belongs to `op-unit-ci`.
- CD responsibility belongs to `op-unit-cd`.

The `cicd` command is the integrated entry point for managing both CI and CD for the whole application.

The behavior inside `OP()->Unit()->CI()->Auto()` and `OP()->Unit()->CD()->Auto()` is implementation-dependent.

The current CD unit may deliver through `git push`, but another CD unit may deliver through `rsync`, `ftp`, or another mechanism. Likewise, a different CI unit may inspect code through a different implementation.

The framework-level contract is the entry and exit boundary, not one fixed internal algorithm.

At the concept level:

- `cicd` enters CI through the configured CI unit.
- `cicd` enters CD through the configured CD unit.
- the CI unit must expose the expected CI-side contract.
- the CD unit must expose the expected CD-side contract.
- CI rule input and pass/fail output must remain understandable through framework-defined configuration and inspection contracts.

The ideal flow is:

1. inspect all code through CI
2. confirm that the whole application set is safe
3. deliver only code that passed that full inspection

In short:

- CI guarantees safety by full inspection.
- CD delivers only the code that CI approved.

## Flow Invariant

CD must not start while CI is still running.

In the ONEPIECE Framework CI/CD flow, CD may start only after the CI unit has returned a whole-application success result to `cicd`.

This means:

- passing one submodule is not enough to start CD
- saving one submodule's CI marker is not enough to start CD
- finishing one package type such as UNIT, MODULE, or LAYOUT is not enough to start CD
- CD during an unfinished full CI run is abnormal behavior

The sequencing rule is:

1. CI starts
2. CI inspects the full application set
3. CI returns global success or failure
4. CD starts only if CI returned global success

If an implementation starts delivery before step 3 has completed, that behavior violates the ONEPIECE CI/CD concept.

## CI In ONEPIECE

CI checks all code needed by the application.

Its purpose is to confirm that code behavior is correct and has not changed unexpectedly.

The definition of that guarantee is stored in CI definition files under each package's `ci/` directory.

The current class-oriented CI implementation uses configuration files, method discovery, and test execution rules to define that guarantee. Those mechanics are implementation details of the active CI unit, but the important framework boundary is stable:

- CI must be able to find the inspection definition.
- CI must be able to identify the behavior being inspected.
- CI must be able to execute the inspection.
- CI must return a clear pass/fail result to the `cicd` flow.

CI must inspect all submodules required by the application, regardless of whether they are Git-managed or non-Git-managed. This includes framework package types such as:

- UNIT
- MODULE
- LAYOUT
- other configured submodule packages

A single submodule passing CI is not enough to guarantee the application.

In the ONEPIECE Framework, CI success means the whole application set has passed inspection.

## CD In ONEPIECE

CD delivers only code that has passed CI.

CD must not start only because one submodule passed CI.

The correct CD boundary is the whole application set:

1. all submodules are inspected by CI
2. all required CI checks pass
3. CD begins only after that full success

After the whole application set is approved, CD may deliver each submodule individually.

Delivery may use different mechanisms depending on the package and operation, such as:

- `git push`
- `rsync`
- `ftp`

The important rule is not the transport mechanism. The important rule is that delivery starts only after the whole application set has passed CI.

For that reason, CD documentation should avoid treating `git push` as the definition of CD. `git push` is one current delivery method, not the concept itself.

## Difference From Common CI/CD Usage

Common industry usage often treats CI/CD as a remote pipeline model:

- CI means build and test after commit or pull request
- CD means automated deployment after a pipeline stage succeeds
- a pipeline may run for one repository or one service at a time

The ONEPIECE Framework uses a stricter application-set model.

In ONEPIECE:

- CI is not only a syntax check or isolated package test
- CI is the full inspection of behavior contracts for the whole application set
- CD is not started per package immediately after that package passes
- CD starts only after the whole application set passes
- delivery can still happen package by package after global CI success

This distinction matters because ONEPIECE applications are composed from many loosely coupled packages. A package-level pass does not by itself prove that the combined application set is safe to deliver.

## Relationship To Current Implementation

[DOC-FUTURE] The intended model is that `cicd` controls the whole application CI/CD flow as described here.

Current implementation details may still contain historical behavior or scattered responsibility.

When current behavior differs from this document, treat this document as the To-Be concept and record the implementation mismatch in the responsible As-Is document.

Primary As-Is references:

- `asset/docs/cicd/usage.md`
- `asset/docs/cicd/hooks.md`
- `asset/unit/ci/docs/ci-spec.md`
- `asset/unit/ci/docs/ci-flow.md`
- `asset/unit/cd/docs/isCanPushToGithub.md`
