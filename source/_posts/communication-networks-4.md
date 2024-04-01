---
title: Communication Networks (4)
date: 2024-01-30
mermaid: true
image:
    path: /img/post/communication-networks-4-1.png
categories:
- Course Notes
- Communication Networks
---

![Alt text](/img/post/communication-networks-4-3.png){: w="8"}

## Application architectures

Possible structure of applications:

- Client-server
- Peer-to-peer (P2P)
- Hybrid

### Client-server architecture

![Alt text](/img/post/communication-networks-4-1.png)

server

- always-on host
- permanent IP address

client

- may be intermittently connected
- may have dynamic IP addresses

### P2P architecture

- no always-on server
- peers request service from other peers, provide service in return to other peers
- self scalability – new peers bring new service capacity, as well as new service demands

### Hybrid of client-server and P2P

- Finding address of remote party: centralized server(s)
- Client-client connection is direct (not through server) 

## Processes communicating

- client process: process that initiates communication
- server process: process that waits to be contacted

## Sockets

- process sends/receives messages to/from its socket
- socket analogous to **mailbox**

![Alt text](/img/post/communication-networks-4-5.png)

## Transport service requirements

| Application        | Data Loss    | Throughput                        | Time Sensitive        |
|--------------------|--------------|-----------------------------------|-----------------------|
| file transfer      | no loss      | elastic                           | no                    |
| e-mail             | no loss      | elastic                           | no                    |
| Web documents      | no loss      | elastic                           | no                    |
| real-time audio/video | loss-tolerant | audio: 5kbps-1Mbps              | yes, 100's msec       |
|                    |              | video:10kbps-5Mbps                | yes, few secs         |
| stored audio/video | loss-tolerant | same as above                    | yes, 100's ms         |
| interactive games  | loss-tolerant | few kbps up                       | yes and no            |
| text messaging     | no loss      | elastic                           | no                    |

