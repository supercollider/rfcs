- Title: Fix UGen inital sample
- Date proposed: yyyy-mm-dd
- RFC PR: https://github.com/supercollider/rfcs/pull/0000 **update this number after RFC PR has been filed**

# Summary

A majority of UGens do not initialize with a correct _initialization_ sample, and subsequently output an incorrect _first_ sample. This RFC proposes that this be corrected for a substantial batch of core UGens: those in `OscUGens`, `FilterUGens`, and `LFUGens`.

# Motivation

A clarification of terms: The *initializations sample* (or "*init sample*"), is not the same as the *first sample* that is output by the UGen. But **they should be equal**.

When the UGen is initialized (created, but not played) it reads in one sample from any upstream UGens that are connected to its inputs, runs one cycle of calculation based on those inputs, and outputs its own init sample for downstream UGens to use for their initialization. The purpose of this is to set a proper initial state of all the UGens in the graph.

Once the UGen is `run` (the synth is `play`ed), it then begins outputting samples based on its initial state. The core problem is that after the init sample was generated upon initialization, that sample value is not sent to the output, but rather passed over and in many cases the first sample we get out is what should be the *second* sample. Not only that, the state of the UGen (its internal parameters) has already been advanced in time by 1 sample. A subtle distinction, but an important if you're looking to do sample accurate work and expecting precise behavior (filter design, precise triggering, control systems, research more generally).


Introduce the topic. If this is a particularly esoteric or not-well-known section of SuperCollider, a detailed explanation of the background is recommended.

Some example points of discussion:

- What specific problems are you facing right now that you're trying to address?
- Are there any previous discussions? Link to them and summarize them (don't force your readers to read them though!).
- Is there any precedent set by other software? If so, link to resources.

# The problem in detail

# Specification

A concrete, thorough explanation of what is being planned.

##### Before time began
Conceptually, what should we expect happened before a UGen starts? The working model we have here is: *silence*. The assumec input that preceded the first sample was `0`s. This is in contrast to how some UGens are initialized—UGens that rely on previous input samples before time `t=0` make the mistake of setting that previous-sample buffer (usually just 1 to 3 samples) as the *same* is the first input sample. This is effectively creating a short burst of DC offset as your input, which can have bad effects on things like filters, especially resonant ones. This proposal considers those problems within the scope of the "UGen initialization problem".

##### Filter UGens: coefficient initialization
The proposed behavior is that any inputs that are triggers are initialized to a `false` state, so they're ready to detect a trigger input on the first sample. Some Ugens force a trigger or initialize it such that a first-sample trigger is missed. *Note* the trigger input state must be reset after generating the init sample.
Related discussion:


# Scope

This would ideally happen for all UGens, but that's a very large undertaking. By first fixing 3 groups of UGens, it makes solid headway, and forms a blueprint for correcting more in the future.
The current scope targets some core UGens, those in `OscUGens`, `FilterUGens`, and `LFUGens`.

##### Documentation
- Changes and motivating logic will be briefly annotated in the commit message (one per UGen fix, or UGen bundle).
- More detailed notes would go in the PR, unless there's a better place to record the rationale in detail for each UGen change?
- As part of testing, I have a number of scripts, and plots, which I could post somewhere as well (gist? wiki on my SC fork?).
- Documentation should be added for UGen authors to understand this intended behavior. I drafted points to include [previously](https://github.com/supercollider/supercollider/issues/4127#issue-370851888) (in 2018 😱).

# Drawbacks

This touches a lot of UGens! Many of which haven't been altered for years. That being said, most changes will not be audible/visible, except for less popping on play, hopefully ;-).

All changes are intended to be back-compatible, and the most noticeable would be behavior changes related to precise triggering within the first block. Where there is ambiguity in how implementation should be made, those changes will have their own PR for discussion.

# Unresolved questions

There are two categories of unresolved questions: procedural, and implementation.

### Procedural questions


##### Commit strategy
- One commit per `UGen` fix? Bundle identical or "related" fixes into a single commit?


##### PR strategy
  - The current thinking is that this would comprise of multiple PRs. The hope is that discussion around one change wouldn't hold up the whole batch.
    1. For many of these bugs to be visible, certain input signals need to be used with reliably correct init and first samples. For example, `SinOsc` and `Impulse`. It would be helpful, but not necessary that some of these be fixed first. Should these be bundled into a "stage 1" PR?
    2. For remaining "uncontroversial" changes, one PR per `.cpp` file (the current scope is 3 files).
    3. Outliers that may require more discussion, scrutiny or breaking changes would be individual PRs. Such as `Impulse` ([already filed](https://github.com/supercollider/supercollider/pull/4150)), and `Env`.

##### Pre-PR discussion
- There are likely to be at least a few implementation discussion threads (see questions below). Should these happen in the discussion thread here? Is there a better place for potentially multiple forking discussions?

### Implementation questions

Here are some implementation questions that have already come up.

##### Filter UGens: coefficient initialization
Currently, those filters with feedback coefficients initialize them to `0.f`. Initial arguments (like, `freq`, `bw`, etc) are initialized to `uninitializedControl`. This triggers their initialization on the first call to `_next`, which in turns sets their value for the *next block*. As a consequence, filter coefficients *ramp up* from `0.f` to their proper value over the first block (in addition to leading to an incorrect first sample, usually affected by the `a0` coefficient).

To my thinking this isn't correct: the coefficients reflect the filter state determined by initial arguments (which are available at initialization). In practice this tends to result in essentially a "crossfade" between the input and it's filtered version over the first block. I would propose accurately setting these coefficients immediately on initialization. It has the added benefit of avoiding extra calculations when the UGen is started, including the slope calculation and coefficient ramp over the first block (could mean less CPU spike on `run`).

This affects `RPF`, `Resonz`, `Ringz`, `MidEQ` (there may be others).

# Related discussions
- work is well underway, with some spotty notes and records here:
    - https://github.com/mtmccrea/supercollider/projects/1
    - https://github.com/mtmccrea/supercollider/wiki/Notes-on-Ctor-fixes
- "Decide on a policy for handling breaking changes when fixing UGen bugs" https://github.com/supercollider/supercollider/issues/4976


**Outstanding issues that are related or would be fixed by this**
- https://github.com/supercollider/supercollider/issues/4127
- https://github.com/supercollider/supercollider/issues/2333
- https://github.com/supercollider/supercollider/issues/2343
- https://github.com/supercollider/supercollider/issues/4279
- https://github.com/supercollider/supercollider/issues/4229
- https://github.com/supercollider/supercollider/issues/1313
- https://github.com/supercollider/supercollider/issues/2322
- https://github.com/supercollider/supercollider/issues/4879
- https://github.com/supercollider/supercollider/issues/2864
