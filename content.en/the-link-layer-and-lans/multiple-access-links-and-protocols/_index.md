---
title: 'Multiple Access Links and Protocols'
weight: 3
references:
    videos:
        - youtube:YAjfUc7Tt24
        - youtube:T0aZ0APuYzE
    links:
        - https://www.geeksforgeeks.org/multiple-access-protocols-in-computer-network/
        - https://www.javatpoint.com/multiple-access-protocols
    books:
        - b1:
            title: Multiple Access Protocols
            url: https://www.google.co.in/books/edition/Multiple_Access_Protocols/YIXTBwAAQBAJ?hl=en&gbpv=0
        - b2:
            title: Performance Analysis of Multiple Access Protocols
            url: https://www.google.co.in/books/edition/Performance_Analysis_of_Multiple_Access/PMRY3J0a5UYC?hl=en&gbpv=0
---

## Multiple Access Links and Protocols
In the introduction to this chapter, we noted that there are two types of network links: point-to-point links and broadcast links. A **point-to-point link** consists of a single sender at one end of the link and a single receiver at the other end of the link. Many link-layer protocols have been designed for point-to-point links; the point-to-point protocol (PPP) and high-level data link control (HDLC) are two such protocols. The second type of link, a **broadcast link**, can have multiple sending and receiving nodes all connected to the same, single, shared broadcast channel. The term _broadcast_ is used here because when any one node transmits a frame, the channel broadcasts the frame and each of the other nodes receives a copy. Ethernet and wireless LANs are examples of broadcast link-layer technologies. In this section, we’ll take a step back from specific link-layer protocols and first examine a problem of central importance to the link layer: how to coordinate the access of multiple sending and receiving nodes to a shared broadcast channel—the **multiple access problem**. Broadcast chan- nels are often used in LANs, networks that are geographically concentrated in a single building (or on a corporate or university campus). Thus, we’ll look at how multiple access channels are used in LANs at the end of this section.

We are all familiar with the notion of broadcasting—television has been using it since its invention. But traditional television is a one-way broadcast (that is, one fixed node transmitting to many receiving nodes), while nodes on a computer network broadcast channel can both send and receive. Perhaps a more apt human analogy for a broadcast channel is a cocktail party, where many people gather in a large room (the air providing the broadcast medium) to talk and listen. A second good analogy is something many readers will be familiar with—a classroom—where teacher(s) and student(s) similarly share the same, single, broadcast medium. A central problem inboth scenarios is that of determining who gets to talk (that is, transmit into the chan- nel) and when. As humans, we’ve evolved an elaborate set of protocols for sharing the broadcast channel:

“Give everyone a chance to speak.” 
“Don’t speak until you are spoken to.” 
“Don’t monopolize the conversation.” 
“Raise your hand if you have a question.” 
“Don’t interrupt when someone is speaking.” 
“Don’t fall asleep when someone is talking.”

Computer networks similarly have protocols—so-called **multiple access protocols**—by which nodes regulate their transmission into the shared broadcast channel. As shown in Figure 6.8, multiple access protocols are needed in a wide variety of network settings, including both wired and wireless access networks, and satellite networks. Although technically each node accesses the broadcast chan- nel through its adapter, in this section, we will refer to the _node_ as the sending and

![Alt text](image-7.png)

**Figure 6.8**  ♦  Various multiple access channels

receiving device. In practice, hundreds or even thousands of nodes can directly com- municate over a broadcast channel.

Because all nodes are capable of transmitting frames, more than two nodes can transmit frames at the same time. When this happens, all of the nodes receive multiple frames at the same time; that is, the transmitted frames **collide** at all of the receiv- ers. Typically, when there is a collision, none of the receiving nodes can make any sense of any of the frames that were transmitted; in a sense, the signals of the col- liding frames become inextricably tangled together. Thus, all the frames involved in the collision are lost, and the broadcast channel is wasted during the collision inter- val. Clearly, if many nodes want to transmit frames frequently, many transmissions will result in collisions, and much of the bandwidth of the broadcast channel will be wasted.

In order to ensure that the broadcast channel performs useful work when mul- tiple nodes are active, it is necessary to somehow coordinate the transmissions of the active nodes. This coordination job is the responsibility of the multiple access protocol. Over the past 40 years, thousands of papers and hundreds of PhD disserta- tions have been written on multiple access protocols; a comprehensive survey of the first 20 years of this body of work is [Rom 1990]. Furthermore, active research in multiple access protocols continues due to the continued emergence of new types of links, particularly new wireless links.

Over the years, dozens of multiple access protocols have been implemented in a variety of link-layer technologies. Nevertheless, we can classify just about any multiple access protocol as belonging to one of three categories: **channel partition- ing protocols**, **random access protocols**, and **taking-turns protocols**. We’ll cover these categories of multiple access protocols in the following three subsections.

Let’s conclude this overview by noting that, ideally, a multiple access protocol for a broadcast channel of rate _R_ bits per second should have the following desirable characteristics:

1\. When only one node has data to send, that node has a throughput of _R_ bps. 
2. When _M_ nodes have data to send, each of these nodes has a throughput of _R_/_M_bps. This need not necessarily imply that each of the _M_ nodes always has an instantaneous rate of _R_/_M_, but rather that each node should have an average transmission rate of _R_/_M_ over some suitably defined interval of time.
3\. The protocol is decentralized; that is, there is no master node that represents a single point of failure for the network.
4\. The protocol is simple, so that it is inexpensive to implement.

### Channel Partitioning Protocols
 Recall from our early discussion back in Section 1.3 that time-division multiplexing (TDM) and frequency-division multiplexing (FDM) are two techniques that can
 
 ![Alt text](image-8.png)
**Figure 6.9**  ♦  A four-node TDM and FDM example
 
 be used to partition a broadcast channel’s bandwidth among all nodes sharing that channel. As an example, suppose the channel supports _N_ nodes and that the trans- mission rate of the channel is _R_ bps. TDM divides time into **time frames** and further divides each time frame into _N_ **time slots**. (The TDM time frame should not be confused with the link-layer unit of data exchanged between sending and receiving adapters, which is also called a frame. In order to reduce confusion, in this subsec- tion we’ll refer to the link-layer unit of data exchanged as a packet.) Each time slot is then assigned to one of the _N_ nodes. Whenever a node has a packet to send, it transmits the packet’s bits during its assigned time slot in the revolving TDM frame. Typically, slot sizes are chosen so that a single packet can be transmitted during a slot time. Figure 6.9 shows a simple four-node TDM example. Returning to our cocktail party analogy, a TDM-regulated cocktail party would allow one partygoer to speak for a fixed period of time, then allow another partygoer to speak for the same amount of time, and so on. Once everyone had had a chance to talk, the pattern would repeat.

TDM is appealing because it eliminates collisions and is perfectly fair: Each node gets a dedicated transmission rate of _R_/_N_ bps during each frame time. However, it has two major drawbacks. First, a node is limited to an average rate of _R_/_N_ bps even when it is the only node with packets to send. A second drawback is that a node must always wait for its turn in the transmission sequence—again, even when it is the only node with a frame to send. Imagine the partygoer who is the only one with anything to say (and imagine that this is the even rarer circumstance where everyone

wants to hear what that one person has to say). Clearly, TDM would be a poor choice for a multiple access protocol for this particular party.

While TDM shares the broadcast channel in time, FDM divides the _R_ bps chan- nel into different frequencies (each with a bandwidth of _R_/_N_) and assigns each fre- quency to one of the _N_ nodes. FDM thus creates _N_ smaller channels of _R_/_N_ bps out of the single, larger _R_ bps channel. FDM shares both the advantages and drawbacks of TDM. It avoids collisions and divides the bandwidth fairly among the _N_ nodes. However, FDM also shares a principal disadvantage with TDM—a node is limited to a bandwidth of _R_/_N_, even when it is the only node with packets to send.

A third channel partitioning protocol is **code division multiple access (CDMA)**. While TDM and FDM assign time slots and frequencies, respectively, to the nodes, CDMA assigns a different _code_ to each node. Each node then uses its unique code to encode the data bits it sends. If the codes are chosen carefully, CDMA networks have the wonderful property that different nodes can transmit _simultaneously_ and yet have their respective receivers correctly receive a send- er’s encoded data bits (assuming the receiver knows the sender’s code) in spite of interfering transmissions by other nodes. CDMA has been used in military systems for some time (due to its anti-jamming properties) and now has wide- spread civilian use, particularly in cellular telephony. Because CDMA’s use is so tightly tied to wireless channels, we’ll save our discussion of the technical details of CDMA until Chapter 7. For now, it will suffice to know that CDMA codes, like time slots in TDM and frequencies in FDM, can be allocated to the multiple access channel users.

### Random Access Protocols
 The second broad class of multiple access protocols are random access protocols. In a random access protocol, a transmitting node always transmits at the full rate of the channel, namely, _R_ bps. When there is a collision, each node involved in the collision repeatedly retransmits its frame (that is, packet) until its frame gets through without a collision. But when a node experiences a collision, it doesn’t necessarily retransmit the frame right away. _Instead it waits a random delay before retrans- mitting the frame_. Each node involved in a collision chooses independent random delays. Because the random delays are independently chosen, it is possible that one of the nodes will pick a delay that is sufficiently less than the delays of the other col- liding nodes and will therefore be able to sneak its frame into the channel without a collision.

There are dozens if not hundreds of random access protocols described in the literature [Rom 1990; Bertsekas 1991]. In this section we’ll describe a few of the most commonly used random access protocols—the ALOHA protocols [Abram- son 1970; Abramson 1985; Abramson 2009] and the carrier sense multiple access (CSMA) protocols [Kleinrock 1975b]. Ethernet [Metcalfe 1976] is a popular and widely deployed CSMA protocol.

**Slotted ALOHA**
Let’s begin our study of random access protocols with one of the simplest random access protocols, the slotted ALOHA protocol. In our description of slotted ALOHA, we assume the following:

• All frames consist of exactly _L_ bits.

• Time is divided into slots of size _L_/_R_ seconds (that is, a slot equals the time to transmit one frame).

• Nodes start to transmit frames only at the beginnings of slots.

• The nodes are synchronized so that each node knows when the slots begin.

• If two or more frames collide in a slot, then all the nodes detect the collision event before the slot ends.

Let _p_ be a probability, that is, a number between 0 and 1. The operation of slotted ALOHA in each node is simple:

• When the node has a fresh frame to send, it waits until the beginning of the next slot and transmits the entire frame in the slot.

• If there isn’t a collision, the node has successfully transmitted its frame and thus need not consider retransmitting the frame. (The node can prepare a new frame for transmission, if it has one.)

• If there is a collision, the node detects the collision before the end of the slot. The node retransmits its frame in each subsequent slot with probability _p_ until the frame is transmitted without a collision.

By retransmitting with probability _p_, we mean that the node effectively tosses a biased coin; the event heads corresponds to “retransmit,” which occurs with prob- ability _p_. The event tails corresponds to “skip the slot and toss the coin again in the next slot”; this occurs with probability (1 - _p_). All nodes involved in the collision toss their coins independently.

Slotted ALOHA would appear to have many advantages. Unlike channel par- titioning, slotted ALOHA allows a node to transmit continuously at the full rate, _R_, when that node is the only active node. (A node is said to be active if it has frames to send.) Slotted ALOHA is also highly decentralized, because each node detects collisions and independently decides when to retransmit. (Slotted ALOHA does, however, require the slots to be synchronized in the nodes; shortly we’ll discuss an unslotted version of the ALOHA protocol, as well as CSMA protocols, none of which require such synchronization.) Slotted ALOHA is also an extremely simple protocol.

Slotted ALOHA works well when there is only one active node, but how efficient is it when there are multiple active nodes? There are two possible efficiency

![Alt text](image-9.png)
**Figure 6.10**  ♦   Nodes 1, 2, and 3 collide in the first slot. Node 2 finally succeeds in the fourth slot, node 1 in the eighth slot, and node 3 in the ninth slot

concerns here. First, as shown in Figure 6.10, when there are multiple active nodes, a certain fraction of the slots will have collisions and will therefore be “wasted.” The second concern is that another fraction of the slots will be _empty_ because all active nodes refrain from transmitting as a result of the probabilistic transmission policy. The only “unwasted” slots will be those in which exactly one node transmits. A slot in which exactly one node transmits is said to be a **successful slot**. The **efficiency** of a slotted multiple access protocol is defined to be the long-run fraction of successful slots in the case when there are a large number of active nodes, each always having a large number of frames to send. Note that if no form of access control were used, and each node were to immediately retransmit after each collision, the efficiency would be zero. Slotted ALOHA clearly increases the efficiency beyond zero, but by how much?

We now proceed to outline the derivation of the maximum efficiency of slotted ALOHA. To keep this derivation simple, let’s modify the protocol a little and assume that each node attempts to transmit a frame in each slot with probability _p_. (That is, we assume that each node always has a frame to send and that the node transmits with probability _p_ for a fresh frame as well as for a frame that has already suffered a collision.) Suppose there are _N_ nodes. Then the probability that a given slot is a suc- cessful slot is the probability that one of the nodes transmits and that the remaining _N_ \- 1 nodes do not transmit. The probability that a given node transmits is _p;_ the probability that the remaining nodes do not transmit is (1 - _p_)_N_\-1. Therefore, the probability a given node has a success is _p_(1 - _p_)_N_\-1. Because there are _N_ nodes, the probability that any one of the _N_ nodes has a success is _Np_(1 - _p_)_N_\-1.

Thus, when there are _N_ active nodes, the efficiency of slotted ALOHA is _Np_(1 - _p_)_N_\-1. To obtain the _maximum_ efficiency for _N_ active nodes, we have to find the _p_\* that maximizes this expression. (See the homework problems for a general outline of this derivation.) And to obtain the maximum efficiency for a large number of active nodes, we take the limit of _Np_\*(1 - _p_\*)_N_\-1 as _N_ approaches infinity. (Again, see the homework problems.) After performing these calculations, we’ll find that the maximum efficiency of the protocol is given by 1/_e_ = 0.37. That is, when a large number of nodes have many frames to transmit, then (at best) only 37 percent of the slots do useful work. Thus, the effective transmission rate of the channel is not _R_ bps but only 0.37 _R_ bps! A similar analysis also shows that 37 percent of the slots go empty and 26 percent of slots have collisions. Imagine the poor network administrator who has purchased a 100-Mbps slotted ALOHA system, expecting to be able to use the network to transmit data among a large number of users at an aggregate rate of, say, 80 Mbps! Although the channel is capable of transmitting a given frame at the full channel rate of 100 Mbps, in the long run, the successful throughput of this channel will be less than 37 Mbps.

**ALOHA**

The slotted ALOHA protocol required that all nodes synchronize their transmissions to start at the beginning of a slot. The first ALOHA protocol [Abramson 1970] was actually an unslotted, fully decentralized protocol. In pure ALOHA, when a frame first arrives (that is, a network-layer datagram is passed down from the network layer at the sending node), the node immediately transmits the frame in its entirety into the broadcast channel. If a transmitted frame experiences a collision with one or more other transmissions, the node will then immediately (after completely transmitting its collided frame) retransmit the frame with probability _p_. Otherwise, the node waits for a frame transmission time. After this wait, it then transmits the frame with prob- ability _p_, or waits (remaining idle) for another frame time with probability 1 – _p_.

To determine the maximum efficiency of pure ALOHA, we focus on an individual node. We’ll make the same assumptions as in our slotted ALOHA analysis and take the frame transmission time to be the unit of time. At any given time, the probability that a node is transmitting a frame is _p_. Suppose this frame begins transmission at time _t_0\. As shown in Figure 6.11, in order for this frame to be successfully transmitted, no other nodes can begin their transmission in the interval of time [_t_0 - 1, _t_0]. Such a transmis- sion would overlap with the beginning of the transmission of node _i_’s frame. The prob- ability that all other nodes do not begin a transmission in this interval is (1 - _p_)_N_\-1. Similarly, no other node can begin a transmission while node _i_ is transmitting, as such a transmission would overlap with the latter part of node _i_’s transmission. The probabil- ity that all other nodes do not begin a transmission in this interval is also (1 - _p_)_N_\-1. Thus, the probability that a given node has a successful transmission is _p_(1 - _p_)2(_N_\-1). By taking limits as in the slotted ALOHA case, we find that the maximum efficiency of the pure ALOHA protocol is only 1/(2_e_)—exactly half that of slotted ALOHA. This then is the price to be paid for a fully decentralized ALOHA protocol.

![Alt text](image-10.png)
**Figure 6.11**  ♦  Interfering transmissions in pure ALOHA

**Carrier Sense Multiple Access (CSMA)**

In both slotted and pure ALOHA, a node’s decision to transmit is made indepen- dently of the activity of the other nodes attached to the broadcast channel. In particu- lar, a node neither pays attention to whether another node happens to be transmitting when it begins to transmit, nor stops transmitting if another node begins to interfere with its transmission. In our cocktail party analogy, ALOHA protocols are quite like a boorish partygoer who continues to chatter away regardless of whether other people are talking. As humans, we have human protocols that allow us not only to behave with more civility, but also to decrease the amount of time spent “colliding” with each other in conversation and, consequently, to increase the amount of data we exchange in our conversations. Specifically, there are two important rules for polite human conversation:

• _Listen before speaking._ If someone else is speaking, wait until they are finished. In the networking world, this is called **carrier sensing**—a node listens to the channel before transmitting. If a frame from another node is currently being trans- mitted into the channel, a node then waits until it detects no transmissions for a short amount of time and then begins transmission.

• _If someone else begins talking at the same time, stop talking._ In the network- ing world, this is called **collision detection**—a transmitting node listens to the channel while it is transmitting. If it detects that another node is transmitting an interfering frame, it stops transmitting and waits a random amount of time before repeating the sense-and-transmit-when-idle cycle.

These two rules are embodied in the family of **carrier sense multiple access (CSMA)** and **CSMA with collision detection (CSMA/CD)** protocols [Kleinrock 1975b; Metcalfe 1976; Lam 1980; Rom 1990]. Many variations on CSMA and


CSMA/CD have been proposed. Here, we’ll consider a few of the most important, and fundamental, characteristics of CSMA and CSMA/CD.

The first question that you might ask about CSMA is why, if all nodes perform carrier sensing, do collisions occur in the first place? After all, a node will refrain from transmitting whenever it senses that another node is transmitting. The answer to the question can best be illustrated using space-time diagrams [Molle 1987]. Figure 6.12 shows a space-time diagram of four nodes (A, B, C, D) attached to a linear broadcast bus. The horizontal axis shows the position of each node in space; the vertical axis represents time.

At time _t_0, node B senses the channel is idle, as no other nodes are currently trans- mitting. Node B thus begins transmitting, with its bits propagating in both directions along the broadcast medium. The downward propagation of B’s bits in Figure 6.12 with increasing time indicates that a nonzero amount of time is needed for B’s bits actually to propagate (albeit at near the speed of light) along the broadcast medium. At time _t_1 (_t_1 7 _t_0), node D has a frame to send. Although node B is currently transmit- ting at time _t_1, the bits being transmitted by B have yet to reach D, and thus D senses

![Alt text](image-11.png)
**Figure 6.12**  ♦   Space-time diagram of two CSMA nodes with colliding transmissions

the channel idle at _t_1\. In accordance with the CSMA protocol, D thus begins transmit- ting its frame. A short time later, B’s transmission begins to interfere with D’s trans- mission at D. From Figure 6.12, it is evident that the end-to-end **channel propagation delay** of a broadcast channel—the time it takes for a signal to propagate from one of the nodes to another—will play a crucial role in determining its performance. The longer this propagation delay, the larger the chance that a carrier-sensing node is not yet able to sense a transmission that has already begun at another node in the network.

**Carrier Sense Multiple Access with Collision Detection (CSMA/CD)**

In Figure 6.12, nodes do not perform collision detection; both B and D continue to transmit their frames in their entirety even though a collision has occurred. When a node performs collision detection, it ceases transmission as soon as it detects a col- lision. Figure 6.13 shows the same scenario as in Figure 6.12, except that the two

![Alt text](image-12.png)
**Figure 6.13**  ♦  CSMA with collision detection

nodes each abort their transmission a short time after detecting a collision. Clearly, adding collision detection to a multiple access protocol will help protocol perfor- mance by not transmitting a useless, damaged (by interference with a frame from another node) frame in its entirety.

Before analyzing the CSMA/CD protocol, let us now summarize its operation from the perspective of an adapter (in a node) attached to a broadcast channel:

1\. The adapter obtains a datagram from the network layer, prepares a link-layer frame, and puts the frame adapter buffer.

2\. If the adapter senses that the channel is idle (that is, there is no signal energy entering the adapter from the channel), it starts to transmit the frame. If, on the other hand, the adapter senses that the channel is busy, it waits until it senses no signal energy and then starts to transmit the frame.

3\. While transmitting, the adapter monitors for the presence of signal energy coming from other adapters using the broadcast channel.

4\. If the adapter transmits the entire frame without detecting signal energy from other adapters, the adapter is finished with the frame. If, on the other hand, the adapter detects signal energy from other adapters while transmitting, it aborts the transmission (that is, it stops transmitting its frame).

5\. After aborting, the adapter waits a random amount of time and then returns to step 2.

The need to wait a random (rather than fixed) amount of time is hopefully clear—if two nodes transmitted frames at the same time and then both waited the same fixed amount of time, they’d continue colliding forever. But what is a good interval of time from which to choose the random backoff time? If the interval is large and the number of colliding nodes is small, nodes are likely to wait a large amount of time (with the channel remaining idle) before repeating the sense-and-transmit-when- idle step. On the other hand, if the interval is small and the number of colliding nodes is large, it’s likely that the chosen random values will be nearly the same, and transmitting nodes will again collide. What we’d like is an interval that is short when the number of colliding nodes is small, and long when the number of colliding nodes is large.

The **binary exponential backoff** algorithm, used in Ethernet as well as in DOC- SIS cable network multiple access protocols [DOCSIS 3.1 2014], elegantly solves this problem. Specifically, when transmitting a frame that has already experienced _n_ collisions, a node chooses the value of _K_ at random from {0,1,2, . . . . 2_n_\-1}. Thus, the more collisions experienced by a frame, the larger the interval from which _K_ is chosen. For Ethernet, the actual amount of time a node waits is K # 512 bit times (i.e., _K_ times the amount of time needed to send 512 bits into the Ethernet) and the maxi- mum value that _n_ can take is capped at 10.

Let’s look at an example. Suppose that a node attempts to transmit a frame for the first time and while transmitting it detects a collision. The node then chooses _K_ = 0 with probability 0.5 or chooses _K_ \= 1 with probability 0.5. If the node chooses _K_ = 0, then it immediately begins sensing the channel. If the node chooses _K_ = 1, it waits 512 bit times (e.g., 5.12 microseconds for a 100 Mbps Ethernet) before beginning the sense-and-transmit-when-idle cycle. After a second collision, _K_ is chosen with equal probability from {0,1,2,3}. After three collisions, _K_ is chosen with equal probability from {0,1,2,3,4,5,6,7}. After 10 or more collisions, _K_ is cho- sen with equal probability from {0,1,2, . . . , 1023}. Thus, the size of the sets from which _K_ is chosen grows exponentially with the number of collisions; for this reason this algorithm is referred to as binary exponential backoff.

We also note here that each time a node prepares a new frame for transmission, it runs the CSMA/CD algorithm, not taking into account any collisions that may have occurred in the recent past. So it is possible that a node with a new frame will immediately be able to sneak in a successful transmission while several other nodes are in the exponential backoff state.

**CSMA/CD Efficiency**
When only one node has a frame to send, the node can transmit at the full channel rate (e.g., for Ethernet typical rates are 10 Mbps, 100 Mbps, or 1 Gbps). However, if many nodes have frames to transmit, the effective transmission rate of the channel can be much less. We define the **efficiency of CSMA/CD** to be the long-run fraction of time during which frames are being transmitted on the channel without collisions when there is a large number of active nodes, with each node having a large number of frames to send. In order to present a closed-form approximation of the efficiency of Ethernet, let _d_prop denote the maximum time it takes signal energy to propagate between any two adapters. Let _d_trans be the time to transmit a maximum-size frame (approximately 1.2 msecs for a 10 Mbps Ethernet). A derivation of the efficiency of CSMA/CD is beyond the scope of this book (see [Lam 1980] and [Bertsekas 1991]). Here we simply state the following approximation:

Efficiency = 1/1 + 5 d prop /d trans

We see from this formula that as _d_prop approaches 0, the efficiency approaches 1. This matches our intuition that if the propagation delay is zero, colliding nodes will abort immediately without wasting the channel. Also, as _d_trans becomes very large, efficiency approaches 1. This is also intuitive because when a frame grabs the chan- nel, it will hold on to the channel for a very long time; thus, the channel will be doing productive work most of the time.

### Taking-Turns Protocols
Recall that two desirable properties of a multiple access protocol are (1) when only one node is active, the active node has a throughput of _R_ bps, and (2) when _M_ nodes are active, then each active node has a throughput of nearly _R_/_M_ bps. The ALOHA and CSMA protocols have this first property but not the second. This has motivated researchers to create another class of protocols—the **taking-turns protocols**. As with random access protocols, there are dozens of taking-turns protocols, and each one of these protocols has many variations. We’ll discuss two of the more important protocols here. The first one is the **polling protocol**. The polling protocol requires one of the nodes to be designated as a master node. The master node polls
each of the nodes in a round-robin fashion. In particular, the master node first sends a message to node 1, saying that it (node 1) can transmit up to some maximum number of frames. After node 1 transmits some frames, the master node tells node 2 it (node 2) can transmit up to the maximum number of frames. (The master node can determine when a node has finished sending its frames by observing the lack of a signal on the channel.) The procedure con- tinues in this manner, with the master node polling each of the nodes in a cyclic manner.

The polling protocol eliminates the collisions and empty slots that plague ran- dom access protocols. This allows polling to achieve a much higher efficiency. Butit also has a few drawbacks. The first drawback is that the protocol introduces a polling delay—the amount of time required to notify a node that it can transmit. If, for example, only one node is active, then the node will transmit at a rate less than _R_ bps, as the master node must poll each of the inactive nodes in turn each time the active node has sent its maximum number of frames. The second drawback, which is potentially more serious, is that if the master node fails, the entire channel becomes inoperative. The Bluetooth protocol, which we will study in Section 6.3, is an exam- ple of a polling protocol.

The second taking-turns protocol is the **token-passing protocol**. In this pro- tocol there is no master node. A small, special-purpose frame known as a **token** is exchanged among the nodes in some fixed order. For example, node 1 might always send the token to node 2, node 2 might always send the token to node 3, and node _N_ might always send the token to node 1. When a node receives a token, it holds onto the token only if it has some frames to transmit; otherwise, it immediately for- wards the token to the next node. If a node does have frames to transmit when it receives the token, it sends up to a maximum number of frames and then forwards the token to the next node. Token passing is decentralized and highly efficient. But it has its problems as well. For example, the failure of one node can crash the entire channel. Or if a node accidentally neglects to release the token, then some recovery procedure must be invoked to get the token back in circulation. Over the years many token-passing protocols have been developed, including the fiber distributed data interface (FDDI) protocol [Jain 1994] and the IEEE 802.5 token ring protocol [IEEE 802.5 2012], and each one had to address these as well as other sticky issues.

### DOCSIS: The Link-Layer Protocol for Cable Internet Access
In the previous three subsections, we’ve learned about three broad classes of mul- tiple access protocols: channel partitioning protocols, random access protocols, and taking turns protocols. A cable access network will make for an excellent case study here, as we’ll find aspects of _each_ of these three classes of multiple access protocols with the cable access network!

Recall from Section 1.2.1 that a cable access network typically connects several thousand residential cable modems to a cable modem termination system (CMTS) at the cable network headend. The Data-Over-Cable Service Interface Specifica- tions (DOCSIS) [DOCSIS 3.1 2014; Hamzeh 2015] specifies the cable data network architecture and its protocols. DOCSIS uses FDM to divide the downstream (CMTS to modem) and upstream (modem to CMTS) network segments into multiple fre- quency channels. Each downstream channel is between 24 MHz and 192 MHz wide, with a maximum throughput of approximately 1.6 Gbps per channel; each upstream channel has channel widths ranging from 6.4 MHz to 96 MHz, with a maximum upstream throughput of approximately 1 Gbps. Each upstream and downstream

![Alt text](image-13.png)
**Figure 6.14**  ♦   Upstream and downstream channels between CMTS and cable modems

channel is a broadcast channel. Frames transmitted on the downstream channel by the CMTS are received by all cable modems receiving that channel; since there is just a single CMTS transmitting into the downstream channel, however, there is no multiple access problem. The upstream direction, however, is more interesting and technically challenging, since multiple cable modems share the same upstream chan- nel (frequency) to the CMTS, and thus collisions can potentially occur.

As illustrated in Figure 6.14, each upstream channel is divided into intervals of time (TDM-like), each containing a sequence of mini-slots during which cable modems can transmit to the CMTS. The CMTS explicitly grants permission to indi- vidual cable modems to transmit during specific mini-slots. The CMTS accomplishes this by sending a control message known as a MAP message on a downstream chan- nel to specify which cable modem (with data to send) can transmit during which mini-slot for the interval of time specified in the control message. Since mini-slots are explicitly allocated to cable modems, the CMTS can ensure there are no colliding transmissions during a mini-slot.

But how does the CMTS know which cable modems have data to send in the first place? This is accomplished by having cable modems send mini-slot-request frames to the CMTS during a special set of interval mini-slots that are dedicated for this purpose, as shown in Figure 6.14. These mini-slot-request frames are transmit- ted in a random access manner and so may collide with each other. A cable modem can neither sense whether the upstream channel is busy nor detect collisions. Instead, the cable modem infers that its mini-slot-request frame experienced a collision if it does not receive a response to the requested allocation in the next downstream con- trol message. When a collision is inferred, a cable modem uses binary exponential

backoff to defer the retransmission of its mini-slot-request frame to a future time 
slot. When there is little traffic on the upstream channel, a cable modem may actually 
transmit data frames during slots nominally assigned for mini-slot-request frames 
(and thus avoid having to wait for a mini-slot assignment).

A cable access network thus serves as a terrific example of multiple access pro- tocols in action—FDM, TDM, random access, and centrally allocated time slots all within one network!
