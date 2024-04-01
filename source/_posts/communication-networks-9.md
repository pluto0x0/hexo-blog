---
title: Communication Networks (9)
date: 2024-03-05 10:56:29
categories:
- Course Notes
- Communication Networks
---

# Reliable Data Transfer: Intuition

rdt
: reliable data transfer protocol

udt
: unreliable data transfer protocol

![](/img/post/communication-networks-9-1.png){: w="500" }

## Reliable Channel

channel is perfectly reliable:

- no bit errors
- no loss of packets

## Channel with Bit Errors

- underlying channel may flip bits in packet

Detect error: checksum

Recover from errors: receiver tells sender with:

ACK
: acknowledgements

NAKs
:negative acknowledgements

### if ACK/NAK corrupted

- sender doesn’t know what happened at receiver
- can’t just retransmit: possible duplicate
- sender adds **sequence number** to each pkt to deal with duplicating

FSM

![](/img/post/communication-networks-9-2.png){: w="600" }

> Notice that the 2 sequence numbers (0 and 1) suffies
{: .prompt-tip }

![](/img/post/communication-networks-9-3.png){: w="600" }

## NAK-free protocol

- using ACKs only
- instead of NAK, receiver sends ACK for last pkt received OK
- in sender, duplicate ACK means NAK

## Channels with Errors and Loss

- Channel can also lose packets (data, ACKs)
- sender waits “reasonable” amount of time for ACK
- retransmits if no ACK received in this time

![](/img/post/communication-networks-9-4.png){: w="700" }

### Performance

utilization $U_{\text {sender }}$ (1 Gbps link, 15 ms prop. delay, 8000 bit packet)

$$
U_{\text {sender }}=\frac{L / R}{R T+L / R}=\frac{.008}{30.008}=0.00027
$$

![](/img/post/communication-networks-9-5.png){: w="300" }

## Pipelining

e.g. $3\times U_{\text {sender }}$

![](/img/post/communication-networks-9-6.png){: w="300" }

### Go-back-N

- sender can have up to N unacked packets in pipeline
- receiver only sends **cumulative** ack, Doesn’t ack packet if there’s a gap
- sender has timer for oldest unacked packet
- when timer expires, retransmit all unacked packets

![](/img/post/communication-networks-9-8.png){: w="600" }

<!-- ![](/img/post/communication-networks-9-7.png){: w="700" } -->

![](/img/post/communication-networks-9-9.png){: w="800" }

### Selective Repeat

- sender can have up to N unacked packets in pipeline
- rcvr sends individual ack for each packet
- sender maintains timer for each unacked packet
- when timer expires, retransmit only that unacked packet
