---
title: "Signaling Use Cases for Collaborative Traffic Differentiation"
abbrev: "Collaborative Host/Network Signaling: Use Cases"
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
  MOQ-PRIORITY:
    title: Prioritization results - Media Over QUIC
    target: https://datatracker.ietf.org/meeting/117/materials/slides-117-moq-sessb-prioritization-results-00
    date: 2024


--- abstract

Host-to-network (and vice versa) signaling can improve the user experience by informing
the network which flows are more important and which packets within a
flow are more important without having to disclose the content of the packets being delivered. The differentiated service may be provided
at the network (e.g., packet discard preference), the sender (e.g.,
adaptive transmission or session migration), or through cooperation of both the host
and the network.

This document outlines a set of use-cases that highlight the need for a mechanism
to share metadata about flows between a host and its network in order to enable different traffic treatment.
Such a mechanism is typically implemented using a signaling protocol between the host and
a set of trusted netwrok elements.

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
flash crowds). A bottleneck may be limited in time, present or not regular patters, etc.

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
by introducing some collaboration between hosts and networks to adjust
their behaviors.

For traffic sent in either direction, the network network elements
that terminate a bandwidth constraining link (or located few hops next to that element) can be fed
with flow metadata. Such augmentation allows those network elements
to make autonomous decisions to prioritize, delay, or drop packets,
especially when performing reactive resource management. Absent such metadata,
these network elements have no means to guide the enforcement of the reactive
resource policy.

There are several challenges with this metadata augmentation:

* for hosts: which data to share without privacy breach or lowering confidentiality.
* for network elements:

  + Deciding which metadata to trust
  + Tradeoff between the extra cost (including processing) vs. expected benefits
  + Impact on the network operations


The metadata signals from a content provider are more likely to be
authentic (if adequate authorization/validation are in place) but the metadata signals from other hosts may be "wrong",
undesired by the peer host, or maliciously contain improper metadata.
Attempts to automate identification of content providers have included
HTTP "Host" header inspection and TLS SNI inspection which are
expected to fail as encrypted SNI and privacy-enhancing proxies
become more prevalent. Another mechanism to authorize metadata
signals from a content provider is to configure the ISP equipment
with the content network's source IP addresses (or other labels that
may be visible on the packets) and provide a differentiated service
to the traffic that match these criteria. However, such an arrangement
may have scalability issues. An approach to mitigate these issues is to limit
the target contents networks and networks that would put in place these arrangements.
Such limitations would benefit large players (large ISPs and large content network) and
disadvantages small players (and new players).  A more egalitarian
approach would provide the same benefit to all parties -- large and
small -- and also provide richer signaling to further improve user
experience and metadata interoperability. This would allow all parties
to become part of the "Internet fast lane".

The authorization problem exists with technologies as relatively
simple as DiffServ and the problem persists with many other
recently discussed metadata signaling mechanisms, including
embedding information in the UDP payload
({{?I-D.trammell-plus-spec}}), UDP options
({{?I-D.kaippallimalil-tsvwg-media-hdr-wireless}}), overloading
the IPv6 Flow Label ({{?I-D.cc-v6ops-wlcg-flow-label-marking}},
and Hop-by-Hop Options.  One mechanism suggested occasionally is
to encrypt or integrity protect the metadata with a key; such a key
could be established using a signaling protocol, see {{key}}.

There is some consensus that applications can benefit by collaborative signaling
the network ({{?IAB=RFC9419}}, {{ATIS}}).  This document provides
use-cases to further detail the need of such signaling.

# Scope & Running Experiments

This document does not intend to define any signaling protocol
nor call whether a new signaling protocol, a new extension, one or more
signaling protocols are needed.

However, this document provides a reference to digest the intended
benefits for enabling collaborating between hosts and networks. These
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
and share metadata between hosts, networks, and servers.

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

# Use Cases

## Generic Cases

### Priority Between Flows (Inter-Flow) of The Same Host

Certain flows being received by a host (or by an application on a host) are less or more important than other
flows of **the same host**.  For example, a host downloading a software update is generally considered less important
than another host doing interactive audio/video or gaming.  By signaling the relative importance
of flows to a network element, the network element can (de-)prioritize
those flows to best accomodate the needs of the various applications (on a same host) and
between hosts on a network.

### Priority Within a Flow (Intra-Flow)

Interactive Audio/Video has long been using {{?RTP=RFC3550}} which
runs over UDP.  As described in {{Section 2.3.7.2 of ?RFC7478}}, there
is value in differentiating between voice, video and data.  Today's
video streaming is exclusively over TCP but will migrate to QUIC and
eventually is likely to support unreliable transport ({{?RFC9221}},
{{?I-D.kpugin-rush}}).  With unreliable transport of video in
RTP or QUIC, it is beneficial to differentiate the important video
keyframes from other video frames.  Other applications such as gaming
and remote desktop also benefit from differentiating their packets to
the network.

Many of these flows do not originate from a content provider's network.
Thus, the flows originate from an IP address that is not known before
connection establishment, so there needs to be a way for the client
to authorize the network elements to receive and hopefully to honor the metadata of those
packets.

## Detailed Use Cases

### Video Streaming {#example-video-streaming}

Streaming video contains the occasional key frame ("i-frame")
containing a full video frame.  These are necessary to rebuild
receiver state after loss of delta frames.  The key frames are
therefore more critical to deliver to the receiver than delta frames.

Streaming video also contains audio frames which can be encoded
separately and thus can be signaled separately.  Audio is more
critical than video for almost all applications, but its importance
(relative to other packets in the flow) is still an application decision.  In the example below, the audio
is more important than video (importance=high, PT=keep, RU=reliable), video key frames
have middle importance (importance=low, PT=discard, RU=reliable), and both types
of video delta frames (P-frame and B-frame) have least importance (importance=low, PT=discard, RU=unreliable).

Video Streaming Metadata:

Based on metadata types listed in the {{?I-D.rwbr-sconepro-flow-metadata}}, the host to network metadata parameters for video streaming type is given below.

| Traffic type                             | Importance | PacketNature      | PacketType           |
|:----------------------------------------:|:----------:|:-----------------:|:--------------------:|
| video I-frame (key frame)                | low        | realtime          | reliable             |
| video delta P-frame                      | low        | discard           | unreliable           |
| video delta B-frame                      | low        | discard           | unreliable           |
| audio                                    | high       | realtime          | reliable             |
{: #table-video-streaming title="Example Values for Video Streaming Metadata"}

### Interactive Media

Examples: VoIP, gaming.

Requirement:  Signal the flow needs low jitter and low delay. However, the network can only provide
a limited amount of low jitter/low delay to each host, maybe as few as one. This requires signaling
feedback indicating that low jitter and low delay flows are already subscribed to other hosts. In
response, the user and the application will likely continue, occasionally re-attempting to get the
desired quality of service from the network.

In many scenarios a game or VoIP application will want to signal different
metadata for the same type of packet in each direction.  For example, for
a game, video in the server-to-client direction might be more important
than audio, whereas input devices (e.g., keystrokes) might be more important
than audio.

Both gaming (video in both directions, audio in both directions, input
devices from client to server) and interactive audio/video (VoIP,
video conference) involves important traffic in both directions --
thus is a slightly more complicated use-case than the previous
example.  Additionally, most Internet service providers constrain
upstream bandwidth so proper packet treatment is critical in the
upstream direction.

Metadata:

Based on metadata types listed in the {{?I-D.rwbr-sconepro-flow-metadata}}, the host to network metadata parameters for interactive media type is given below.

Interactive A/V, downstream Metadata:

| Traffic type      | Importance | PacketNature      | PacketType           |
|:-----------------:|:----------:|:-----------------:|:--------------------:|
| video key frame   | low        | realtime          | reliable             |
| video delta frame | low        | discard           | unreliable           |
| audio             | high       | realtime          | reliable             |
{: #table-interactive-av-downstream title="Example Values for Interactive A/V, downstream"}

| Traffic type      | Importance | PacketNature      | PacketType           |
|:-----------------:|:----------:|:-----------------:|:--------------------:|
| video key frame   | low        | realtime          | reliable             |
| video delta frame | low        | discard           | unreliable           |
| audio             | high       | realtime          | reliable             |
{: #table-video-av-upstream title="Example Values for Interactive A/V, upstream"}

Many interactive audio/video applications also support sharing the presenter's
screen, file, video, or pictures.  During this sharing the presenter's video
is less important but the screen or picture is more important.  This change
of imporance can be conveyed in metadata to the network, as in the table
below:

Interactive A/V, upstream Metadata:

| Traffic type      | Importance | PacketNature      | PacketType           |
|:-----------------:|:----------:|:-----------------:|:--------------------:|
| video key frame   | low        | realtime          | reliable             |
| video delta frame | low        | discard           | unreliable           |
| audio             | high       | realtime          | reliable             |
| picture sharing   | high       | realtime          | reliable             |
{: #table-video-av-sharing title="Example Values for Interactive A/V with picture sharing, upstream"}

In many scenarios a game or VoIP application will want to signal different
metadata for the same type of packet in each direction.  For example, for
a game, video in the server-to-client direction might be more important
than audio, whereas input devices (e.g., keystrokes) might be more important
than audio.

Todo: this section on cooperation needs editing.

### Live Streaming {#example-live-straming}

Live streaming is different from other forms of streaming (e.g., video). Specifically, the delivery of live streams have more constraints on reducing latency to keep the streaming as close to the live feed. In this scenario, video might be prioritized than audio in some cases (e.g., Super Bowl, Olympics, or the World Cup) while audio might be prioritized for other events (e.g., music concert). This also prioritizes latency (to a certain extent) over stream quality. Compared to heuristics, explicit network-to-host information is likely to provide deterministic behaviors to optimally select of the "quality" of the access to minimize the latency. An example of where such schemes can reduce the latency and improve performance of live streaming is discussed in {{MOQ-PRIORITY}}.

Metadata for live streaming that prefers video over audio: (e.g., Superbowl game coverage)

| Traffic type      | Importance | PacketNature      | PacketType           |
|:-----------------:|:----------:|:-----------------:|:--------------------:|
| video key frame   | high        | realtime          | reliable             |
| video delta frame | low        | discard           | unreliable           |
| audio             | low       | discard          | unreliable             |
{: #table-video-livestream title="Example Values for Live Streaming of Video Preferred Event"}

Metadata for live streaming that prefers audio over video: (e.g., Music concerts)

| Traffic type      | Importance | PacketNature      | PacketType           |
|:-----------------:|:----------:|:-----------------:|:--------------------:|
| video key frame   | low        | realtime          | reliable             |
| video delta frame | low        | discard           | unreliable           |
| audio             | high       | realtime          | reliable             |
{: #table-audio-livestream title="Example Values for Live Streaming of Audio Preferred Event"}


### Bulk Data Transfer

Examples: backup/restore, software update, RSS feed update, email, printing to a print server

Requirement: Signal the flow as below best-effort.

Metadata:

| Traffic type               | Importance | PacketNature    | PacketType          | Comments  |
|:--------------------------:|:----------:|:---------------:|:-------------------:|:---------:|
| File copy                  | low        | bulk            | reliable            |           |
| Printing                   | high       | bulk            | reliable            |           |

### Mixed Traffic

Examples: Desktop Virtualization, Office software in the cloud (editing local files, typing is interactive while save operation is bulk transfer)

Requirement: Signal flow will vary depending on the nature of the packet. With variety of traffic going through the session, some packets can contain interactive traffic while the others contain bulk transfer. There can be combination of reliable and unreliable traffic within the same session through multiple streams. Host-to-network signaling plays a vital role in effectively routing mixed traffic for ideal user interactivity and network performance.

Example packet metadata for Desktop Virtualization (like Citrix
Virtual Apps and Desktops - CVAD) application.  This is shown in two
tables, client-to-server traffic ({{table-desktop-virtualization-c2s}})
and server-to-client traffic ({{table-desktop-virtualization-s2c}}).

Remote Desktop Virtualization Metadata:

Based on metadata types listed in the {{?I-D.rwbr-sconepro-flow-metadata}}, the host to network metadata parameters for remote desktop virtualization type is given below.

| Traffic type               | Importance | PacketNature    | PacketType          | Comments  |
|:--------------------------:|:----------:|:---------------:|:-------------------:|:---------:|
| User typing                | high       | realtime        | reliable            |           |
| Mouse click/End Position   | high       | realtime        | reliable            | The start and endpoint of the pointer movement is vital to ensure user action is completed correctly. So, the endpoints have to be reliably transmitted with real-time priority. **|
| Interactive audio          | high       | keep            | unreliable          |   |
| Authentication - Finger print, smart card | low | realtime | reliable |  |
| Interactive video key frame            | low        | keep            | unreliable          | Video key frames form the base frames of a video upon which the next 'n' timeframe of video updates is applied on. These frames, are hence, critical and without them, the video would not be coherent until the next critical frame is received. Retransmits of these are harmful to the UX. ***|
| Mouse position tracking    | low        | discard         | unreliable          | When the pointer is moved from one point to another, the coordinates of the pointers between the two points can be lost without much of an impact to the UX as long as the start and endpoint reaches. This would ensure the user action is completed, even if the experience seems glitchy. |
| Interactive video delta frame           | low        | discard            | unreliable          |   |
{: #table-desktop-virtualization-c2s title="Example Values for Remote Desktop Virtualization Metadata, client to server"}

| Traffic type               | Importance | PacketNature    | PacketType          | Comments  |
|:--------------------------:|:----------:|:---------------:|:-------------------:|:---------:|
| Glyph critical             | high       | realtime        | reliable          | The frames that form the base for the image is more critical and needs to be transmitted as reliably as possible. Retransmits of these are harmful to the UX.**|
| Interactive (or streaming) audio   | high       | keep            | unreliable          |   |
| Haptic feedback            | high       | discard         | unreliable          | Virtualizing haptic feedback is real-time and high importance although the feedback being delivered late is of no use. So dropping the packet altogether and not retransmitting it makes more sense |
| Interactive (or streaming) video key frame            | low        | keep            | unreliable          | Video key frames form the base frames of a video upon which the next 'n' timeframe of video updates is applied on. These frames, are hence, critical and without them, the video would not be coherent until the next critical frame is received. Retransmits of these are harmful to the UX. ***|
| File copy                  | low        | bulk            | reliable            |   |
| Interactive (or streaming) video predictive frame     | low        | discard         | unreliable          | Video predictive frames can be lost, which would result in minor glitch but not compromise the user activity and video would still be coherent and useful. The reception of subsequent video key frame would mitigate the loss in quality caused by lost predictive frames. |
| Glyph smoothing            | low        | discard         | Unreliable          | The smoothing elements of the glyph can be lost and would still present a recognizable image, although with a lesser quality. Hence, these can be marked as loss tolerant as the user action is still completed with a small compromise to the UX. Moreover, with the reception of the next glyph critical frame would mitigate the loss in quality caused by lost glyph smoothing elements. |
{: #table-desktop-virtualization-s2c title="Example Values for Remote Desktop Virtualization Metadata, server to client"}

*** A video key frame should be handled differently by the network
depending on a streaming application versus a remote desktop
application.  The video streaming application's primary and only
nature of traffic is video and audio.  In contrast, a remote desktop
application might be playing a video and its associated audio while at
the same time the user is editing a document.  The user's keystrokes
and those glyphs need to be prioritized over the video lest the user
think their inputs are being ignored (and type the same characters
again). Hence, the values are different even for the same nature of
traffic but a different application.

### Assisted Offload

There are  cases (crisis) where "normal" network
resources cannot be used at maximum and, thus, a network would seek to
reduce or offload some of the traffic during these events -- often
called 'reactive traffic policy'. An example of such sue case is cellular networks
that are overly used (and radio resources exhausted) while alternative network
attachment networks are available to host.

Network-to-host signals are
useful to put in place adequate traffic distribution policies (e.g.,
prefer the use of alternate paths, offload a network).

# Operational Considerations

## Abuse and Constraints

It is important that not every flow be prioritized; otherwise, the
network devolves into the best-effort network that existed prior to
metadata signaling. It is a requirement that mechanisms exist to
prevent this occurrence.

Such a mechanism might be simple, for example, a
cellular network might allow one flow from a subscriber to declare
itself as important; other flows with that subscriber are denied
attempts to prioritize themselves.  The mechanism might be more
complex where authentication and authorization is performed by an
enterprise network which, itself, decides which flows are important
based on its policy and only the enterprise network communicates flow
priorities to the ISP network.  The enterprise might prioritize
certain users (e.g., IT staff), certain equipment (audio/video
equipment in a conference room), or whatever its policies it might
want.

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

# Security Considerations

TODO Security


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
