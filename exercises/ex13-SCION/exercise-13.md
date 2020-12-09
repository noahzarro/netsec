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

*Solution:* Your solution here

**1.2.** (2 points)
On the other hand, how is the data (or “forwarding”) plane defined?

*Solution:* Your solution here

**1.3.** (3 points)
Are those two aspects clearly separated in the current Internet? (Think
about how packets are routed and forwarded, and in terms of when routing
decisions are made)

*Solution:* Your solution here

### Question 2 
**SCION vs IP**

**2.1.** (2 points)
How is an IP packet forwarded when in reaches a border router? What
happens instead in SCION?

*Solution:* Your solution here

**2.2.** (2 points)
How do those different routing architectures influence the size of a
packet?

*Solution:* Your solution here

**2.3.** (2 points)
Consider this scenario: network failure interrupts an inter-AS link, and
BGP eventually reconverges to start using a backup link. Besides the
connectivity interruption, how is a host using that link affected by a
change in the network topology? (Think about what information about the
routing is available to the end-hosts.)

*Solution:* Your solution here

**2.4.** (2 points)
Consider the same setting as above in SCION: what are the differences?
Can you think of an advantage of this approach?

*Solution:* Your solution here

**2.5.** (1 points)
Consider the same setting as above, with SCION, but in the case the
end-hosts are not SCION-aware (IP is used in the local network, SCION on
the border router). Are there still advantages compared to normal IP?

*Solution:* Your solution here

### Question 3 
**SCION vs BGP**

**3.1.** (2 points)
How are available paths determined in BGP? How does it compare to SCION?
(Be very concise.)

*Solution:* Your solution here

**3.2.** (2 points)
What happens when a link fails in BGP and SCION?

*Solution:* Your solution here

**3.3.** (2 points)
One key property of BGP is that ASes influence how traffic is routed
based on BGP policies. In SCION, how do ASes control how packets are
routed?

*Solution:* Your solution here

### Question 4 
In this exercise, we take a closer look at the control plane
implementation of SCION.

**4.1.** (1 points)
What is an ISolation Domain (ISD)? What is a Core AS?

*Solution:* Your solution here

**4.2.** (4 points)
Explain what is beaconing and how the distribution of PCBs (Path-segment
Construction Beacon) works.

*Solution:* Your solution here

**4.3.** (3 points)
What is a path segment? Why is it called that way? What is the
difference between up, down and core path segments?

*Solution:* Your solution here

**4.4.** (1 points)
Where is each type of path segment stored after the beaconing has ended?

*Solution:* Your solution here

### Question 5 
**DRKey**

**5.1.** (2 points)
Why is public key cryptography not suitable for per-packet
authentication at infrastructural level?

*Solution:* Your solution here

**5.2.** (4 points)
Consider the DRKey notation **K**<sub>X &#8594; Y</sub>. We want to use
this key to authenticate data. Is the flow of authenticated information
the same as the direction of key derivation? (For which AS is key
derivation fast? Which AS uses this key to generate MACs? Which one,
instead, verifies those MACs?)

*Solution:* Your solution here

**5.3.** (6 points)
Assume Host **H** in AS **A** sends an SCMP message, and AS **C**, that
receives this message, wants to authenticate it. Describe how DRKey can
be used for this purpose.

*Solution:* Your solution here

**5.4.** (4 points)
A colleague of yours believes they found a better way to implement
dynamically recreatable keys. In their proposal, ASs X and Y establish a
shared secret **SHSV**<sub>*X*, *Y*</sub>. Both ASs then use this
shared secret instead of the local **SV** for generating first-level keys.
Their claim is that now, since
**K**<sub>X &#8594; Y</sub> = **K**<sub>Y &#8594; X</sub>, both ASs can
benefit from fast key derivation time. Can you see a problem with this
design?

*Solution:* Your solution here
