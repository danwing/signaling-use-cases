---
title: "Host to Network Signaling Use Cases for Collaborative Traffic Differentiation"
abbrev: "Host to Network Signaling: Use Cases"
category: info

docname: draft-rwbr-tsvwg-signaling-use-cases-latest
submissiontype: IETF  # also: "independent", "editorial", "IAB", or "IRTF"
number:
date:
consensus: true
v: 3
area: "Web and Internet Transport"
workgroup: "Transport and Services Working Group"
keyword:
 - user experience
 - bandwidth
 - priority
 - enriched feedback
 - media streaming
 - realtime media
 - QoS
 - 5G
 - Wi-Fi
 - WiFi
 - diffserv

venue:
  group: "Transport and Services Working Group"
  type: "Working Group"
  mail: "tsvwg@ietf.org"
  arch: "https://mailarchive.ietf.org/arch/browse/tsvwg/"
  github: "danwing/signaling-use-cases"
  latest: "https://danwing.github.io/signaling-use-cases/draft-wing-tsvwg-signaling-use-cases.html"

author:
 -
    fullname: Sridharan Rajagopalan
    organization: Cloud Software Group Holdings, Inc.
    abbrev: Cloud Software Group
    country: United States of America
    email: ["sridharan.girish@gmail.com"]
 -
    fullname: Dan Wing
    organization: Cloud Software Group Holdings, Inc.
    abbrev: Cloud Software Group
    country: United States of America
    email: ["danwing@gmail.com"]
 -
    fullname: Mohamed Boucadair
    organization: Orange
    country: France
    email: mohamed.boucadair@orange.com
 -
    fullname: Tirumaleswar Reddy
    organization: Nokia
    country: India
    email: kondtir@gmail.com


normative:

informative:

  ATIS:
    title: Content Classification for Traffic Optimization
    target: https://access.atis.org/higherlogic/ws/public/download/72240
    date: 2023


--- abstract

Host-to-network (and vice versa) signaling can improve the user experience by informing
the network which flows are more important and which packets within a
flow are more important without having to disclose the content of the packets being delivered. The differentiated service may be provided
at the network (e.g., packet discard preference), the sender (e.g.,
adaptive transmission or session migration), or through cooperation of both the host
and the network.

This document lists use cases demonstrating the need for a mechanism
to share metadata about flows between a receiving host and its networks
to enable differentiated traffic treatment for packets sent to the
host. Such a mechanism is typically implemented using a signaling
protocol between the host and a set of network elements.

--- middle

# Introduction

Bandwidth constraints exist most predominantly at the access network (e.g., radio access networks).
Users who are serviced via these networks use various hosts which run various
applications; each having different connectivity needs for an optimal user
experience. These needs are not frozen but change over time
depending on the application and even depending on how an application
is used (e.g., user's preferences).

The simple network diagram below shows where such bandwidth and
performance constraints usually exist with a "B" (for Bottleneck).
Other network bottlenecks may be experienced in other segments not shown
in the figure, such as interconnection links or the infrastructure that hosts the service (e.g.,
flash crowds).  When a bottleneck exists temporarily, the network has no choice but
to discard or delay packets -- which can harm certain flows and thus lead to suboptimal perceived experience.  In this
document, this is termed 'reactive policy'.


~~~~~ aasvg

           +------+  |                     |          |
+----+     |WLAN  |  |  +------+  +------+ | +------+ | +----+
|host+--B--+access+--B--+router+--+router+---+router+---+host|
+----+     |point |  |  +------+  +------+ | +------+ | +----+
           +------+  |                     |          |
                     |                     | Transit  |  Content
   User Network      |    ISP Network      | Network  |  Network
~~~~~

Complications that are induced by such phenomena may be eliminated by
adequate dimensioning and upgrades. However, such upgrades may not
be always immediately possible or economically justified.

Complementary mitigations are thus needed to soften these complications
by introducing some collaboration between a host and its networks to adjust
their behaviors.

For traffic sent in either direction, the network network elements
that terminate a bandwidth constraining link (or located few hops next to that element) can be fed
with flow metadata. Such augmentation allows those network elements
to make autonomous decisions to prioritize, delay, or drop packets,
especially when performing reactive resource management. Absent such metadata,
these network elements have no guidance and treat packets indiscriminately.

There are several challenges with this metadata augmentation:

* for hosts:
  * which data to share without privacy breach or lowering confidentiality.

* for network elements:
  * Which metadata to honor
  * Tradeoff between the extra cost (including processing) versus benefits
  * Operational impacts

Configured cooperation between an ISP and content providers (via contractual agreements, typically) can allows
metadata signals augmenting packets to be honored by the ISP.  This
cooperation has historically involved examination of server IP
address, TLS SNI, AS number, or heuristics to identify flows.
However, this cooperation might have scalability issues (e.g., ISPs to establish and maintain agreements with a large number of content providers), operational implications as the network configuration will rely on service-specific (e.g., risk of frozen or stale configuration), mismatch between the expected service level from users and the delivered one, etc.
Moreover, smaller ISPs, small content providers, and new content providers are harmed.

A more egalitarian approach provides the same benefit to parties --
large and small -- and also provide richer signaling to further
improve user experience and metadata interoperability. This allows all
parties, big and small, to request the network differentiate a flow,
improving the Internet for end users per {{?RFC8890}}.

Rather than relying on configured cooperation between ISPs and
content providers, this document shows use cases where the client
tells its ISP the importance of packets it is receiving.  This
provides several benefits described in later sections.

There is already some consensus that applications can benefit by collaborative signaling
the network ({{?IAB=RFC9419}}, {{ATIS}}).  This document provides
use cases to further detail the need of such signaling and highlights
the value of the receiving host signaling to its network about flows
being sent to the host.

# Scope & Running Experiments

This document does not intend to define any signaling protocol
nor call whether a new signaling protocol, a new extension, one or more
signaling protocols are needed.

However, this document provides a reference to digest the intended
benefits for enabling collaborating of a host and its networks. These
benefits are yet to be backed up with more evidence. Some experimental
work would be reasonable to be endorsed by the IETF to solicit more
feedback and collect assess the benefits under various setups.

# Conventions and Definitions

Intentional Management:
: network policy such as (monthly) bandwidth quota or bandwidth limit, or quality (delay and/or jitter)) assurances.

Reactive Management:
: network reactions to congestion events, with very short to very long
durations  (e.g., varying wireless and mobile air
interface conditions).

# Various Approaches for Collaborative Signaling

{{design-approaches}} depicts examples of approaches to establish channels to convey
and share metadata between a host, its networks, and the servers.

Metadata exchanges can occur in one single direction or both directions of a flows.

~~~~~ aasvg
(1)  Proxied Connection
                       .--------------.                   +------+
                      |                |                +-+----+ |
+------+              |   Network(s)   |              +-+----+ +-+
|Client+--------------)----------------(--------------+Server+-+
+---+--+              |                |              +---+--+
    |                  '-------+------'                   |
    |                          |                          |
    +<===User Data+Metadata===>+<===User Data+Metadata===>+
    |   Secure Connection 1    |   Secure Connection 2    |
    |                          |                          |

(2)  Out-of-band Metadata Sharing
                        .--------------.                  +------+
                       |                |               +-+----+ |
+------+               |   Network(s)   |             +-+----+ +-+
|Client+---------------)----------------(-------------+Server+-+
+---+--+               |                |             +---+--+
    |                   '-------+------'                  |
    |                           |                         |
    +<-----End-to-End Secure Connection + User Data------>+<---.
    |                           |                         | GLUE|
    |                           |                         | CXs |
    +<-- Metadata (Optional) -->+<----- Metadata -------->+<---'
    |    Secure Connection 1    |    Secure Connection 2  |
    |                           |                         |

(3)  Client-centric Metadata Sharing
                          .--------------.                  +------+
                         |                |               +-+----+ |
+------+                 |   Network(s)   |             +-+----+ +-+
|Client+-----------------)----------------(-------------+Server+-+
+---+--+                 |                |             +---+--+
    |                     '-------+------'                  |
    |                             |                         |
    +<--------- Metadata -------->+                         |
    |        Secure Connection    |                         |
    |                             |                         |
    +<== End-to-End Secure Connection User Data+Metadata ==>+
    |                             |                         |
~~~~~
{: #design-approaches artwork-align="center" title="Candidate Signaling Approaches"}

The client-centric metadata sharing approach because it preserves privacy and also
takes advantage of clients having a full view on their available network attachments.
Without client involvement some use-cases cannot be solved, as detailed below
in {{generic-cases}}.

> Note: some use cases may require seeking for users preference or receiving these preferences. Such matters are
out of scope.




# Use Cases

## Generic Cases {#generic-cases}

### Priority Between Flows (Inter-Flow) of The Same Host

Certain flows being received by a host (or by an application on a host) are less or more important than other
flows of **the same host**.  For example, a host downloading a software update is generally considered less important
than another host doing interactive audio/video or gaming.  By signaling the relative importance
of flows to a network element, the network element can (de-)prioritize
those flows to best accommodate the needs of the various applications on a same host.

Without a signaling in place between a receiving host and its network, remote peers are able to mark packets that interfere with the
desires of the receiving host -- making their flows more important than what the receiving host
considers more important. This eventually causes all flows to be marked as important, or -- more
likely -- such priority markings to be ignored.


### Priority Within a Flow (Intra-Flow)

Interactive Audio/Video has long been using {{?RTP=RFC3550}} which
runs over UDP.  As described in {{Section 2.3.7.2 of ?RFC7478}}, there
is value in differentiating between voice, video and data.  Today's
video streaming is exclusively over TCP but will migrate to QUIC and
eventually is likely to support unreliable transport ({{?RFC9221}},
{{?I-D.ietf-moq-transport}}).  With unreliable transport of video in
RTP or QUIC, it is beneficial to differentiate the important video
keyframes from other video frames.  Other applications such as gaming
and remote desktop also benefit from differentiating their packets to
the network.

Many of these flows do not originate from a content provider's network --
rather, they originate from a peer (e.g., VoIP, interactive video,
peer-to-peer gaming, Remote Desktop).
Thus, the flows originate from an IP address that is not known before
connection establishment, so there needs to be a way for the client
to authorize the network elements to receive and hopefully to honor the metadata of those
packets.

Without a signaling in place between a receiving host and its network,
remote peers are able to mark every packet of a flow
as important, causing much the same problem as the previous use-case. Eventually, when all
packets of every flow are marked as important, there is no differentiation between packets
within a flow, rendering the network unable to improve reactive policy decisions.

## Detailed Use Cases

### Media Streaming {#example-media-streaming}

Streaming video contains the occasional key frame ("i-frame")
containing a full video frame.  These are necessary to rebuild
receiver state after loss of delta frames.  The key frames are
therefore more critical to deliver to the receiver than delta frames.

Streaming video also contains audio frames which can be encoded
separately and thus can be signaled separately.  Audio is more
critical than video for almost all applications, but its importance
(relative to other packets in the flow) is still an application decision.

Examples: Super bowl, On-Demand Streaming

Requirement:

Signal the flow needs least delay between server and client. Network can provide minimal delay information to the host. Feedback from the network and the client, based on user preference, is required for more efficient streaming. This is required for incorporating special needs based on application/user capabilities and to prioritize traffic during reactive events.

Problems:

1.  All packets prioritized the same irrespective of user preferences/needs:

    a.  A client based change in priority of a certain type of data is not possible. For example, a hearing challenged user can choose video over audio while the priority is different for other users.

    b.  Dynamic changes to priority based on user activity is not possible today. For example, audio packets having the same priority when a user mutes the audio locally, or change in priority during time of emergency where video streaming applications share the same priority as SOS signals.
2.  In loss-prone networks or during a reactive policy events, retransmissions cause immense delay. Networks, not able to distinguish between reliable and loss-tolerant data or the critical data within a flow (i-frames, p-frames, and audio packets), can have challenges in efficiently handling/forwarding data.

Solution:

1.  Client to ISP (Host-to-network) signaling can help with conveying user needs.

    a. The server (content provider) achieves best scalability by sending a single stream with the same per-packet metadata. The client, on the other hand, signaling the ISP to treat certain packets with a different priority over the others, can help solve the issue. For example, for video streaming, if the metadata just said "audio" (0x00), "video i-frame" (0x01) "video p-frame" (0x02), the client could have the signaling protocol tell an upstream router which one was most important. Some users could prioritize video over audio and vice versa.

    b. Clients occasionally change the importance of receiving certain streams, such as muting video or audio, but still need to receive some frames to populate their playout jitter buffer. In such cases, the client can signal the ISP to (de-)prioritize certain types of traffic during the duration of that event. The per-packet metadata from the server would remain the same while the ISP treats certain per-packet metadata differently. These signals can be propagated to the server (in the form of a network to host signals) for improved efficiency, but this is out of the scope of this document.
2.  Server to ISP (Host-to-network) per packet metadata can help in informing the network about the nature of traffic.

### Interactive Media

Examples: VoIP (peer-to-peer (P2P), group conferencing), gaming.

Requirement:

The flow needs low jitter and low delay. However, the network can only provide a limited amount of low jitter/low delay to each host, maybe as few as one. This requires signaling feedback indicating that low jitter and low delay flows are already subscribed to other hosts. In response, the user and the application will likely continue, occasionally re-attempting to get the desired quality of service from the network.

In many scenarios a game or VoIP application will want to signal different metadata for the same type of packet in each direction.  For example, for a game, video in the server-to-client direction might be more important than audio, whereas input devices (e.g., keystrokes) might be more important than audio.

Many interactive audio/video applications also support sharing the presenter's screen, file, video, or pictures.  During this sharing the presenter's video is less important but the screen or picture is more important.  This change of importance can be conveyed in metadata to the network, by signaling from the client to the network element(s).

Both gaming (video in both directions, audio in both directions, input
devices from client to server) and interactive audio/video (VoIP,
video conference) involves important traffic in both directions --
thus is a slightly more complicated use-case than the previous
example.  Additionally, most Internet service providers constrain
upstream bandwidth so proper packet treatment is critical in the
upstream direction.

Examples: VoIP, online gaming.

Problems:

1. Peer-to-peer communication often involves direct connection(s) without involvement of an intermediary server (or one of the peers take the role of a server in some gaming cases). The signaling (like DSCP) from these IP addresses are often ignored as these IP addresses are not in the ISPs' list of recognized servers. It is the same fate for servers that are not in the ISPs' list of recognized servers in an online gaming or a conference scenario

2. Each peer part of the network will have different preferences and it is difficult for the application to modify metadata for each peer based on the preference.

Solutions:

1. Client-to-network signal indicating ISP to honor the honoring signaling data of a particular flow enables any data, irrespective of whether the sending server is part of the list of IP addresses or not. This enables client(user)-driven processing of metadata and client driven authorization of the IP addresses apart from the ISPs' list of recognized IP addresses.

2. Client-to-network signaling also helps the ISP treat the same metadata differently for different flows based on the user preference. The per-packet metadata from the server would remain the same, simplifying signaling implementation on the server and making it more scalable.


#### Examples of Same Type of Metadata with Different Preferences

A game or VoIP application may want to signal different metadata for the same type of packet in each direction.  For example, for a game, video in the server-to-client direction might be more important than audio, whereas input devices (e.g., keystrokes) might be more important than audio. Each user can have varied preferences for the same type of data originating from the server. Determination of such preferences is outside of the scope of this document.

1. Video in a media session: One user can choose to keep the video and screen-sharing equally distributed on the screen while another user can minimize or reduce the size of the video. Based on the size/preference of the video window, video packets can be prioritized accordingly relative to screen-shared content. This preference can be propagated to the server as network-to-host signaling for more efficient transmission but that is out of the scope of this document.

2. User Inactivity signal: When the user is inactive during an interactive session such as gaming (activity can be detected automatically or can be a simple user signal of Away-from-Keyboard (AFK)), the client can signal to the ISP to deprioritize the packets to the extent that the client receives some data to populate their playout jitter buffer.

> Todo: this section on cooperation needs editing.

### Bulk Data Transfer

Examples: backup/restore, software update, RSS feed update, email, printing to a print server, file copy

Requirement: Signal the flow as below best-effort.

Problem:

1. Bulk data is generally not interactive and can be forwarded of over paths of greater latency. It is treated as non-time sensitive operation. Although some bulk transfers (like saving a file in the middle of editing) that cannot be minimized, where the user will have to wait till it is completed to further use the device, should be given real-time importance. Bulk data such as printing a file while editing can be parallelized and need not have real-time priority. Similarly, backing up of data can be parallelized while restoring of data cannot be parallelized based on the application.

Solution:

1. Client-to-network signaling can solve this by identifying and signaling the flow's priority appropriately for ISP to handle the packets accordingly.

### Mixed Traffic

Examples: Desktop Virtualization

Requirement:

: Signal flow will vary depending on the nature of the packet. With variety of traffic going through the session, some packets can contain interactive traffic while the others contain bulk transfer. There can be combination of reliable and unreliable traffic within the same session through multiple streams. Host-to-network signaling plays a vital role in effectively forwarding mixed traffic for better user interactivity and network performance. ```

Mixed traffic has a large variety of applications with unique cases. For the purpose of use case, Desktop Virtualization is considered below.

Problems:

1. In remote desktop use case, a server can host multiple connections with varying type of traffic to it. These servers are often in a database, exposed to the internet through some sort of a gateway-proxy and the signaling (like DSCP bits) from these servers are often ignored by the ISPs.

2. A video key frame should be handled differently by the network
depending on a streaming application versus a remote desktop
application.  The video streaming application's primary and only
nature of traffic is video and audio.  In contrast, a remote desktop
application might be playing a video and its associated audio while at
the same time the user is editing a document. The user's keystrokes
and those glyphs need to be prioritized over the video lest the user
think their inputs are being ignored (and type the same characters
again).

3. With multiple applications running in the same virtualized environment, video streaming having same priority as graphics updates while the user is playing a video in the background while typing a document in the foreground can cause interactivity issues.

Solution:

1. Client-to-network signal indicating ISP to honor the honoring signaling data of a particular flow enables enables the servers, that are not even directly visible to the ISPs, to benefit from signaling. This enables client(user)-driven processing of metadata and client driven authorization of the IP addresses apart from the ISPs' list of recognized IP addresses.

2. Client signaling can reduce/eliminate the resource intensive responsibility of identifying such scenarios and modifying the metadata accordingly. The per-packet metadata from the server would remain the same, simplifying signaling implementation on the server and making it more scalable.

3. Client signaling based on user activity can improve user experience. Signaling the ISP to treat the same metadata differently based on the user activity can help improve/resolve this, while keeping the per packet metadata the same.

### Assisted Offload

There are  cases (crisis) where "normal" network
resources cannot be used at maximum and, thus, a network would seek to
reduce or offload some of the traffic during these events -- often
called 'reactive traffic policy'. An example of such use case is cellular networks that are overly used (and radio resources exhausted) such as a large collection of people (e.g., parade, sporting event), or such as a partial radio network outage (e.g., tower power outage).  During such a condition, an alternative network attachment may be available to the host (e.g., Wi-Fi).

Network-to-host signals are useful to put in place adequate traffic distribution policies (e.g., prefer the use of alternate paths, offload a network).

# Operational Considerations

## Policy Enforcement

Some metadata requires the network to share some hints with a host to adjust its behavior for some specific flows. However, that metadata may have a dependency on the service offering that is subscribed by a user. Let us consider the example of a bitrate for an optimized video delivery. **Such bitrate may not be computed system-wide** given that flows from users with distinct service offerings
(and connectivity SLOs) may be serviced by the same network nodes.


## Redundant Functions & Classification Complications

If distinct channels are used to share the metadata between a host and a network, a network that engages in the collaborative signaling approach will require sophisticated features to classify flows and decide which channel is used to share metadata so that it can consume that information. Likewise, the network will require to implement redundant functions; for each signaling interface.

As such, application- and protocol-specific signaling channels are suboptimal.

## Metadata Scope

An operational challenge for sharing resource-quota like metadata (e.g., maximum bitrate) is that the network is generally not entitled to allocate quota per-application, per-flow, per-stream, etc. that
delivered as part of an Internet connectivity service. However, the network has a visibility about the overall network attachment (e.g., inbound/outbound bandwidth discussed in {{?I-D.ietf-opsawg-teas-attachment-circuit}}). As such,  hints about resource-like metadata is bound by default to the overall network attachment, not specific to a given application or flow.

> It is out of the scope of this document to discuss setups (e.g., 3GPP PDU Sessions) where network attachments with GBR (Guaranteed Bit Rate) for specific flows is provided.

## Applications Interference

Applications that have access to a resource-quota information may adopt an aggressive behavior (compared to those that don't have access) if they assumed that a resource-quota like metadata is for the application, not for the host that runs the applications.

This is challenging for home networks where multiple hosts may be running behind the same CPE, with each of them running a video application.


## Abuse and Constraints

It is important that not every flow be prioritized; otherwise, the
network devolves into the best-effort network that existed prior to
metadata signaling. It is a requirement that mechanisms exist to
prevent this occurrence.

Such a mechanism might be simple, for example, a cellular network might allow one flow from a subscriber to declare itself as important; other flows with that subscriber are denied attempts to prioritize themselves.  The mechanism might be more complex where authentication and authorization is performed by an enterprise network which, itself, decides which flows are important based on its policy and only the enterprise network communicates flow priorities to the ISP network.  The enterprise might prioritize certain users (e.g., IT staff), certain equipment (audio/video equipment in a conference room), or whatever its policies it might want.

## Key Establishment {#key}

Various proposals have suggested establishing a key to validate
per-packet metadata or to decrypt per-packet metadata.  However, most
proposals have not specified how this key would be established. A
signaling protocol from the receiving host to its ISP could establish
such a key. The host can then convey the key to the sending host to
use to integrity protect or encrypt the per-packet metadata.

> Note: The CPU overhead of validating or decrypting such per-packet metadata
needs to be carefully considered (and further assessed via experiments) by the signaling protocol proposing such
keying. Also, the required operational setup should be documented.

## Metadata Version/Capability Exchange

The sender has to convey metadata in a way that is understood by the
various network elements on the path -- each of which might be
operated by different entities and have different capabilities.  For
example, the Wi-Fi access point might be operated by an enterprise
network, hotel, or home user, whereas the upstream router is operated by
the ISP.  Each of those might support different versions of the same
metadata, or might need the metadata expressed in different ways.

The signaling protocol would provide a way to learn the needs of those
networks, and provide metadata signaling satisfying most or all of their
needs.

## On the Use of Fast Path

TBC

## Impacts of Nested Congestion Controls

TBC


# Requirements Summary

TODO summary.

* The activation of the collaborative signalling MUST NOT lower the perceived service
  of flows during nominal conditions (i.e., when the network is not in a reactive policy mode).



# Security Considerations

TODO Security


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

Thanks to Hang Shi for the review and comments.
