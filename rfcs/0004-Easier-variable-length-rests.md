- Title: Easier variable-length rests in patterns
- Date proposed: 2019-11-01
- RFC PR: https://github.com/supercollider/rfcs/pull/0004

# Summary

Currently it is easy to embed a rest of a fixed value in a sequence: `Pseq([0, Rest(1), 2])`.

It is not easy to embed a rest with being lazy-calculation value: `Pseq([0, Pwhite(1, 3, 1).collect(Rest(_)), 2])`.

# Motivation

This question keeps coming up repeatedly on the mailing list and forum. It's a legitimate user need.

Users often expect to be able to write `Rest(Pwhite(1, 3))` and have the Rest be automatically stream-ified. This might be a reasonable expectation (and not hard to implement). But there is also precedent to reject that approach, e.g., an array of patterns requires a `Ptuple()` wrapper.

Alternately, we could follow the model of `.collect` -> Pcollect, `.select` -> Pselect, `.keep` -> Pfin and provide a .rest method for patterns and other objects.

Some prior discussion is here.

https://scsynth.org/t/pbind-and-samples-with-different-durations-2/735/13

# Specification

1. A `Prest(patternOrStream, n)` pattern, to take `n` values from the source pattern and output `Rest()` objects with those values -- following the model of Pcollect to implement .collect, etc.
   a. Default n should be 1. See unresolved questions.

2. A `.rest(n)` method that returns `Prest(this, n)` for patterns, and `Rest(this)` or other objects. Possibly, for SequenceableCollections, we might do `this.collect(Rest(_))`.
   - `2.rest` --> `Rest(2)`
   - `Pwhite(1, 3, inf)` --> `Prest(Pwhite(1, 3, inf), 1)`
   - (Maybe?) `[1, 2].rest` --> `[Rest(1), Rest(2)]` but here, I don't have a concrete use case. Currently the relationship between rests and arrays is undefined.

# Drawbacks

A possible objection is "too many ways to write the same thing." In fact, I raised that objection to a `.rest` method in the past. But we have both `.fin` and `.keep` as pattern aliases for `Pfin`, so there is precedent and I've relaxed my objection.

# Unresolved Questions

### `.rest` is not precisely analogous to `.collect`.

In `pattern.collect(...)`, we expect every value of the pattern to be streamed out and further mapped by the function.

In `pattern.rest(...)`, it's pointless to stream out all the values and turn them into rests, because then you will have a potentially long series of silent events.

`Pbind(\degree, Pwhite(0, 7, inf), \dur, Pseq([0.5, 0.5, rest(Pwhite(1, 4, inf) * 0.5)], inf))` -- if we follow the `collect` model, then you get two notes and then silence, indefinitely.

I believe the best solution is to limit the length of the Prest stream, default = 1. But this is worth discussing.

### (xyz).rest vs rest(xyz) vs Rest(xyz)

Note here that I decided to save a dot by using function-style notation: `rest(Pwhite(1, 4, inf) * 0.5)`.

If we support this, then inevitably -- guaranteed -- some user will write `Rest(Pwhite(1, 4, inf) * 0.5)` and get annoyed that it doesn't work.

I think it wouldn't be hard to fold Prest behavior into Rest:embedInStream. Is this worth doing for user friendliness, or should we be strict and enforce the distinction between a Rest object (simple value) and the rest method applied to patterns?