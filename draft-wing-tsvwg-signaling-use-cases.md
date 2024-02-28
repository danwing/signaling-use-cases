---
title: "Signaling Use Cases for Traffic Differentiation"
abbrev: "Signaling Use Cases"
category: info

docname: draft-wing-tsvwg-signaling-use-cases-latest
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

Host-to-network signaling can improve
the user experience by informing the network which flows are more important and which packets
within a flow are more important. The differentiated service may be provided at the network (e.g., packet prioritization), the server (e.g., adaptive transmission), or both.

This document outlines some use-cases and their requirements, and also explains deficiencies
in existing standard mechanisms.



--- middle

# Introduction

TODO Introduction



# Conventions and Definitions

Intentional Management:
: network policy such as (monthly) bandwidth quota or bandwidth limit, or quality (delay and/or jitter)) assurances.

Reactive Management:
: network reactions to congestion events, with very short to very long
durations  (e.g., varying wireless and mobile air
interface conditions).


# Use Cases
## Priority Between Flows (Inter-Flow)

## Priority Within a Flow (Intra-Flow)

Interactive Audio/Video has long been using {{?RTP=RFC3550}} which runs over UDP.  As described in
{{Section 2.3.7.2 of ?RFC7478}}, there is value in differentiating between vooice, video and
data.




# Requirements

# Security Considerations

TODO Security


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
