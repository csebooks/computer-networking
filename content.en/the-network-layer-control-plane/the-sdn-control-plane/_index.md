---
title: 'The SDN Control Plane'
weight: 4
references:
    videos:
        - youytube:iTjMc201GK0
        - youytube:lfqQhRkL4Vo
    links:
        - https://www.geeksforgeeks.org/software-defined-networking-sdn-controller/
        - https://www.sdxcentral.com/networking/sdn/definitions/what-the-definition-of-software-defined-networking-sdn/inside-sdn-architecture/
    books:
        - b1:
            title: SDN: Software Defined Networks An Authoritative Review of Network Programmability
            url: https://www.google.co.in/books/edition/SDN_Software_Defined_Networks/Bc1qAAAAQBAJ?hl=en&gbpv=0
        - b2:
            title: Software-Defined Networking 2 Extending SDN Control to Large-Scale Networks 
            url:  https://www.google.co.in/books/edition/Software_Defined_Networking_2/pbSgEAAAQBAJ?hl=en&gbpv=0
---


## The SDN Control Plane
In this Section, we’ll dive into the SDN control plane—the network-wide logic that controls packet forwarding among a network’s SDN-enabled devices, as well as the configuration and management of these devices and their services. Our study here builds on our earlier discussion of generalized SDN forwarding in Section 4.4, so you might want to first review that Section, as well as Section 5.1 of this chapter, before continuing on. As in Section 4.4, we’ll again adopt the terminology used in the SDN literature and refer to the network’s forwarding devices as “packet switches” (or just switches, with “packet” being understood), since forwarding decisions can be made on the basis of network-layer source/destination addresses, link-layer source/destina- tion addresses, as well as many other values in transport-, network-, and link-layer packet-header fields.

Four key characteristics of an SDN architecture can be identified [Kreutz 2015]:

• _Flow-based forwarding._ Packet forwarding by SDN-controlled switches can be based on any number of header field values in the transport-layer, network-layer, or link-layer header. We saw in Section 4.4 that the OpenFlow1.0 abstraction allows forwarding based on eleven different header field values. This contrastssharply with the traditional approach to router-based forwarding that we studied in Sections 5.2–5.4, where forwarding of IP datagrams was based solely on a datagram’s destination IP address. Recall from Figure 5.2 that packet forwarding rules are specified in a switch’s flow table; it is the job of the SDN control plane to compute, manage and install flow table entries in all of the network’s switches.

• _Separation of data plane and control plane._ This separation is shown clearly in Figures 5.2 and 5.14. The data plane consists of the network’s switches— relatively simple (but fast) devices that execute the “match plus action” rules in their flow tables. The control plane consists of servers and software that deter- mine and manage the switches’ flow tables.

• _Network control functions: external to data-plane switches._ Given that the “S” in SDN is for “software,” it’s perhaps not surprising that the SDN control plane is implemented in software. Unlike traditional routers, however, this software exe- cutes on servers that are both distinct and remote from the network’s switches. As shown in Figure 5.14, the control plane itself consists of two components—an SDN controller (or network operating system [Gude 2008]) and a set of network-control applications. The controller maintains accurate network state information (e.g., the state of remote links, switches, and hosts); provides this information to the network- control applications running in the control plane; and provides the means through which these applications can monitor, program, and control the underlying network devices. Although the controller in Figure 5.14 is shown as a single central server, in practice the controller is only logically centralized; it is typically implemented on several servers that provide coordinated, scalable performance and high availability.

• _A programmable network._ The network is programmable through the network- control applications running in the control plane. These applications represent the “brains” of the SDN control plane, using the APIs provided by the SDN controller to specify and control the data plane in the network devices. For example, a routing network-control application might determine the end-end paths between sources and destinations (for example, by executing Dijkstra’s algorithm using the node- state and link-state information maintained by the SDN controller). Another net- work application might perform access control, that is, determine which packets are to be blocked at a switch, as in our third example in Section 4.4.3. Yet another application might have switches forward packets in a manner that performs server load balancing (the second example we considered in Section 4.4.3).

From this discussion, we can see that SDN represents a significant “unbundling” of network functionality—data plane switches, SDN controllers, and network-control applications are separate entities that may each be provided by different vendors and organizations. This contrasts with the pre-SDN model in which a switch/router (together with its embedded control plane software and protocol implementations) was monolithic, vertically integrated, and sold by a single vendor. This unbundling

![Alt text](image-14.png)
**Figure 5.14**  ♦   Components of the SDN architecture: SDN-controlled switches, the SDN controller, network-control applications

of network functionality in SDN has been likened to the earlier evolution from main- frame computers (where hardware, system software, and applications were provided by a single vendor) to personal computers (with their separate hardware, operating systems, and applications). The unbundling of computing hardware, system soft- ware, and applications has led to a rich, open ecosystem driven by innovation in all three of these areas; one hope for SDN is that it will continue to drive and enable such rich innovation.

Given our understanding of the SDN architecture of Figure 5.14, many questions naturally arise. How and where are the flow tables actually computed? How are these tables updated in response to events at SDN-controlled devices (e.g., an attached link going up/down)? And how are the flow table entries at multiple switches coordinated in such a way as to result in orchestrated and consistent network-wide functionality (e.g., end-to-end paths for forwarding packets from sources to destinations, or coor- dinated distributed firewalls)? It is the role of the SDN control plane to provide these, and many other, capabilities.

### The SDN Control Plane: SDN Controller and SDN Network-control Applications

Let’s begin our discussion of the SDN control plane in the abstract, by consider- ing the generic capabilities that the control plane must provide. As we’ll see, this abstract, “first principles” approach will lead us to an overall architecture that reflects how SDN control planes have been implemented in practice.

As noted above, the SDN control plane divides broadly into two components— the SDN controller and the SDN network-control applications. Let’s explore the controller first. Many SDN controllers have been developed since the earliest SDN controller [Gude 2008]; see [Kreutz 2015] for an extremely thorough survey. Figure 5.15 provides a more detailed view of a generic SDN controller. A controller’s function- ality can be broadly organized into three layers. Let’s consider these layers in an uncharacteristically bottom-up fashion:

• _A communication layer: communicating between the SDN controller and con- trolled network devices._ Clearly, if an SDN controller is going to control the operation of a remote SDN-enabled switch, host, or other device, a protocol is needed to transfer information between the controller and that device. In addition, a device must be able to communicate locally-observed events to the controller (for example, a message indicating that an attached link has gone up or down, that a device has just joined the network, or a heartbeat indicating that a device is up and operational). These events provide the SDN controller with an up-to- date view of the network’s state. This protocol constitutes the lowest layer of the controller architecture, as shown in Figure 5.15. The communication between the controller and the controlled devices cross what has come to be known as the controller’s “southbound” interface. In Section 5.5.2, we’ll study OpenFlow—a specific protocol that provides this communication functionality. OpenFlow is implemented in most, if not all, SDN controllers.

• _A network-wide state-management layer._ The ultimate control decisions made by the SDN control plane—for example, configuring flow tables in all switches to achieve the desired end-end forwarding, to implement load balancing, or to imple- ment a particular firewalling capability—will require that the controller have up- to-date information about state of the networks’ hosts, links, switches, and other SDN-controlled devices. A switch’s flow table contains counters whose values might also be profitably used by network-control applications; these values should thus be available to the applications. Since the ultimate aim of the control plane is to determine flow tables for the various controlled devices, a controller might also maintain a copy of these tables. These pieces of information all constitute exam- ples of the network-wide “state” maintained by the SDN controller.

• _The interface to the network-control application layer._ The controller interacts with network-control applications through its “northbound” interface. This API

![Alt text](image-15.png)
**Figure 5.15**  ♦  Components of an SDN controller

allows network-control applications to read/write network state and flow tables within the state-management layer. Applications can register to be notified when state-change events occur, so that they can take actions in response to network event notifications sent from SDN-controlled devices. Different types of APIs may be provided; we’ll see that two popular SDN controllers communicate with their applications using a REST [Fielding 2000] request-response interface.

We have noted several times that an SDN controller can be considered to be “logically centralized,” that is, that the controller may be viewed externally (for exam- ple, from the point of view of SDN-controlled devices and external network-control
applications) as a single, monolithic service. However, these services and the data- bases used to hold state information are implemented in practice by a _distributed_ set of servers for fault tolerance, high availability, or for performance reasons. With controller functions being implemented by a _set_ of servers, the semantics of the con- troller’s internal operations (e.g., maintaining logical time ordering of events, con- sistency, consensus, and more) must be considered [Panda 2013]. Such concerns are common across many different distributed systems; see [Lamport 1989, Lampson 1996] for elegant solutions to these challenges. Modern controllers such as Open- Daylight [OpenDaylight 2020] and ONOS [ONOS 2020] (see sidebar) have placed considerable emphasis on architecting a logically centralized but physically distrib- uted controller platform that provides scalable services and high availability to the controlled devices and network-control applications alike.

The architecture depicted in Figure 5.15 closely resembles the architecture of the originally proposed NOX controller in 2008 [Gude 2008], as well as that of today’s OpenDaylight [OpenDaylight 2020] and ONOS [ONOS 2020] SDN controllers (see sidebar). We’ll cover an example of controller operation in Section 5.5.3. First, how- ever, let’s examine the OpenFlow protocol, the earliest and now one of several pro- tocols that can be used for communication between an SDN controller and a controlled device, which lies in the controller’s communication layer.

### OpenFlow Protocol
 The OpenFlow protocol [OpenFlow 2009, ONF 2020] operates between an SDN controller and an SDN-controlled switch or other device implementing the Open- Flow API that we studied earlier in Section 4.4. The OpenFlow protocol operates over TCP, with a default port number of 6653.

Among the important messages flowing from the controller to the controlled switch are the following:

• _Configuration._ This message allows the controller to query and set a switch’s configuration parameters.

• _Modify-State._ This message is used by a controller to add/delete or modify entries in the switch’s flow table, and to set switch port properties.

• _Read-State._ This message is used by a controller to collect statistics and counter values from the switch’s flow table and ports.

• _Send-Packet._ This message is used by the controller to send a specific packet out of a specified port at the controlled switch. The message itself contains the packet to be sent in its payload.

Among the messages flowing from the SDN-controlled switch to the controller are the following:

• _Flow-Removed._ This message informs the controller that a flow table entry has been removed, for example by a timeout or as the result of a received _modify-state_ message.• _Port-status._ This message is used by a switch to inform the controller of a change in port status.

• _Packet-in._ Recall from Section 4.4 that a packet arriving at a switch port and not matching any flow table entry is sent to the controller for additional processing. Matched packets may also be sent to the controller, as an action to be taken on a match. The _packet-in_ message is used to send such packets to the controller.

Additional OpenFlow messages are defined in [OpenFlow 2009, ONF 2020].



### Data and Control Plane Interaction: An Example
 In order to solidify our understanding of the interaction between SDN-controlled switches and the SDN controller, let’s consider the example shown in Figure 5.16, in which Dijkstra’s algorithm (which we studied in Section 5.2) is used to determine shortest path routes. The SDN scenario in Figure 5.16 has two important differ- ences from the earlier per-router-control scenario of Sections 5.2.1 and 5.3, where Dijkstra’s algorithm was implemented in each and every router and link-state updates were flooded among all network routers:

• Dijkstra’s algorithm is executed as a separate application, outside of the packet switches.

• Packet switches send link updates to the SDN controller and not to each other.

In this example, let’s assume that the link between switch s1 and s2 goes down; that shortest path routing is implemented, and consequently and that incom- ing and outgoing flow forwarding rules at s1, s3, and s4 are affected, but that s2’s

![Alt text](image-16.png)
**Figure 5.16**  ♦  SDN controller scenario: Link-state change

infooperation is unchanged. Let’s also assume that OpenFlow is used as the communi- cation layer protocol, and that the control plane performs no other function other than link-state routing.

1\. Switch s1, experiencing a link failure between itself and s2, notifies the SDN controller of the link-state change using the OpenFlow _port-status_ message.

2\. The SDN controller receives the OpenFlow message indicating the link-state change, and notifies the link-state manager, which updates a link-state database.

3\. The network-control application that implements Dijkstra’s link-state routing has previously registered to be notified when link state changes. That applica- tion receives the notification of the link-state change.

4\. The link-state routing application interacts with the link-state manager to get updated link state; it might also consult other components in the state- management layer. It then computes the new least-cost paths.

5\. The link-state routing application then interacts with the flow table manager, which determines the flow tables to be updated.

6\. The flow table manager then uses the OpenFlow protocol to update flow table entries at affected switches—s1 (which will now route packets destined to s2 via s4), s2 (which will now begin receiving packets from s1 via intermediate switch s4), and s4 (which must now forward packets from s1 destined to s2).

This example is simple but illustrates how the SDN control plane provides control- plane services (in this case, network-layer routing) that had been previously imple- mented with per-router control exercised in each and every network router. One can now easily appreciate how an SDN-enabled ISP could easily switch from least-cost path routing to a more hand-tailored approach to routing. Indeed, since the controller can tailor the flow tables as it pleases, it can implement _any_ form of forwarding that it pleases—simply by changing its application-control software. This ease of change should be contrasted to the case of a traditional per-router control plane, where soft- ware in all routers (which might be provided to the ISP by multiple independent vendors) must be changed.

### SDN: Past and Future
 Although the intense interest in SDN is a relatively recent phenomenon, the techni- cal roots of SDN, and the separation of the data and control planes in particular, go back considerably further. In 2004, [Feamster 2004, Lakshman 2004, RFC 3746] all argued for the separation of the network’s data and control planes. [van der Merwe 1998] describes a control framework for ATM networks [Black 1995] with multiple controllers, each controlling a number of ATM switches. The Ethane project [Casado 2007] pioneered the notion of a network of simple flow-based Ethernet switches with match-plus-action flow tables, a centralized controller that managed flowadmission and routing, and the forwarding of unmatched packets from the switch to the controller. A network of more than 300 Ethane switches was operational in 2007. Ethane quickly evolved into the OpenFlow project, and the rest (as the saying goes) is history!

Numerous research efforts are aimed at developing future SDN architectures and capabilities. As we have seen, the SDN revolution is leading to the disruptive replacement of dedicated monolithic switches and routers (with both data and control planes) by simple commodity switching hardware and a sophisticated software con- trol plane. A generalization of SDN known as network functions virtualization (NFV) (which we discussed earlier in Section 4.5) similarly aims at disruptive replacement of sophisticated middleboxes (such as middleboxes with dedicated hardware and proprietary software for media caching/service) with simple commodity servers, switching, and storage. A second area of important research seeks to extend SDN concepts from the intra-AS setting to the inter-AS setting [Gupta 2014].

![Alt text](image-37.png)

![Alt text](image-17.png)
**Figure 5.17**  ♦  A simplified view of the OpenDaylight controller

and the Simple Network Management Protocol (SNMP) and the Network Configuration (NETCONF) protocol, both of which we’ll cover in Section 5.7. The Open vSwitch Database Management Protocol (OVSDB) is used to manage data center switching, an important application area for SDN technology. We’ll introduce data center networking in Chapter 6.

Network Orchestrations and Applications determine how data-plane forwarding and other services, such as firewalling and load balancing, are accomplished in the controlled devices. ODL provides two ways in which applications can interoperate with native controller services (and hence devices) and with each other. In the API-Driven (AD-SAL) approach, shown in Figure 5.17, applications communicate with controller modules using a REST request-response API running over HTTP. Initial releases of the OpenDaylight controller provided only the AD-SAL. As ODL became increasingly used for network configuration and management, later ODL releases introduced a Model- Driven (MD-SAL) approach. Here, the YANG data modeling language [RFC 6020] defines models of device, protocol, and network configuration and operational state data. Devices are then configured and managed by manipulating this data using the NETCONF protocol.

![Alt text](image-18.png)
**Figure 5.18**  ♦  ONOS controller architecture

**The ONOS Controller**
Figure 5.18 presents a simplified view of the ONOS controller ONOS 2020]. Similar to the canonical controller in Figure 5.15, three layers can be identified in the ONOS controller:

• _Northbound abstractions and protocols._ A unique feature of ONOS is its intent framework, which allows an application to request a high-level service (e.g., to setup a connection between host A and Host B, or conversely to not allow Host A and host B to communicate) without having to know the details of how this service is performed. State information is provided to network-control applications across the northbound API either synchronously (via query) or asynchronously (via listener callbacks, e.g., when network state changes).

• _Distributed core._ The state of the network’s links, hosts, and devices is maintained in ONOS’s distributed core. ONOS is deployed as a service on a set of intercon- nected servers, with each server running an identical copy of the ONOS software; an increased number of servers offers an increased service capacity. The ONOS core
provides the mechanisms for service replication and coordination among instances, 
providing the applications above and the network devices below with the abstraction 
of logically centralized core services.
• Southbound abstractions and protocols. The southbound abstractions mask the hetero geneity of the underlying hosts, links, switches, and protocols, allowing the distributed 
core to be both device and protocol agnostic. Because of this abstraction, the south bound interface below the distributed core is logically higher than in our canonical 
controller in Figure 5.14 or the ODL controller in Figure 5.17.
