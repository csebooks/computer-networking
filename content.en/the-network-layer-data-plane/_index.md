---
title: 'The Network Layer: Data Plane'
weight: 4
---
# The Network Layer: Data Plane

We learned in the previous chapter that the transport layer provides various forms of process-to-process communication by relying on the network layer’s host-to-host communication service. We also learned that the transport layer does so without any knowledge about how the network layer actually implements this service. So perhaps you’re now wondering, what’s under the hood of the host-to-host communication service, what makes it tick?

In this chapter and the next, we’ll learn exactly how the network layer can pro- vide its host-to-host communication service. We’ll see that unlike the transport and application layers, _there is a piece of the network layer in each and every host and router in the network._ Because of this, network-layer protocols are among the most challenging (and therefore among the most interesting!) in the protocol stack.

Since the network layer is arguably the most complex layer in the protocol stack, we’ll have a lot of ground to cover here. Indeed, there is so much to cover that we cover the network layer in two chapters. We’ll see that the network layer can be decomposed into two interacting parts, the **data plane** and the **control plane**. In Chapter 4, we’ll first cover the data plane functions of the network layer—the _per-router_ functions in the network layer that determine how a datagram (that is, a network-layer packet) arriving on one of a router’s input links is forwarded to one of that router’s output links. We’ll cover both traditional IP forwarding (where for- warding is based on a datagram’s destination address) and generalized forwarding (where forwarding and other functions may be performed using values in several different fields in the datagram’s header). We’ll study the IPv4 and IPv6 protocols and addressing in detail. In Chapter 5, we’ll cover the control plane functions of the network layer—the _network-wide_ logic that controls how a datagram is routed

among routers along an end-to-end path from the source host to the destination host. We’ll cover routing algorithms, as well as routing protocols, such as OSPF and BGP, that are in widespread use in today’s Internet. Traditionally, these control-plane rout- ing protocols and data-plane forwarding functions have been implemented together, monolithically, within a router. Software-defined networking (SDN) explicitly sepa- rates the data plane and control plane by implementing these control plane functions as a separate service, typically in a remote “controller.” We’ll also cover SDN con- trollers in Chapter 5.

This distinction between data-plane and control-plane functions in the network layer is an important concept to keep in mind as you learn about the network layer — it will help structure your thinking about the network layer and reflects a modern view of the network layer’s role in computer networking.
