# Exercise sheet 6: Anonymous Communication Systems

*20 October 2020*

Handing in this exercise sheet is optional.
If you want individual feedback for your solutions, you have to hand in your solution by the **Wednesday following exercise publication, October 28, at 23:59**.
The hand-in procedure is as follows:

- copy this document, and answer the questions in the appropriate spaces;
- create a new issue on [the GitLab issue repo](https://gitlab.inf.ethz.ch/PRV-PERRIG/netsec-course/netsec-2020-issues);
- the issue title must be in the form `[exercise-hand-in] Exercise 6 {YOUR NETHZ ID}` (without curly braces); 
- you should set the issue as confidential;
- paste the modified document with your solution in the body of the issue. 

### Question 1 
**ACS anonymity set**  
Alice and Bob are communicating using a circuit-based anonymous
communication system (ACS).

**1.1.** (1 points)
Normally, the fact that someone is using an ACS for communication is not
hidden. In other words, by observing the Internet traffic of Alice or
Bob, it is clear that they are using a particular ACS. Explain why this
may not be considered an issue, and in what cases instead it could be a
problem.

**Solution**:
An observer can not tell who is communicating with whom, if the set of
possible senders and receivers is large enough. If the usage of an ACS
can be correlated with activity from the participating nodes, this is a
problem. For instance, if the government wants to find out who sent a
particular message at a known time, they can try to identify the set of
people who were using the system at that time.

**1.2.** (1 points)
There are systems that try to disguise their traffic as regular web
traffic (in Tor, there are so-called *pluggable transports*[1]). Name
one possible scenario where this could be useful.

[1] <https://www.torproject.org/docs/pluggable-transports.html.en>

**Solution**:
-   Some organisations might block the access to ACS systems by using
    port / protocol based filtering for instance. By using traffic that
    looks like regular web traffic, ACS is harder to block.

-   If the assumption that “the set of people simultaneously using the
    ACS is large enough;” does not hold, some communication endpoints
    could easily be identified. By disguising the traffic,
    identification is harder (obfuscation).

### Question 2 (2 points)
**Malicious relay**  
You operate an ACS relay server. You think that Alice and Bob are
communicating with each other and you suspect that your server is on
their communication path.  
Assume that you can sniff the encrypted packets from/to Alice/Bob
directly on their Internet uplinks, but you cannot read the contents of
these packets as they are (onion-)encrypted.  
How can you verify whether Alice communicates with Bob over your server?
Assume you can monitor traffic in different locations and can see the
outer IP header data.

**Solution**:
As you operate the relay-server you can drop packets at will.  
By dropping packets in a certain pattern and observing the packets that
Alice sends, you can check if there are any retransmissions.
Retransmissions that match your drop pattern would lead to the
conclusion that the packets are from Alice. Note that to detect the
retransmissions, you have to look at the *timing* of the packets, as
encrypted packets differ from their retransmission (with very high
probability).  
By dropping all packets, we can check if no more messages arrive at
Bob’s, which would lead to the conclusion that the dropped packets were
destined to Bob.  
Note that this will be more precise than statistically comparing the packets
from Alice and the ones from Bob directly, because some layers of encryption
are removed by other relays in the ACS network.

### Question 3 (2 points)
**Circumvent anonymisation**  
ETH has taken control of a dark web *carder* website, and wants to
dismantle the operation: on this website, criminals buy (stolen) credit
card numbers. All users connect using circuits with telescopic setup and
you can’t compromise their relays. After a “customer” pays for the
credit card data, a `.pdf` file with the credit card numbers is sent to
the purchaser. How can you use this to deanonymise the criminals?

**Solution**:
We can send the malicious customer a document with the phone-home
feature (also known as call home), or malware: once opened, the document
will attempt to contact us, for example by performing a web request. If
this happens outside the secure channel, we will obtain the real IP
address of the criminal and, hopefully, deanonymise him. This could for
example be done by embedding javascript in PDFs.

### Question 4 (5 points)
**A new setup**  
In the context of circuit-based ACSes, a new setup has been proposed.
The author claims that it has the speed of a direct setup, but the
security of the telescopic setup (in particular, it is supposed to
provide perfect forward secrecy). The setup protocol is illustrated
below, shown with only two relays, *R*<sub>1</sub> and *R*<sub>2</sub>
for simplicity. The long-term Diffie-Hellman public keys of these relays
are *g*<sup>*Y*<sub>1</sub></sup> and *g*<sup>*Y*<sub>2</sub></sup>,
respectively. Lowercase letters denote ephemeral private keys:
*x*<sub>1</sub> and *x*<sub>2</sub> are ephemeral private keys generated
by the circuit initiator, while *y*<sub>1</sub> and *y*<sub>2</sub> are
ephemeral private keys generated by the relays *R*<sub>1</sub> and
*R*<sub>2</sub>, respectively.

<img src="assets/new-setup.png" style="width:90.0%" alt="image" />

In the diagram above, *l*<sub>1</sub> and *l*<sub>2</sub> denote
per-link IDs, while the parts of the messages denoted by
creat<sub>1</sub>, created<sub>1</sub>, creat<sub>2</sub>,
created<sub>2</sub>, relay<sub>1</sub>, are defined as follows:
<img src="assets/new-setup-table.png" style="width:90.0%" alt="image" />
Once the circuit initiator (Alice) has received all ephemeral public
keys of the relays (*g*<sup>*y*<sub>1</sub></sup> and
*g*<sup>*y*<sub>2</sub></sup>), she only uses the shared keys computed
based on these ephemeral public keys (i.e.,
*g*<sup>*x*<sub>1</sub>*y*<sub>1</sub></sup> and
*g*<sup>*x*<sub>2</sub>*y*<sub>2</sub></sup>) for the rest of the
circuit’s lifetime. Argue about the correctness of the claims about this
new setup: is it really faster? Does it provide immediate forward
secrecy?

**Solution**:
The proposed setup does not live up to its promise. While it is indeed
faster than the telescopic setup and possibly as fast as a direct setup,
it does not provide immediate forward secrecy, since the information
about the next hop is included in the creat message, and is encrypted
using a key that depends only on information that the adversary can see
on the wire (*g*<sup>*x*<sub>1</sub></sup>) and long-term private keys.

### Question 5 
**On the Internet, nobody knows you are a dog.[1] Maybe.**  
How many times have you heard about the death of online privacy? In this
exercise, we will analyze two methods that companies use to de-anonimyze
users, and we will see the impact they have on Tor browser.

[1] <https://en.wikipedia.org/wiki/On_the_Internet,_nobody_knows_you%27re_a_dog>

**5.1.** (2 points)
What is a widely used technique to track users browsing across many
websites? How can an advertising website use it to track your history?

**Solution**:
HTTP cookies can serve to this function. The ad servers, which serve the
ad banners included in many websites, save a cookie in the browser with
a unique identifier. Every time the browser loads a page in the future
that has a banner from the same company, it is going to include the
cookie. This way, the company can associate the id with the requesting
website.

**5.2.** (3 points)
How can you defend yourself from tracking cookies? How do hardened
browsers like the Tor Browser deal with this problem?

**Solution**:
You can set up your browser not to accept third-party cookies (cookies
that are not from the domain you are currently visiting), and to
immediately or periodically forget all the cookies. By default Tor
browser allows (first-party) cookies, but deletes them every time you
close the browser.

**5.3.** (4 points)
As time went by, companies had to get clever as research caught on with
their sneaky practices. They figured out they could create fingerprints
of users. How does it work? What information does it use? How does it
collect it and how does it hamper your privacy?

**Solution**:
The technique is called Browser fingerprinting. It is easy to create a
fingerprint of your browser based on some features and configuration
settings. In order of decreasing entropy: installed plugins, system
fonts, user agent, HTTP accept, screen resolution, time zone, and many
others. Websites do not need any permission: scripts run in your browser
and they can access your info, build the fingerprint and send it. Your
anonymity is in danger if you have a unique attribute or a combination
of them: unique fingerprint = tracking is possible!

**5.4.** (1 points)
Please go to <https://amiunique.org/> and run the test. Are you unique?
What’s the global percentage of unique devices?

**Solution**:
Probably yes.

**5.5.** (3 points)
What could be a solution to the problem of fingerprinting? How does the
Tor Browser deal with it?

**Solution**:
There are two possible directions:

-   Randomization: make the fingerprint look random, each user always
    has a new, random fingerprint.

-   Uniformity: make all the fingerprints look the same, all the users
    share a common fingerprint.

Both have their pros and cons, look at
<https://2019.www.torproject.org/projects/torbrowser/design/#fingerprinting-linkability>
for more information. <https://wiki.mozilla.org/Fingerprinting> has some
useful guidelines to protect yourself.

Tor Browser tries to enforce uniformity. All Tor Browser user supposedly
share the same fingerprint, increasing their anonymity set. Numerous
patches are used to limit plugins, ask permission for HTML Canvas,
restrict WebSockets, reduce available fonts, standardize the User-Agent
header, and limit many other fingerprinting techniques. Even window size
is considered sensitive: the browser will warn you if you attempt to
maximize its window (you would leak the size of your monitor).

**5.6.** (3 points)
In a controversial blogpost, Google’s Chome Engineering Director
affirmed: "\[...\] large scale blocking of cookies undermine people’s
privacy by encouraging opaque techniques such as fingerprinting." [1].
Google main source of income is advertising, and their position is
understandable [2] – nevertheless, blaming privacy-conscious users for
the advent of draconian tracking measures is not going to solve any
problem. In the blogpost cited above, Google proposes some new privacy
controls aimed to leverage cookie blocking. Highlight one strength and
one weaknesses of the proposed system. Can you imagine an alternative
revenue source for content producers in an personalized-ad free
Internet?

[1] <https://www.blog.google/products/chrome/building-a-more-private-web/>

[2] <https://services.google.com/fh/files/misc/disabling_third-party_cookies_publisher_revenue.pdf>

**Solution**:
Pros:

-   Better privacy controls are needed, even if their proposal seems
    hard to implement.

-   They say they will also restrict fingerprinting on Chrome, which is
    good.

-   “None” is an acceptable answer.

Cons:

-   Google has no incentive to really protect user’s privacy.

-   Cookies are too low-level for end-users to meaningfully understand
    which ones to block.

-   As the debate on privacy continues, many users may simply wish never
    to be tracked, which is not even contemplated by Google.

-   “Protect users form Spectre” is just wrong.

-   <https://freedom-to-tinker.com/2019/08/23/deconstructing-googles-excuses-on-tracking-protection/>

Some micropayment-based proposal emerged, e.g. Brave
(<https://brave.com/brave-ads-waitlist/>). Also, non-personalized ads
are a solution – industry can be forced to adapt to the decreased revenue 
in the presence of government regulation.
