- Title: Add TCP port information to NetAddr
- Date proposed: 2020-07-20
- RFC PR: https://github.com/supercollider/rfcs/pull/0000 **update this number after RFC PR has been filed**

# Summary

Short description, 1-2 sentences.
Currently it is not possible to directly access an open TCP socket from sclang. This RFC proposes a way to access the metadata of the socket created when calling NetAddr.connect method so the user can listen to messages coming back from that socket.

# Motivation

One of the differences between TCP and UDP is that in UDP the connecton is usually not kept alive. The sender of a message simply sends the UDP package and does not bother if the package arrives, is actually sent, or even if the host exists. In TCP a connection between both points must be made before any packages are sent, and this connection is kept open until one of the ends closes it. This usually means that the sender opens a random port locally to receive incoming data from the other end of the TCP connection. One of the advantages of TCP is that a host behind a NAT or a complex network routing can open a connection to a public IP on the internet and, because the connection is two-way, the host receiving the connection can send messages back through the opened channel without the need to know the route or how to solve NAT routing and so on. This is not possible with UDP, when both ends need to know the IP of each other to exchange messages.

While it is possible to open TCP connections from sclang using `NetAddr.connect` currently it is not possible to get any information about the opened socket, making it difficult to listen back to messages sent on that channel. For example in the [OSCRouterClient Quark](https://github.com/aiberlin/HyperDisCo/blob/master/Classes/OSCRouterClient.sc#L95) it needs to call `this.addOSCRecvFunc` with a generic function that will listen to any incoming message on any port, and add some information on the application protocol layer (in this case the randomId assigned when the first call to the other end of the connection is made) so we can check back and assign the correct TCP port to that socket.

The proposal in this RFC is to expose the metadata of the socket, especially the port it listens to, so we can do things like `n = NetAddr(addr, port); OSCFunc({}, n.tcpRecvPort);` and directly listen to messages received in that that opened socket.

# Specification

Adds the instance attribute `tcpRecvPort` to `NetAddr`, an integer representing the locally opened port for a TCP connection. It is `nil` by default and is only set after `.connect` or `.tryConnectTCP` is called.

# Drawbacks


# Unresolved Questions

Are there other information to be added to `NetAddr` that might be useful in the context of TCP sockets?
