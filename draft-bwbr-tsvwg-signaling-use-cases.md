---
title: "Signaling Use Cases for Traffic Differentiation"
abbrev: "Signaling Use Cases"
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

Host-to-network signaling can improve the user experience by informing
the network which flows are more important and which packets within a
flow are more important.  The differentiated service may be provided
at the network (e.g., packet prioritization), the sender (e.g.,
adaptive transmission), or through cooperation of both the sender
and the network.

This document outlines use-cases that highlight the need for a new
signaling protocol from the receiver to its network elements which
enables different traffic treatment.

--- middle

# Introduction

Bandwidth constraints exist most predominently at the access network.
Users of those networks run various hosts which run various
applications, each having different needs for the best user
experience.  These requirements are not fixed but change over time
depending on the application and even depending on how the application
is used.


The simple network diagram below shows where such bandwidth and
performance constraints usually exist with a "B".

~~~~~ aasvg

           +------+  |                     |          |
+----+     |Wi-Fi |  |  +------+  +------+ | +------+ | +----+
|host+--B--+access+--B--+router+--+router+---+router+---+host|
+----+     |point |  |  +------+  +------+ | +------+ | +----+
           +------+  |                     |          |
                     |                     | Transit  |  Content
   User Network      |    ISP Network      | Network  |  Network
~~~~~

For traffic sent in either direction, the network network element(s)
immediately prior to the bandwidth constraining link can be augmented
with flow metadata.  Such augmentation allows those network elements
to make autonomous decisions to prioritize, delay, or drop packets
especially to when performing Reactive Management.

A difficulty with this metadata augmentation is deciding which
metadata to trust.  Traffic arriving from a content provider cannot be
differentiated from traffic arriving from other hosts on the Internet.
The metadata signals from the content provider are more likely to be
authentic but the metadata signals from other hosts may be wrong,
undesired by the local host, or maliciously contain improper metadata.
Attempts to automate identification of content providers have included
HTTP "Host" header inspection and TLS SNI inspection which are
expected to fail as encrypted SNI and privacy-enhancin MASQUE proxies
become more prevalant.  A remaining mechanism to authorize metadata
signals from the content provider is to configure the ISP equipment
with the content network's source IP addresses and provide only that
traffic with differentiated service.  However, such an arrangement
benefits large players (large ISPs and large content network) and
disadvantages small players (and new players).  A more egalitarian
approach would provide the same benefit to all parties -- large and
small -- and also provide richer signaling to further improve user
experience and metadata interoperability. This would allow all parties
to become part of the "Internet fast lane".

The authorization problem exists with technologies as relatively
simple as DiffServ and the problem persists with many other
recently discussed metadata signaling mechanisms including
embedding information in the UDP payload
({{?I-D.draft-trammell-plus-spec}}), UDP options
({{?I-D.draft-kaippallimalil-tsvwg-media-hdr-wireless}}), overloading
the IPv6 Flow Label ({{?I-D.draft-cc-v6ops-wlcg-flow-label-marking}},
and Hop-by-Hop Options.  One mechanism suggested occasionally is
to encrypt or integrity protect the metadata with a key; such a key
could be established with a signaling protocol, see {{key}}.

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


# Use Cases


## Priority Between Flows (Inter-Flow)

Certain flows being received by an host or by an application are less or more important than other
flows.  For example, a host downloading a software update is generally considered less important
than another host doing interactive audio/video or gaming.  By signaling the relative importance
of flows to a network element (e.g., router, MASQUE proxy), the network element can (de-)prioritize
those flows to best accomodate the needs of the various applications (on a single host) and
between hosts on a network.

### Abuse and Constraints

It is important that not every flow be prioritized; otherwise, the
network devolves into the best-effort network that existed prior to
metadata signaling. It is a requirement that mechanisms exist to
prevent this occurrence. The mechanism might be simple, for example a
cellular network might allow one flow from a subscriber to declare
itself as important; other flows with that subscriber are denied
attempts to prioritize themselves.  The mechanism might be more
complex where authentication and authorization is performed by an
enterprise network which, itself, decides which flows are important
based on its policy and only the enterprise network communicates flow
priorities to the ISP network.  The enterprise might prioritize
certain users (e.g., IT staff, CEO), certain equipment (audio/video
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
{{?I-D.draft-kpugin-rush}}).  With unreliable transport of video in
RTP or QUIC, it is beneficial to differentiate the important video
keyframes from other video frames.  Other applications such as gaming
and remote desktop also benefit from differentiating their packets to
the network.

Many of these flows do not originate from a content provider's network.
Thus, the flows originate from an IP address that is not known before
connection establishment, so there needs to be a way for the client
to authorize the network elements to honor the metadata of those
packets.

## Key Establishment {#key}

Various proposals have suggested establishing a key to validate
per-packet metadata or to decrypt per-packet metadata.  However, most
proposals have not specified how this key would be established.  A
signaling protocol from the receiving host to its ISP could establish
such a key. The host can then convey the key to the sending host to
use to integrity protect or encrypt the per-packet metadata.

> Note: The CPU overhead of validating or decrypting such per-packet metadata
needs to be carefully considered by the signaling protocol proposing such
keying.

## Metadata Version/Capability Exchange

The sender has to convey metadata in a way that is understood by the
various network elements on the path -- each of which might be
operated by different entities and have different capabilities.  For
example, the Wi-Fi access point might be operated by an enterprise
network, hotel, or home user, whereas the ISP's router is operated by
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
