---
title: 'Transport Layer'
weight: 3
---
# Transport Layer
Residing between the application and network layers, the transport layer is a central piece of the layered network architecture. It has the critical role of providing com- munication services directly to the application processes running on different hosts. The pedagogic approach we take in this chapter is to alternate between discussions of transport-layer principles and discussions of how these principles are implemented in existing protocols; as usual, particular emphasis will be given to Internet proto- cols, in particular the TCP and UDP transport-layer protocols.

We’ll begin by discussing the relationship between the transport and network layers. This sets the stage for examining the first critical function of the transport layer—extending the network layer’s delivery service between two end systems to a delivery service between two application-layer processes running on the end sys- tems. We’ll illustrate this function in our coverage of the Internet’s connectionless transport protocol, UDP.

We’ll then return to principles and confront one of the most fundamental prob- lems in computer networking—how two entities can communicate reliably over a medium that may lose and corrupt data. Through a series of increasingly complicated (and realistic!) scenarios, we’ll build up an array of techniques that transport proto- cols use to solve this problem. We’ll then show how these principles are embodied in TCP, the Internet’s connection-oriented transport protocol.

We’ll next move on to a second fundamentally important problem in networking—controlling the transmission rate of transport-layer entities in order to avoid, or recover from, congestion within the network. We’ll consider the causes and consequences of congestion, as well as commonly used congestion-control

techniques. After obtaining a solid understanding of the issues behind congestion control, we’ll study TCP’s approach to congestion control.

