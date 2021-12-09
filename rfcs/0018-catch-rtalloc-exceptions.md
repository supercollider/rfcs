- Title: Catch RTAlloc exceptions to prevent leaky behavior
- Date proposed: 2021-12-10
- RFC PR: https://github.com/supercollider/rfcs/pull/18

# Summary

UGens don't catch RTAlloc exceptions (e.g. alloc failed), leading to interruption of the processing block, and repeated calls to UGens constructors, leading to memory leaks. Comments are requested about how to best fix this issue for all affected UGens.

# Motivation

Every UGen that needs to allocate memory in real-time does so through RTAlloc, which throws an exception if there is not enough available memory. Those exceptions are never caught before the audio backend does so, meaning that the processing block in which they occurs exits early. Consequences are: 
- no UGens after (in the tree) the one that threw the exception can process audio
- if the exception is thrown in a UGen constructor, the whole synth that contains that UGen doesn't advance from "construction-phase". This means that that constructor (and all other UGens' who are before it in the node tree) will be called again and again at every processing block
- Memory leak: if any of the repeteadly called constructors allocates memory via RTAlloc, references to previously allocated memory by those same UGens are lost.


Relevant open issues: #782 #3563 #5634

# Specification

RTAlloc is used in 125 times in 25 files in `server/plugins` (and 802 times in 164 files in `sc3-plugins`). Wouldn't it be nice to agree on how to fix best it before anyone starts editing all these places? I so much would like to have this issue fixed that I volunteer to do tedious work myself :)


My proposals:

UGens that use RTAlloc should be responsible for catching the exception. We could make a macro like [this](https://github.com/supercollider/supercollider/issues/782#issuecomment-241276856) and use it all over the place.
- pro: UGens not using RTAlloc wouldn't be bothered
- con: all UGens that use RTAlloc need to be fixed


Another way would be to catch it in `SC_Graph.cpp`, specifically in `Graph_FirstCalc`, `Graph_Calc_unit` and `Graph_CalcTrace`.
- pro: fix in one file only
- con: every call to every UGen's `mCalcFunc` would be wrapped in a try/catch block

# Drawbacks

For now I can see only the pros and cons of my proposals as I listed above. Will update :)
