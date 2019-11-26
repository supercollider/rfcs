- Title: Extended Node Info Response
- Date proposed: 2019-11-26
- RFC PR: https://github.com/supercollider/rfcs/pull/5

# Summary

Node info responses (`/n_info ...`) should be extended to include the SynthDef
name and control names and values when the node queried is a synth.

# Motivation

Getting the state of the server node tree is invaluable for writing unit tests.
Comparing the actual server state against the test’s expected state, without
relying on any client side information lets us validate test assumptions
efficiently .

But there are some gaps in functionality for querying the server node tree
state. `/n_query` doesn’t return the SynthDef name or controls if it identifies
a node as a synth. `/s_get` requires knowing the parameters of the synth’s
SynthDef in advance.

This leaves `/g_queryTree`, which does identify the SynthDef name and its
control names and values, but can only be called against the Synth’s parent
group. Unfortunately, for large node trees (~100 nodes or more, or with
extensive SynthDef controls) the `/g_queryTree.reply` response is often
malformed; the largest OSC packet cannot hold all of the data and truncates it.
Avoiding this overflow by querying various subtrees and patching the results
together is error-prone and unreliable for the same reasons. It’s not possible
to query the state of a synth without potentially querying the state of its
siblings too, which runs the risk of overflowing the OSC packet.

As a solution, if we extend `/n_query`’s `/n_info` response to include the
SynthDef name and its controls if the node in question is a synth, we can query the
entire node tree by sending a series of `/n_query` requests. This allows us to
perform the depth first traversal of the node tree on the client side and
construct its complete state without risk of error, at the cost of potentially not
capturing modifications that take place while the querying occurs.

# Specification

Currently `/n_info` returns the following:

```
int: node ID
int: the node’s parent group ID
int: previous node ID, -1 if no previous node.
int: next node ID, -1 if no next node.
int: 1 if the node is a group, 0 if it is a synth
The following two arguments are only sent if the node is a group:
int: the ID of the head node, -1 if there is no head node
int: the ID of the tail node, -1 if there is no tail node
```

This should be modified to:

```
int: node ID
int: the node’s parent group ID
int: previous node ID, -1 if no previous node.
int: next node ID, -1 if no next node.
int: 1 if the node is a group, 0 if it is a synth
The following two arguments are only sent if the node is a group:
int: the ID of the head node, -1 if there is no head node
int: the ID of the tail node, -1 if there is no tail node
The following arguments are only sent if the node is a synth:
str: the SynthDef name
int: the number of control name/value pairs
N * control number:
    str: the control name
    int/str: the control value or bus mapping symbol
```

The implementation path for scsynth is fairly simple:

- Refactor `Group_QueryTreeAndControls()` by extracting the logic that
  describes a Synth’s SynthDef name and controls into a new
  `Node_QueryControls()` function.
- Add a pointer to the node to the `NodeEndMsg` struct.
- Modify `NodeEndMsg::Perform()` to use a big OSC packet rather than the
  current small packet for compatibility with `Node_QueryControls()`.
- Modify `NodeEndMsg::Perform()` to write the node’s SynthDef name and controls
  into its OSC packet via `Node_QueryControls()` when creating a `/n_info`
  response.

# Drawbacks

The additional packet size and computation makes `/n_query` potentially slower.
For that reason I’ve only proposed modifying `/n_info` and not the other
automatic node notifications.

Additionally, logic with a hard expectation of the older OSC message format for
`/n_info` responses could break with this change. A quick search of the
SuperCollider GitHub organization doesn’t show any usage of `/n_query` outside
of the `Node.query` method. Impact on third party libraries would need to be
investigated.

# Unresolved Questions

Should this logic be extended to all of the other node notifications (`/n_go`,
`/n_end`, `/n_off`, `/n_on`, `/n_move`)?

Can `/g_queryTree` be fixed or modified to avoid the overflow problem?

Is accessing a node in `NodeEndMsg::Perform()` always guaranteed to be safe?
