- Title: Refactor server boot logic
- Date proposed: 2022-09-12
- RFC PR: https://github.com/supercollider/rfcs/pull/0020

# Summary

I propose to replace the current server boot logic with a state-machine approach. At the same time, it is an opportunity to reconsider the various server-initialization issues, and streamline and standardize.

# Motivation

Server boot logic has been a thorny problem for years. Even after multiple attempts to fix it, there remain edge cases, particularly with respect to remote server connection.

- As identified in https://github.com/supercollider/supercollider/pull/5149#issuecomment-1223651437 and https://github.com/supercollider/supercollider/pull/5149#issuecomment-1223657585, specification of maxLogins is clumsy (user needs to specify a value in server options, but then this value will be thrown away upon connection).
- Server tries to create a default group for every clientID, rather than only its own clientID. ???

At least these, perhaps others.

There is also not a clear, central place to observe the boot/init process. The process is divided somewhat haphazardly between Server and ServerStatusWatcher methods and is not at all straightforward to trace.

# Specification

1. Clarify the roles of Server and ServerStatusWatcher. I am leaning toward `Server` for user-facing control, and `ServerProcess` to manage a specific server process. A ServerProcess would be created when booting, or connecting to a remote server, and destroyed when that process exits. (Here, we might want a ServerRemoteProcess as well.)
   - Currently, we have an awkward 1:1 mapping between Server and ServerStatusWatcher, which manifests as a lack of clarity in these classes' responsibilities.
   - I would propose a 1:M relationship between a Server object and the processes that may exist for it. We would not allow multiple "running" server processes for one Server object, but, for instance, `server.reboot` may be simplified by switching out ServerProcess objects.
2. Because node, buffer, bus allocators are intimately connected to the server process's state, probably these should be managed in ServerProcess.
3. ServerProcess could centralize and streamline by borrowing the idea of a state machine. It would implement `status_(newState)` where newState is a symbol descriptor. Each status change would perform initialization or shutdown tasks (below).

A sketch of statuses and permitted state transitions:

- 'idle' state (no server process) may go to 'booting'
  - Or, `Server.remote` could go directly to 'handshake'
- 'booting' may go to:
  - 'handshake'
  - 'idle' (server process terminated for some reason)
- 'handshake' may go to:
  - 'initialize'
  - 'idle' (server process terminated for some reason)
- 'initialize' (new allocators, ServerBoot, ServerTree etc.) may go to:
  - 'running'
  - 'idle' (server process terminated for some reason)
- 'running' may go to:
  - 'quitting'
  - 'idle' (server process terminated for some reason)
- 'quitting' may go to:
  - 'idle' (success)

Upon a second read of this, I would add an 'unresponsive' state. If the aliveThread doesn't get a response, `status = \unresponsive` (and perhaps ignore these during init states). If the process shuts down (detected by unixCmd's action function), then `status = \idle` (and a transition into idle status is normal only from 'quitting' status -- otherwise the user would get a warning that the server crashed).

Significant advantages would be 1/ we always know the current status, 2/ it's easy to evaluate whether the process flow is normal or abnormal, based on the previous state and the new state, and 3/ state changes would be centralized in one place.

I may have missed some details -- comments are more than welcome.

# Drawbacks

I welcome discussion on this.

# Unresolved Questions

Did I miss any statuses?

Did I overlook any subtleties of `Server.remote`?
