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

A difficulty is traffic arriving from the content provider cannot be
differentiated from other traffic arriving from other hosts on the
Internet -- it is all incoming traffic.  But the receiving host would
like to indicate the traffic's importance to the host (or to the
application) to those bottleneck locations.  A solution to this
problem is configuring the ISP equipment with the content network's
source IP addresses and providing that traffic differentiated
access.  That differentiated access might be honoring the
DSCP bits of the traffic, providing lower latency or jitter,
assured bandwidth, or suchlike.  However such an arrangement
benefits large players (large ISPs and large content network)
but disadvantages small players and new players.  A more egalitarian
approach would provide the same benefit to all parties -- large
and small -- and also provide richer signaling to further improve
user experience. This allows all parties to become part of the
"Internet fast lane".

There is consensus that applications can benefit by signaling
the network ({{?IAB=RFC9419}}, {{ATIS}}).  This document provides
use-cases where such signaling is valuable.


## Everyone Is a Fire Engine

While DiffServ is a candidates technology which meets many of the
use-cases, the ISP's router has no reason to believe the DiffServ
code points set by the remote host, especially as some hosts will
set DSCP in an attempt to boost the speed of their traffic at the
detriment of other traffic the receiving host considers more important.

Other technologies being considered for host-to-network signaling
suffer a similar problem as DiffServ: they need authorization from the
receiving host, or else accidentally misconfigured senders or
purposefully misbehaving senders will interfere with networks, or else
only large ISPs and large CSPs will prevail.  These proposals include
embedding information in the UDP payload
({{?I-D.draft-trammell-plus-spec}}), UDP options
({{?I-D.draft-kaippallimalil-tsvwg-media-hdr-wireless}}), overloading
the IPv6 Flow Label ({{?I-D.draft-cc-v6ops-wlcg-flow-label-marking}},
and Hop-by-Hop Options.  Occasionally to provide the necessary
authorization those specifications suggest establishing a key to
mitigate against malicious use, see {{key}}.


Many approaches being considered today for host-to-network signaling
have a similar




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

#### Interactive Media

Examples: VoIP, gaming, virtual desktop.

Requirement:  Signal the flow needs low jitter and low delay.  However, the network can only provide
a limited amount of low jitter/low delay to each host, maybe as few as one.  This requires signaling
feedback indicating that low jitter and low delay flows are already subscribed to other hosts. In
response, the user and the application will likely continue, occasionally re-attempting to get the
desired quality of service from the network.

Todo: this section on cooperation needs editing.


#### Bulk Data Transfer

Examples: backup/restore, software update

Requirement: Signal the flow


## Priority Within a Flow (Intra-Flow)

Interactive Audio/Video has long been using {{?RTP=RFC3550}} which
runs over UDP.  As described in {{Section 2.3.7.2 of ?RFC7478}}, there
is value in differentiating between voice, video and data.  When video
streaming uses unreliable transport ({{?RFC9221}}) as proposed by
{{?I-D.draft-kpugin-rush}} it is beneficial to differentiate the
important video keyframes from other video frames.

As these types of flows are received over the Internet -- and not
from IP addresses known a priori -- there is no way to authorize
using packet markings from some sources while not authorizing
traffic markings from other sources.  In contrast, authorization
is implicit when cooperating content providers are sending
traffic that is known to be compliant with the ISP's expectations,
and those traffic source IP addresses are known a priori.

TODO.

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

TODO.

To be useful, the sender has to convey metadata in a way that is understood
by the various network elements on the path -- which might be operated by
different entities.  For example, the Wi-Fi access point might be operated
by an enterprise network, hotel, or home user, whereas the ISP's router
is operated by the ISP.  Each of those might support different versions
of the same metadata, or might need the metadata expressed in different
ways.

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
