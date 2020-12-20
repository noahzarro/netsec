# Exercise sheet 13: SCION

*09 December 2020*

Handing in this exercise sheet is optional.
If you want individual feedback for your solutions, you have to hand in your solution by the **Wednesday following exercise publication, December 16, at 23:59**.
The hand-in procedure is as follows:

- copy this document, and answer the questions in the appropriate spaces;
- create a new issue on [the GitLab issue repo](https://gitlab.inf.ethz.ch/PRV-PERRIG/netsec-course/netsec-2020-issues);
- the issue title must be in the form `[exercise-hand-in] Exercise 13 {YOUR NETHZ ID}` (without curly braces); 
- you should set the issue as confidential;
- paste the modified document with your solution in the body of the issue. 

### Question 1 
One of the principal aims of SCION is to provide users with transparency
and better control over the forwarding paths of network packets. Answer
concisely to the following questions.

**1.1.** (2 points)
In the context of routing architectures, what is the control plane?

**Solution**:
The control pane is the part of the architecture that deals with network
topology, making routing decisions. It is responsible for the discovery
of network paths, route establishment, switching to failover paths in
case of failures, and policing.

**1.2.** (2 points)
On the other hand, how is the data (or “forwarding”) plane defined?

**Solution**:
Data plane is responsible for forwarding data packets that end hosts
have injected into the network. Packets are forwarded according to the
routes established in the control plane.

**1.3.** (3 points)
Are those two aspects clearly separated in the current Internet? (Think
about how packets are routed and forwarded, and in terms of when routing
decisions are made)

**Solution**:
In IP, routing selects a path that is opaque to the end host. To forward
a packet, an IP router can determine the next hop solely based on the
destination of the packet, looking up a routing table. BGP is used to
disseminate routes between domains, and writes routes into the
forwarding tables.

Therefore, data and forwarding planes are not clearly separated: an IP
packet routing decision is made at forwarding time, and a route change
can modify how a packet is forwarded in-flight (potentially leading to
packet drops, or worse, routing loops). Tthis cannot happen in SCION,
where the two planes are more clearly separated.

### Question 2 
**SCION vs IP**

**2.1.** (2 points)
How is an IP packet forwarded when in reaches a border router? What
happens instead in SCION?

**Solution**:
The router looks up a routing table and (usually based on longest-prefix
matching of the destination address of the packet). The lookup returns
an exit infterface, to which the packet is forwarded. In SCION none of
that happens: the packet already contains the forwarding information, so
a router only needs to check the next hop information in the SCION
packet header.

**2.2.** (2 points)
How do those different routing architectures influence the size of a
packet?

**Solution**:
A SCION packet needs to contain all of its routing information (the full
path it will be forwarded through). Consequently, the header overhead is
higher compared to IP (where routing information is distributed on the
various hops in the path).

**2.3.** (2 points)
Consider this scenario: network failure interrupts an inter-AS link, and
BGP eventually reconverges to start using a backup link. Besides the
connectivity interruption, how is a host using that link affected by a
change in the network topology? (Think about what information about the
routing is available to the end-hosts.)

**Solution**:
The topology change will be almost completely transparent to end-hosts
running on the network: the routing is opaque for them. The end-host
will have no information on how a packet is routed, and cannot influence
the path they are forwarded on.

**2.4.** (2 points)
Consider the same setting as above in SCION: what are the differences?
Can you think of an advantage of this approach?

**Solution**:
The end-host will detect that the traffic on a certain path is not being
forwarded correctly (or the path will be revoked), and explicitly
failover to another path. In this case the end-hosts have full
information on the routing, and full power on its forwarding path.

This allows for fast failovers.

**2.5.** (1 points)
Consider the same setting as above, with SCION, but in the case the
end-hosts are not SCION-aware (IP is used in the local network, SCION on
the border router). Are there still advantages compared to normal IP?

**Solution**:
Yes: the border router can still respond to the topology change and do a
fast failover, while in BGP reconverging to a new path may possibly take
a long time.

There are proposals for implementing fast failover on BGP routers as
well. However, routing loops can be introduced in the case of multiple
topology changes.

### Question 3 
**SCION vs BGP**

**3.1.** (2 points)
How are available paths determined in BGP? How does it compare to SCION?
(Be very concise.)

**Solution**:
BGP: routing announcements are globally propagated. SCION: paths are
disseminated through beaconing, which is divided into core beaconing and
intra-ISD beaconing.

**3.2.** (2 points)
What happens when a link fails in BGP and SCION?

**Solution**:
In BGP, packets will queue up or be lost until the network converges to
a new states (self-healing). The time required for this process may be
on the order of minutes.

SCION is not self-healing: the failed paths will be revoked, but
end-hosts will need to be path-aware and select a new path for
communication. The time required for this process is in the order of a
few RTTs/beaconing intervals.

**3.3.** (2 points)
One key property of BGP is that ASes influence how traffic is routed
based on BGP policies. In SCION, how do ASes control how packets are
routed?

**Solution**:
ASes can choose to which customers to forward which beacons (PCBs). This
provides end-hosts with a set of possible paths that are already
conforming to policy, balancing expressiveness with economic incentives.

### Question 4 
In this exercise, we take a closer look at the control plane
implementation of SCION.

**4.1.** (1 points)
What is an ISolation Domain (ISD)? What is a Core AS?

**Solution**:
The architecture of SCION mandates that the Internet is partitioned into
ISDs, that are independently organized groupings of ASes. In each ISD,
part of the ASes form the core, which is responsible for managing the
whole ISD and has some special functions.

An ISD usually represents an area of common trust or of common
legislation (e.g. countries, multinational federations).

**4.2.** (4 points)
Explain what is beaconing and how the distribution of PCBs (Path-segment
Construction Beacon) works.

**Solution**:
Beaconing is the process through which paths are found in SCION. The
Core ASes in an ISD (Isolation Domain) periodically flood the ISD with
PCBs, by sending them in an anycast fashion (dubbed service anycast in
SCION). Any AS receiving this packet will send it to its beacon server,
that will add the current AS info and send the PCB to the ASes
downstream. When beacons reach leaf nodes in the AS graph, the process
is completed.

**4.3.** (3 points)
What is a path segment? Why is it called that way? What is the
difference between up, down and core path segments?

**Solution**:
A path segment is any contiguous subsequence of ASes contained in a PCB,
provided that at least one of the extremes is a core AS. They owe their
name to the fact that they represent different segments of a whole path.
Each path is in fact comprised of an up-path segment (from source AS to
a core AS), possibly a core-path segment (from core AS to core AS,
possibly on a different ISD), and a down-path segment(from core to
destination AS). Cross-overs are possible, allowing packets to go from
an up- to a down-segment without passing through the core.

**4.4.** (1 points)
Where is each type of path segment stored after the beaconing has ended?

**Solution**:
Every AS has its own path server.

-   Regular AS: contains up-path segments to reach the core ASes

-   Core AS: contains down-path segments and core-path segments

-   Caches: entries cached at various levels for faster setups

### Question 5 
**DRKey**

**5.1.** (2 points)
Why is public key cryptography not suitable for per-packet
authentication at infrastructural level?

**Solution**:
The fastest PK primitives are too slow for processing packets at high
speed (tens of gigabytes per second).

**5.2.** (4 points)
Consider the DRKey notation **K**<sub>X &#8594; Y</sub>. We want to use
this key to authenticate data. Is the flow of authenticated information
the same as the direction of key derivation? (For which AS is key
derivation fast? Which AS uses this key to generate MACs? Which one,
instead, verifies those MACs?)

**Solution**:
AS X can derive the key based on its SV efficiently, while AS Y needs to
fetch it form AS X.

Data flow is opposite to the key generation flow: AS Y uses
$K\_{X \\xrightarrow{} Y}$ for MACcing data, and X checks those MACs. In
this way, the burden of fetching the key (a slower operation) is on the
sender, while the receiver can always efficiently verify the data it
receives.

**5.3.** (6 points)
Assume Host **H** in AS **A** sends an SCMP message, and AS **C**, that
receives this message, wants to authenticate it. Describe how DRKey can
be used for this purpose.

**Solution**:

1.  First-level Key Exchange: AS C has its local secret
    *SV*<sub>*C*</sub>. It uses *SV*<sub>*C*</sub> to generate the
    first level key **K**<sub>C &#8594; A</sub>=PRF<sub>SV<sub>C</sub></sub>('A').
    AS C securely sends **K**<sub>C &#8594; A</sub> to AS A.

2.  Second-level Key Exchange: AS A generates a second-level key for H
    that can be used for source authentication by ASC, i.e.
    <var>**K**<sup>SCMP</sup><sub>C &#8594; A:H</sub></var>=<var>PRF<sub><var>**K**<sub>C &#8594; A</sub></var></sub></var>('H|SCMP')
    and gives it to host H.

3.  Host H authenticates its ICMP message using
    **K**<sup>SCMP</sup><sub>C &#8594; A:H</sub>.

4.  AS C dynamically recreates **K**<sup>SCMP</sup><sub>C &#8594; A:H</sub>
    by retrieving the first-level key that it generated in Step 1 and
    computing the second-level key that AS A computed.

5.  Finally it verifies the authenticity of the message.

**5.4.** (4 points)
A colleague of yours believes they found a better way to implement
dynamically recreatable keys. In their proposal, ASs X and Y establish a
shared secret **SHSV**<sub>*X*, *Y*</sub>. Both ASs then use this
shared secret instead of the local **SV** for generating first-level keys.
Their claim is that now, since
**K**<sub>X &#8594; Y</sub> = **K**<sub>Y &#8594; X</sub>, both ASs can
benefit from fast key derivation time. Can you see a problem with this
design?

**Solution**:
The shared secret needs to be propagated in the AS infrastructure: it
cannot any longer be derived locally on-the-fly. Therefore, rather than
providing fast derivation for both parties, this would make key
derivation always slow, because a key fetch is needed to authenticate
the data.
