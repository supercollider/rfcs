- Title: Synth Query Command
- Date proposed: 2019-11-26
- RFC PR: https://github.com/supercollider/rfcs/pull/5
- Draft 2

# Summary

We should introduct a new command, `/s_query`, to allow retrieving both the
SynthDef name and the names and current values of a Synth.

# Motivation

Getting the state of the server node tree is invaluable for writing unit tests.
Comparing the actual server state against the test’s expected state, without
relying on any client side information lets us validate test assumptions
efficiently .

But there are some gaps in functionality for querying the server node tree
state. `/n_query` doesn’t return the SynthDef name or controls if it identifies
a node as a synth. `/s_get` requires knowing the parameters of the synth’s
SynthDef in advance. This leaves `/g_queryTree`, which does identify the
SynthDef name and its control names and values, but can only be called against
the Synth’s parent group.

Unfortunately, for large node trees (~100 nodes or more, or with
extensive SynthDef controls) the `/g_queryTree.reply` response is often
malformed; the largest OSC packet cannot hold all of the data and truncates it.
Avoiding this overflow by querying various subtrees and patching the results
together is error-prone and unreliable for the same reasons. It’s not possible
to query the state of a synth without potentially querying the state of its
siblings too, which runs the risk of overflowing the OSC packet.

As a solution, we can add a new `/s_query` command to return the SynthDef name,
control names, and current values. This allows us to perform the depth-first
traversal of the node tree manually on the client-side. Using a combination of
`/n_query` and `/s_query` commands, we can construct the equivalent of what
`/g_queryTree` provides without risk of error, at the cost of some speed and
potentially not capturing modifications that take place while the querying
occurs. That trade-off is acceptable for the purposes of unit testing.

# Specification

The new `/s_query` command should return a `/s_info` response with the
following format.

```
int: node ID
str: the SynthDef name
int: the number of control name/value pairs
N * control number:
    str: the control name
    int/str: the control value or bus mapping symbol
```

The `/s_query` / `/s_info` naming matches the existing `/n_query` / `n_info`
and `/b_query` / `/b_info` requests and responses.

The response need only be sent to the originating client, unlike `/n_info`
which is broadcast to all clients.

Some existing server-side `/g_queryTree` logic can be refactored to support the
new command.

# Drawbacks

Simply modifying `/n_info` to provide the same additional information as the proposed
`/s_query.reply` would be simpler to implement. However, in the interest of
reducing risk of client breakage, adding a new command is always going to be
safer than modifying a pre-existing one.

The server command reference will need to be updated to clearly advertise the
version at which `/s_query` became available.

Adding `/s_query` does nothing to address the inherent problems with calling
`/g_queryTree` against large node trees.
