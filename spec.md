# spec

# TODO:

- Add scenario where a packet is lost and with heartbeats, it is resent
- Add scenario where a packet is resent multiple times, but it is ignored since it is not the latest
- Add scenario with request and response types
- Add scenario with multiple clients joining a room and all getting the same message
- Add scenario with a client disconnecting, missing a message, and then getting it re-sent after it reconnects based on it’s client ID

# Introduction

This is an attempt to lay out the specification for a library that will allow bidirectional communication between clients and a server.

better-socket is a library that intends to allow better communication between client and server applications through usage of the WebSocket protocol in an attempt to make the life of a developer easier.

## Overview

The main principle of design for this library is that it must:

- be usable both in the browser, and in node.js desktop applications
- must support implementations for both clientside and serverside implementations
- cross-compatibility across systems (Linux, macOS, and Windows)
- have a healthy amount of tests to ensure bugs are caught early
- good general usability for developers with TypeScript and generics
- optional symmetric+asymmetric encryption of messages
- ensure that each message is delivered to the correct client

# Scenarios

## Scenario 1: Basic Connection & Heartbeat

We have a server and a single client which would like to establish a communication channel with each other and then start sending heartbeats.

```
@startuml

group Connection
	Client -> Server: Hello! I want to connect, I'm new here
	Server -> Client: Sure. Your client ID is 856bd3d1-830c-4f38-a3fe-726bafb48ee1. I support heartbeats, send them every 30 seconds or I might stop listening to you. We're both starting to sequence from 0 now.
	Client -> Server: I acknowledge message number 0! This is a successfull connection.
end

group Heartbeat
	Client --> Server: Client Heartbeat. Last received seq: 0
	Server --> Client: Server Heartbeat. Last received seq: 0
	...30s...
	Client --> Server: Client Heartbeat. Last received seq: 0
	Server --> Client: Server Heartbeat. Last received seq: 0
end

@enduml
```

![Untitled](https://user-images.githubusercontent.com/23482862/164115635-753477d7-aa81-4121-bdb2-136f2a35ed72.png)

In this scenario, a basic communication channel has been established between the server. The server told the client it’s ID with which it can identify itself if needed in other use cases. The server and client also established a heartbeat and heartbeat interval.

## Scenario 2: Sending Basic Messages

In this scenario, we have a server and a client. They establish communication and heartbeats. Additionally, the server and client both send each other a message.

```
@startuml

group Connection
	Client -> Server: Hello! I want to connect, I'm new here
	Server -> Client: Sure. Your client ID is 856bd3d1-830c-4f38-a3fe-726bafb48ee1. I support heartbeats, send them every 30 seconds or I might stop listening to you. We're both starting to sequence from 0 now.
	Client -> Server: I acknowledge message number 0! This is a successfull connection.
end

group Heartbeat
	Client --> Server: Client Heartbeat. Last received seq: 0
	Server --> Client: Server Heartbeat. Last received seq: 0
	...A few seconds...

	note across
	Here we start numbering sequences
	The client and server each have their sequence number separately
	end note
	
	Client -> Server: Hey there. My current time is 2022-04-19T22:46:58.232Z if you wanna know. seq=1
	Server --> Client: I acknowledge that I received message seq=1
	
	...Some more time passes...
	Server -> Client: Heyo, I'm just gonna restart in 5 minutes so just end up your stuff with me and then reconnect. seq=1
	Client --> Server: I acknowledge that I received message seq=1
end

@enduml
```

![Untitled 1](https://user-images.githubusercontent.com/23482862/164115606-3060ebf5-6738-465e-a380-ebf8972c8c75.png)

# Non-Goals

## Support for multiple servers

Support for multiple servers is a good idea for purposes of load-balancing in larger servers, however, that is not the goal of this library. In the current layout, it is not meant to support multiple servers having interconnected sessions with users. If you have a case like that, you will most likely use something more complex, such as gRPC or other alternatives.

# Final note

This specification was written by my, [oof2win2](https://github.com/oof2win2), because I felt like it would be a cool project where I could learn something new, and it would be pretty useful to implement for [FAGC](https://github.com/FactorioAntigrief/FactorioAntigrief). It is not therefore meant as a corporate solution or anything like that, as it was designed by me mainly. It may therefore contain bugs; therefore, if you find any, please don’t hesitate to create an issue on GitHub.
