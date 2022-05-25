<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [Summary](#summary)
- [Motivation](#motivation)
- [The problem in detail](#the-problem-in-detail)
- [Scope](#scope)
- [Specification / Discussion of conventions](#specification--discussion-of-conventions)
    - [What is time zero? ...and before time began](#what-is-time-zero-and-before-time-began)
    - [Coefficient initialization (filter UGens, etc.)](#coefficient-initialization-filter-ugens-etc)
    - [Triggered UGens](#triggered-ugens)
    - [Signal generating UGens](#signal-generating-ugens)
    - [Exceptions](#exceptions)
- [PR strategy for fixing batches of UGens](#pr-strategy-for-fixing-batches-of-ugens)
  - [Along the way](#along-the-way)
- [Affected Issues and PRs (WIP)](#affected-issues-and-prs-wip)
    - [Related Issues](#related-issues)
    - [Related PRs](#related-prs)
    - [Related discussions](#related-discussions)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

-  Title: Fix UGen Initializations
- Date proposed: 2022-05-25
- RFC PR: https://github.com/supercollider/rfcs/pull/19

# Summary

A majority of UGens do not initialize with a correct _initialization_ sample, and subsequently output an incorrect _first_ sample. This RFC proposes that this be corrected for a substantial batch of core UGens, and to document the discussion for reference as the effort moves on to cover more of the UGen source files.

Work is underway for `OscUGens`, `FilterUGens`, and `LFUGens`, including a PR already submitted for [OscUGens](https://github.com/supercollider/supercollider/pull/5787) to serve as a concrete reference to this discussion. I chose to use `OscUGens` as the starting point because I expect these changes are minor (likely won't be heard), and are hopefully not overly controversial.

Changes to UGens which are more elaborate or might create more substantial change in behavior will be left out of these kind of "batch" fixes, and submitted as separate PRs.
Progress on the above three source files is also [tracked here](https://github.com/mtmccrea/supercollider/wiki/UGen-Fix-List).

# Motivation

A lack of clarity and consistency around how UGens are initialized has led to many UGens being improperly initialized. The affects include:
- UGen graphs aren't properly "primed" (users see unexpected results),
- samples are delayed,
- filters can pop from incorrect feedback coefficients,
- accessing unassigned memory, etc.

Users regularly report Issues that can be traced back to UGen initialization problems. They range in complexity, but clarifying some core concepts around initialization leads to a formulaic approach to fixing many UGens.


# The problem in detail

To clarify terms: The *initializations sample* (or "*init sample*"), is not the same as the *first sample* that is output by the UGen. When the synth graph is assembled, the UGens are interconnected through shared wirebufs. Each UGen is initialized by reading in one sample from its input buffers. Using those inputs, it either a) runs its calculation function for one cycle to generate the first output value, or b) directly calculates its first output value in the constructor. This single _init sample_ is written to its output buffer which is in turn consumed by downstream UGens for their initialization. Many Ugens depend on knowing their first input values to determine their state. Once the synth is played, the first sample written out by each UGen _needs to be the same as the init sample_, if possible, so that at time zero the UGen graph matches its initial state.

Many UGens incorrectly set the init sample to `0`. Others calculate the init sample correctly, and in doing so advance the state of the ugen's internal parameters in time, forgetting to reset its state back to time zero, so the first sample that is output is what should be the *second* sample, or the state is muddled in some other way. Relatedly, Many UGens that have state depending on previous outputs (e.g. IIR filter coefficients), are also set in an ad hoc way or not reset to represent the correct state at time zero. This is important if you're looking to do sample accurate work and expecting precise behavior (filter design, precise triggering, control systems, research more generally).

# Scope

Ideally, all UGens would be checked and corrected for proper initialization, but that's a very large undertaking.

The author of this RFC has initiated work on three groups of core UGens, those in `OscUGens`, `FilterUGens`, and `LFUGens` (please reach out if you've also done work in this scope on these source files). By first fixing 3 groups of UGens, it makes solid headway, and forms a blueprint for correcting more in the future. A [checklist of source files/UGens that have been addressed](https://github.com/mtmccrea/supercollider/wiki/UGen-Fix-List), to minimize duplicate work. Contributors can scale the scope of their contribution—even one source file of UGens can be a lot of work, fixing a batch of a handful of UGens in PR is still helpful progress.

Aside from the actual work of fixing UGen initialization, this purpose of this RFC is to also document the process and rationale for how UGens are initialized. The outcomes could be formalized in a wiki and/or Help docs. The following discussion would form the kernel of such docs...

# Specification / Discussion of conventions

### What is time zero? ...and before time began
Conceptually, what should we expect happened before a UGen starts? The working model here is: *silence* — assume input that preceded the first sample was zeros.

### Coefficient initialization (filter UGens, etc.)
A common mistake is that previous samples held in a UGen's state (often called `x1`, `y1`, etc.), are simply set to the _current_ input value, effectively creating repeated input samples (a short burst of DC offset) as your initial input. This doesn't make conceptual sense, and for filters this can mean a nasty "warm up" period. This proposal considers those problems within the scope of the "UGen initialization problem".

For UGen state coefficients that result from the feedback of a UGen's output, their state would be determined by how the UGen processed the preceding inputs, which by this model are zeros.

One view is that it's best to expose these initial states for the user to set (with well motivated, documented defaults otherwise). This is a good convention for new UGens, IMO. But for most existing UGens exposing these coefficients to users means appending them to the existing argument list. Unfortunately, an old convention was to have `mul` and `add` arguments at the tail of the argument list, so exposing these coefficients means either they sit in odd positions at the tail of the argument list (could be ok if they're infrequently used?), or they're inserted before `mul/add` args, which is a breaking change, and unlikely to happen for historical reasons... maybe in SC 4 ;).

### Triggered UGens
Some UGens force a trigger on initialization regardless of the input values, or forget to reset the state causing the first-sample trigger is missed upon running.

The proposed behavior is that any inputs that respond to triggers are initialized to a `false` (or `< 0`) state, so they're ready to detect a trigger input on the first sample.
If they are triggered during initialization, _the state should be reset_ to so that it's re-triggered upon running.

### Signal generating UGens
UGens that generate signals (E.g. `PureUGens`) may not have obvious initial state, but others will. For example, it's reasonable to expect that the first sample of a `SinOsc` will be `0` (assuming an initital phase of zero). Currently it sets its init sample to `0`, but doesn't reset it's state and outputs some positive non-zero value for its first sample. This proposal considers that a bug.

### Exceptions
There are reasons for UGens to deviate from the behavior above. Assuming the conventions are agreed on, _exceptions should be documented_, ideally in the source code as well as in Help documentation for the UGen.

Some UGens are less clear and would require some discussion before changing behavior. For example, `Impulse`: it's a fair, but maybe not obvious assumption that the init/first sample should be an impulse, as oppose to waiting a full period before sending out its first impulse. This bug is mixed in with others, and given its wide use, warrants individual discussion (as opposed to being grouped into a batch of other minor fixes in this project).


# PR strategy for fixing batches of UGens
Reviewers are encouraged to comment on the approach here...

  - The current thinking is to group the minor, non-controversial changes, into a single PR per UGen source file.
  - One commit per `UGen` fix. Identical or "related" fixes could be bundled into a single commit if easily separated and reasoned through.
  - Exclude from the batch PR UGens that require more elaborate fixes, large behavior changes, or judgement calls that could use more information/discussion. Submit these as separate PRs, cross-referenced to other relevant PRs.
    - The hope is that discussion around one change wouldn't hold up the whole batch!

## Along the way
Try to look back through SC Issues and PRs to see if any are fixed or impacted by each PR.

You'll find more bugs as you do this work, almost certainly. File Issues for bugs that are out of the present scope.

You'll also find gaps in documentation. If it's not clear why something works the way it does, chances are there are others that don't know either. Try to update docs (or source) along the way while your head is in that space :)


# Affected Issues and PRs (WIP)

### Related Issues
- [Initial state of many UGens is not sample accurate. #4127](https://github.com/supercollider/supercollider/issues/4127)
- [server plugins: Some UGens fail to calculate one sample of output at Ctor #2333](https://github.com/supercollider/supercollider/issues/2333)
- [UGens init to unexpected values #2343](https://github.com/supercollider/supercollider/issues/2343)
- [Line, XLine: Incorrect starting value, early arrival, etc. #4279](https://github.com/supercollider/supercollider/issues/4279)
- [Delay2: first output sample is garbage #4229](https://github.com/supercollider/supercollider/issues/4229)
- [Delay1's first output sample is its input, rather than 0 #1313](https://github.com/supercollider/supercollider/issues/1313)
- [Inconsistent initial state of Delay2 #2322](https://github.com/supercollider/supercollider/issues/2322)
- [Integrator init issue #4879](https://github.com/supercollider/supercollider/issues/4879)
- [plugins: Impulse Ctor initial output = 1 for iphase = 0 #2864](https://github.com/supercollider/supercollider/issues/2864)

### Related PRs
- https://github.com/supercollider/supercollider/pull/4971 (PanAz)

### Related discussions
- https://github.com/supercollider/supercollider/issues/4976
