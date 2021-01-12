- Title: Add an /n_status Message and equivalent messages
- Date proposed: 2021-01-12
- RFC PR: https://github.com/supercollider/rfcs/pull/0000 **update this number after RFC PR has been filed**

# Summary

Add an /n_status message to the OSC command interface, to allow you to check on whether a node currently exists and if it is running or paused.

# Motivation

Given the complications of asynchronous communication, it can be useful to check whether a node already or still exists. (A recent case arose in the VST interface). Currently there is no straightforward way to do this. One can send an /n_query but if the node does not exist this will cause a FAILURE message to be posted (in this case the node not existing is not a failure, just info that we need). This seems like a basic thing that we should be able to query. It would also be useful in multi-client situations, where another client may have created a node.

The existing NodeWatcher approach does not suffice here, since there is no way to reliably register a node that may not exists. Enabling this would require such a message.

As we're doing this we may as well include info on whether the node is running or paused. Anything else?

# Specification

/n_status - Check whether a node exists and its run status.

N * int	node ID
The server sends an /n_status.reply message for each node to registered clients.

/n_status.reply has the following arguments:

int node ID
int status flag, 1 if running, 0 if paused, -1 if node was not found

In addition we will add a corresponding message to the Node class, and optionally a checkNodeStatus method to the Server class. (Thoughts on this welcome!) Other places in the class lib?

# Drawbacks

Adds another command to the interface.

# Unresolved Questions

Whether to add a method to Server. My instinct is no, as it's a bit odd style, and Server is already huge.
