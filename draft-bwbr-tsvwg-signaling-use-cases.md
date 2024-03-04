---
title: "Signaling Use Cases for Collaborative Traffic Differentiation"
abbrev: "Collaborative Host/Network Signaling: Use Cases"
category: info

docname: draft-bwbr-tsvwg-signaling-use-cases-latest
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
({{?I-D.draft-trammell-plus-spec}}), UDP options
({{?I-D.draft-kaippallimalil-tsvwg-media-hdr-wireless}}), overloading
the IPv6 Flow Label ({{?I-D.draft-cc-v6ops-wlcg-flow-label-marking}},
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


## Priority Between Flows (Inter-Flow) of The Same Host


Certain flows being received by a host (or by an application on a host) are less or more important than other
flows of **the same host**.  For example, a host downloading a software update is generally considered less important
than another host doing interactive audio/video or gaming.  By signaling the relative importance
of flows to a network element, the network element can (de-)prioritize
those flows to best accomodate the needs of the various applications (on a same host) and
between hosts on a network.

### Abuse and Constraints

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

#### Interactive Media

Examples: VoIP, gaming, virtual desktop.

Requirement:  Signal the flow needs low jitter and low delay.  However, the network can only provide
a limited amount of low jitter/low delay to each host, maybe as few as one.  This requires signaling
feedback indicating that low jitter and low delay flows are already subscribed to other hosts. In
response, the user and the application will likely continue, occasionally re-attempting to get the
desired quality of service from the network.

Todo: this section on cooperation needs editing.


#### Bulk Data Transfer

Examples: backup/restore, software update, RSS feed update, email

Requirement: Signal the flow as below best-effort.


## Priority Within a Flow (Intra-Flow)

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

## Assisted Offload

There are  cases (crisis) where "normal" network
resources cannot be used at maximum and, thus, a network would seek to
reduce or offload some of the traffic during these events -- often
called 'reactive traffic policy'. An example of such sue case is cellular networks
that are overly used (and radio resources exhausted) while alternative network
attachment networks are available to host.

Network-to-host signals are
useful to put in place adequate traffic distribution policies (e.g.,
prefer the use of alternate paths, offload a network).

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
