---
title: 'Evolution of Transport-Layer Functionality'
weight: 8
references:
    videos:
        - youytube:oRUIEmpzk0Y
        - youytube:dXh5mN59wL0
    links:
        - https://notes.bweston.uk/comp211/lectures/2021/11/10/3.html
        - https://en.wikipedia.org/wiki/Transport_layer
    books:
        - b1:
            title: Advanced Transport Protocols
            url: https://www.google.co.in/books/edition/Advanced_Transport_Protocols/bgvfooFV9nEC?hl=en&gbpv=0
        - b2:
            title: Evolution of Wireless Communication Ecosystems
            url: https://www.google.co.in/books/edition/Evolution_of_Wireless_Communication_Ecos/xS3BEAAAQBAJ?hl=en&gbpv=0
---

## Evolution of Transport-Layer Functionality
Our discussion of specific Internet transport protocols in this chapter has focused on UDP and TCP—the two “work horses” of the Internet transport layer. However, as we’ve seen, three decades of experience with these two protocols has identified cir- cumstances in which neither is ideally suited, and so the design and implementation of transport layer functionality has continued to evolve.

We’ve seen a rich evolution in the use of TCP over the past decade. In Sections 3.7.1 and 3.7.2, we learned that in addition to “classic” versions of TCP such as TCP Tahoe and Reno, there are now several newer versions of TCP that have been developed, implemented, deployed, and are in significant use today. These include TCP CUBIC, DCTCP, CTCP, BBR, and more. Indeed, measurements in [Yang 2014] indicate that CUBIC (and its predecessor, BIC [Xu 2004\) and CTCP are more widely deployed on Web servers than classic TCP Reno; we also saw that BBR is being deployed in Google’s internal B4 network, as well as on many of Google’s public-facing servers.

And there are many (many!) more versions of TCP! There are versions of TCP specifically designed for use over wireless links, over high-bandwidth paths with large RTTs, for paths with packet re-ordering, and for short paths strictly within data centers. There are versions of TCP that implement different priorities among TCPconnections competing for bandwidth at a bottleneck link, and for TCP connections whose segments are being sent over different source-destination paths in parallel. There are also variations of TCP that deal with packet acknowledgment and TCP session establishment/closure differently than we studied in Section 3.5.6. Indeed, it’s probably not even correct anymore to refer to “the” TCP protocol; perhaps the _only_ common features of these protocols is that they use the TCP segment format that we studied in Figure 3.29, and that they should compete “fairly” amongst themselves in the face of network congestion! For a survey of the many flavors of TCP, see [Afanasyev 2010\ and [Narayan 2018].

**QUIC: Quick UDP Internet Connections**

If the transport services needed by an application don’t quite fit either the UDP or TCP service models—perhaps an application needs more services than those provided by UDP but does not want all of the particular functionality that comes with TCP, or may want different services than those provided by TCP—applica- tion designers can always “roll their own” protocol at the application layer. This is the approach taken in the QUIC (Quick UDP Internet Connections) protocol \[Langley 2017, QUIC 2020\]. Specifically, QUIC is a new application-layer pro- tocol designed from the ground up to improve the performance of transport-layer services for secure HTTP. QUIC has already been widely deployed, although is still in the process of being standardized as an Internet RFC \[QUIC 2020\]. Google has deployed QUIC on many of its public-facing Web servers, in its mobile video streaming YouTube app, in its Chrome browser, and in Android’s Google Search app. With more than 7% of Internet traffic today now being QUIC \[Langley 2017\], we’ll want to take a closer look. Our study of QUIC will also serve as a nice culmi- nation of our study of the transport layer, as QUIC uses many of the approaches for reliable data transfer, congestion control, and connection management that we’ve studied in this chapter.

As shown in Figure 3.58, QUIC is an application-layer protocol, using UDP as its underlying transport-layer protocol, and is designed to interface above specifi- cally to a simplified but evolved version of HTTP/2. In the near future, HTTP/3 will natively incorporate QUIC [HTTP/3 2020]. Some of QUIC’s major features include:

• **Connection-Oriented and Secure.** Like TCP, QUIC is a connection-oriented protocol between two endpoints. This requires a handshake between endpoints to set up the QUIC connection state. Two pieces of connection state are the source and destination connection ID. All QUIC packets are encrypted, and as suggested in Figure 3.58, QUIC combines the handshakes needed to establish connection state with those needed for authentication and encryption (transport layer security topics that we’ll study in Chapter 8), thus providing faster estab- lishment than the protocol stack in Figure 3.58(a), where multiple RTTs are
![Alt text](image-58.png)
**Figure 3.58**  ♦   (a) traditional secure HTTP protocol stack, and the (b) secure QUIC-based HTTP/3 protocol stack

required to first establish a TCP connection, and then establish a TLS connection over the TCP connection.

• **Streams.** QUIC allows several different application-level “streams” to be mul- tiplexed through a single QUIC connection, and once a QUIC connection is established, new streams can be quickly added. A stream is an abstraction for the reliable, in-order bi-directional delivery of data between two QUIC endpoints. In the context of HTTP/3, there would be a different stream for each object in a Web page. Each connection has a connection ID, and each stream within a connection has a stream ID; both of these IDs are contained in a QUIC packet header (along with other header information). Data from multiple streams may be contained within a single QUIC segment, which is carried over UDP. The Stream Control Transmission Protocol (SCTP) \[RFC 4960, RFC 3286\] is an earlier reliable, mes- sage-oriented protocol that pioneered the notion of multiplexing multiple appli- cation-level “streams” through a single SCTP connection. We’ll see in Chapter 7 that SCTP is used in control plane protocols in 4G/5G cellular wireless networks.

• **Reliable, TCP-friendly congestion-controlled data transfer.** As illustrated in Figure 3.59(b), QUIC provides reliable data transfer to each QUIC stream _separately_. Figure 3.59(a) shows the case of HTTP/1.1 sending multiple HTTP requests, all over a single TCP connection. Since TCP provides reliable, in-order byte delivery, this means that the multiple HTTP requests must be delivered in- order at the destination HTTP server. Thus, if bytes from one HTTP request are lost, the remaining HTTP requests can not be delivered until those lost bytes are retransmitted and correctly received by TCP at the HTTP server—the so-called HOL blocking problem that we encountered earlier in Section 2.2.5. Since QUIC provides a reliable in-order delivery on a _per-stream_ basis, a lost UDP segment only impacts those streams whose data was carried in that segment; HTTP mes- sages in other streams can continue to be received and delivered to the applica- tion. QUIC provides reliable data transfer using acknowledgment mechanisms similar to TCP’s, as specified in \[RFC 5681\].

![Alt text](image-59.png)
**Figure 3.59** ♦ (a) HTTP/1.1: a single-connection client and server using application-level TLS encryption over TCP’s reliable data transfer (RDT) and congestion control (CC) (b) HTTP/3: a multi-stream client and server using QUIC’s encryption, reliable data transfer and congestion control over UDP’s unreliable datagram

QUIC’s congestion control is based on TCP NewReno \[RFC 6582\], a slight modification to the TCP Reno protocol that we studied in Section 3.7.1. QUIC’s Draft specification \[QUIC-recovery 2020\] notes “Readers familiar with TCP’s loss detection and congestion control will find algorithms here that parallel well- known TCP ones.” Since we’ve carefully studied TCP’s congestion control in Section 3.7.1, we’d be right at home reading the details of QUIC’s draft specifica- tion of its congestion control algorithm!

In closing, it’s worth highlighting again that QUIC is an _application-layer_ protocol providing reliable, congestion-controlled data transfer between two endpoints. The authors of QUIC [Langley 2017\] stress that this means that changes can be made to QUIC at “application-update timescales,” that is, much faster than TCP or UDP update timescales.

# Summary
We began this chapter by studying the services that a transport-layer protocol can provide to network applications. At one extreme, the transport-layer protocol can be very simple and offer a no-frills service to applications, providing only a multiplexing/ demultiplexing function for communicating processes. The Internet’s UDP protocol serviceis an example of such a no-frills transport-layer protocol. At the other extreme, a transport-layer protocol can provide a variety of guarantees to applications, such as reliable delivery of data, delay guarantees, and bandwidth guarantees. Nevertheless, the services that a transport protocol can provide are often constrained by the service model of the underlying network-layer protocol. If the network-layer protocol cannot provide delay or bandwidth guarantees to transport-layer segments, then the transport- layer protocol cannot provide delay or bandwidth guarantees for the messages sent between processes.

We learned in Section 3.4 that a transport-layer protocol can provide reliable data transfer even if the underlying network layer is unreliable. We saw that provid- ing reliable data transfer has many subtle points, but that the task can be accom- plished by carefully combining acknowledgments, timers, retransmissions, and sequence numbers.

Although we covered reliable data transfer in this chapter, we should keep in mind that reliable data transfer can be provided by link-, network-, transport-, or application-layer protocols. Any of the upper four layers of the protocol stack can implement acknowledgments, timers, retransmissions, and sequence numbers and provide reliable data transfer to the layer above. In fact, over the years, engineers and computer scientists have independently designed and implemented link-, network-, transport-, and application-layer protocols that provide reliable data transfer (although many of these protocols have quietly disappeared).
