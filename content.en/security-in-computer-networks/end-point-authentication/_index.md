---
title: 'End-Point Authentication'
weight: 4
references:
    videos:
        - youtube:91GMK_k7HUM
        - youtube:DeiX19SIPh8
    links:
        - https://www.techopedia.com/definition/23918/endpoint-authentication
        - https://www.spiceworks.com/it-security/network-security/articles/what-is-endpoint-encryption/
    books:
        - b1:
            title: Endpoint Security
            url: https://www.google.co.in/books/edition/Endpoint_Security/oBgNPOk2kw8C?hl=en&gbpv=0
        - b2:
            title: Emerging Technologies for Authorization and Authentication
            url: https://www.google.co.in/books/edition/Emerging_Technologies_for_Authorization/0DZZEAAAQBAJ?hl=en&gbpv=0
---

## End-Point Authentication

**End-point authentication** is the process of one entity proving its identity to another entity over a computer network, for example, a user proving its identity to an e-mail server. As humans, we authenticate each other in many ways: We recognize each other’s faces when we meet, we recognize each other’s voices on the telephone, we are authenticated by the customs official who checks us against the picture on our passport.

In this section, we consider how one party can authenticate another party when the two are communicating over a network. We focus here on authenticating a “live” party, at the point in time when communication is actually occurring. A concrete example is a user authenticating him or herself to an e-mail server. This is a subtly different problem from proving that a message received at some point in the past did indeed come from that claimed sender, as studied in Section 8.3.

When performing authentication over the network, the communicating parties cannot rely on biometric information, such as a visual appearance or a voiceprint. Indeed, we will see in our later case studies that it is often network elements such as routers and client/server processes that must authenticate each other. Here, authen- tication must be done solely on the basis of messages and data exchanged as part of an **authentication protocol** Typically, an authentication protocol would run _before_ the two communicating parties run some other protocol (for example, a reliable data transfer protocol, a routing information exchange protocol, or an e-mail protocol). The authentication protocol first establishes the identities of the parties to each other’s satisfaction; only after authentication do the parties get down to the work at hand.

As in the case of our development of a reliable data transfer (rdt) protocol in Chapter 3, we will find it instructive here to develop various versions of an authentica- tion protocol, which we will call **ap** (authentication protocol), and poke holes in each version as we proceed. (If you enjoy this stepwise evolution of a design, you might also enjoy [Bryant 1988], which recounts a fictitious narrative between designers of an open- network authentication system, and their discovery of the many subtle issues involved.)

Let’s assume that Alice needs to authenticate herself to Bob. Perhaps the simplest authentication protocol we can imagine is one where

Alice simply sends a message to Bob saying she is Alice. This protocol is shown in Figure 8.15. The flaw here is obvious—there is no way for Bob actually to know that the person sending the message “I am Alice” is indeed Alice. For example, Trudy (the intruder) could just as well send such a message.
![Alt text](image-18.png)
**Figure 8.15**  ♦  Protocol _ap1.0_ and a failure scenario

**Authentication Protocol _ap2.0_**

If Alice has a well-known network address (e.g., an IP address) from which she always communicates, Bob could attempt to authenticate Alice by verifying that the source address on the IP datagram carrying the authentication message matches Alice’s well-known address. In this case, Alice would be authenticated. This might stop a very network-naive intruder from impersonating Alice, but it wouldn’t stop the determined student studying this book, or many others!

From our study of the network and data link layers, we know that it is not that hard (for example, if one had access to the operating system code and could build one’s own operating system kernel, as is the case with Linux and several other freely available operating systems) to create an IP datagram, put whatever IP source address we want (for example, Alice’s well-known IP address) into the IP datagram, and send the datagram over the link-layer protocol to the first-hop router. From then on, the incorrectly source-addressed datagram would be dutifully forwarded to Bob. This approach, shown in Figure 8.16, is a form of IP spoofing. IP spoofing can be avoided if Trudy’s first-hop router is configured to forward only datagrams con- taining Trudy’s IP source address [RFC 2827]. However, this capability is not uni- versally deployed or enforced. Bob would thus be foolish to assume that Trudy’s network manager (who might be Trudy herself) had configured Trudy’s first-hop router to forward only appropriately addressed datagrams.

**Authentication Protocol _ap3.0_**

One classic approach to authentication is to use a secret password. The password is a shared secret between the authenticator and the person being authenticated. Gmail, Facebook, telnet, FTP, and many other services use password authentication. In pro- tocol ap3.0, Alice thus sends her secret password to Bob, as shown in Figure 8.17.
![Alt text](image-19.png)
**Figure 8.16**  ♦  Protocol _ap2.0_ and a failure scenario
![Alt text](image-50.png)
**Figure 8.17** ♦ Protocol ap3.0 and a failure scenario

Since passwords are so widely used, we might suspect that protocol _ap3.0_ is fairly secure. If so, we’d be wrong! The security flaw here is clear. If Trudy eaves- drops on Alice’s communication, then she can learn Alice’s password. Lest you think this is unlikely, consider the fact that when you Telnet to another machine and log in, the login password is sent unencrypted to the Telnet server. Someone connected to the Telnet client or server’s LAN can possibly sniff (read and store) all packets transmitted on the LAN and thus steal the login password. In fact, this is a well- known approach for stealing passwords (see, for example, [Jimenez 1997]). Such a threat is obviously very real, so _ap3.0_ clearly won’t do.

**Authentication Protocol ap3.1**

Our next idea for fixing ap3.0 is naturally to encrypt the password. By encrypting the password, we can prevent Trudy from learning Alice’s password. If we assume that Alice and Bob share a symmetric secret key, _KA_\-_B_, then Alice can encrypt the password and send her identification message, “I am Alice,” and her encrypted password to Bob. Bob then decrypts the password and, assuming the password is cor- rect, authenticates Alice. Bob feels comfortable in authenticating Alice since Alice not only knows the password, but also knows the shared secret key value needed to encrypt the password. Let’s call this protocol _ap3.1_.

While it is true that _ap3.1_ prevents Trudy from learning Alice’s password, the use of cryptography here does not solve the authentication problem. Bob is subject

to a **playback attack**: Trudy need only eavesdrop on Alice’s communication, record the encrypted version of the password, and play back the encrypted version of the password to Bob to pretend that she is Alice. The use of an encrypted password in _ap3.1_ doesn’t make the situation manifestly different from that of protocol _ap3.0_ in Figure 8.17.

**Authentication Protocol ap4.0**

The failure scenario in Figure 8.17 resulted from the fact that Bob could not distin- guish between the original authentication of Alice and the later playback of Alice’s original authentication. That is, Bob could not tell if Alice was live (that is, was currently really on the other end of the connection) or whether the messages he was receiving were a recorded playback of a previous authentication of Alice. The very (_very_) observant reader will recall that the three-way TCP handshake protocol needed to address the same problem—the server side of a TCP connection did not want to accept a connection if the received SYN segment was an old copy (retransmission) of a SYN segment from an earlier connection. How did the TCP server side solve the problem of determining whether the client was really live? It chose an initial sequence number that had not been used in a very long time, sent that number to the client, and then waited for the client to respond with an ACK segment containing that number. We can adopt the same idea here for authentication purposes.

A **nonce** is a number that a protocol will use only once in a lifetime. That is, once a protocol uses a nonce, it will never use that number again. Our _ap4.0_ protocol uses a nonce as follows:

1\. Alice sends the message “I am Alice” to Bob. 
2\. Bob chooses a nonce, R, and sends it to Alice. 
3\. Alice encrypts the nonce using Alice and Bob’s symmetric secret key, _KA_\-_B_, and sends the encrypted nonce, _KA_\-_B_ (R), back to Bob. As in protocol _ap3.1_, it is the fact that Alice knows _KA_\-_B_ and uses it to encrypt a value that lets Bob know that the message he receives was generated by Alice. The nonce is used to ensure that Alice is live.

4\. Bob decrypts the received message. If the decrypted nonce equals the nonce he sent Alice, then Alice is authenticated.

Protocol _ap4.0_ is illustrated in Figure 8.18. By using the once-in-a-lifetime value, _R_, and then checking the returned value, _KA_\-_B_ (_R_), Bob can be sure that Alice is both who she says she is (since she knows the secret key value needed to encrypt _R)_ and live (since she has encrypted the nonce, _R_, that Bob just created).

The use of a nonce and symmetric key cryptography forms the basis of _ap4.0_. A natural question is whether we can use a nonce and public key cryptography (rather than symmetric key cryptography) to solve the authentication problem. This issue is explored in the problems at the end of the chapter.
![Alt text](image-21.png)
**Figure 8.18**  ♦  Protocol _ap4.0_ and a failure scenario

