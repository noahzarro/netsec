# Exercise sheet 11: DNS Security

*24 November 2020*

Handing in this exercise sheet is optional.
If you want individual feedback for your solutions, you have to hand in your solution by the **Wednesday following exercise publication, December 2, at 23:59**.
The hand-in procedure is as follows:

- copy this document, and answer the questions in the appropriate spaces;
- create a new issue on [the GitLab issue repo](https://gitlab.inf.ethz.ch/PRV-PERRIG/netsec-course/netsec-2020-issues);
- the issue title must be in the form `[exercise-hand-in] Exercise 11 {YOUR NETHZ ID}` (without curly braces); 
- you should set the issue as confidential;
- paste the modified document with your solution in the body of the issue. 

### Question 1 
The `dig` tool can be used to perform DNS queries on Linux and MacOS
systems. In this question, we will perform a recursive lookup for the
domain `netsec.ethz.ch` using `dig`. Type the command
`dig +trace netsec.ethz.ch` to run a recursive search. Then, use the
output of the command to answer the following questions.

**1.1.** (1 points)
What is the difference between a stub and a recursive resolver?

**Solution**:
A stub resolver is a piece of software running on a client that sends
recursive DNS requests to a recursive resolver.  
A recursive resolver is a program that runs on a DNS server. It receives
recursive queries and starts an iterative query: it contacts all the
different servers at the different domain levels to get the final
answer.

**1.2.** (1 points)
DNS names are composed of labels separated by dots, and a server may
return a match for part of a query if it does not have the answer for
the whole domain name. Which servers have replied to your requests (IPs
and domain names)? What part of the domain did you request them?

**Solution**:
For example (you may have different responders):  

    Root (.): 192.168.0.1#53(192.168.0.1)
    ch.: 193.0.14.129#53(k.root-servers.net)
    ethz.ch.: 85.119.5.230#53(h.nic.ch)
    netsec.ethz.ch. (authoritative server): 129.132.250.8#53(ns2.ethz.ch)

**1.3.** (2 points)
What role do the NS records have in the resolution?

**Solution**:
NS records are given as answers to delegate the requested domain to
another server. They are mainly used to point the resolver towards a
lower level of the namespace hierarchy in a recursive query.

**1.4.** (2 points)
What is the Time To Live (TTL) value of the CNAME record of
`netsec.ethz.ch.`? Name one advantage and one disadvantage of a short
TTL?

**Solution**:
TTL is 3600. A shorter TTL leads to shorter living cache entries,
leading to faster refreshes for end users in case of an update (for
example, it might be useful if a server is hosted by a CDN). However,
this means that there will be more load on the DNS servers for doing the
recursive lookup again.

**1.5.** (2 points)
What is a CNAME record used for? Can you give an example of when it is
used in the real world?

**Solution**:
CNAME records are used for domain name aliasing. This means that you
point a domain name to another domain name, called the canonical domain,
thus resolving them to the same IP address. This is heavily used by
hosting companies.

### Question 2 
A recent publication, “Poison Over Troubled Forwarders: A Cache
Poisoning Attack Targeting DNS Forwarding Devices”[1], describes a DNS
cache poisoning attack against DNS forwarders. Answer the following
questions.

[1] <https://www.usenix.org/system/files/sec20-zheng.pdf>

**2.1.** (2 points)
You own the authoritative server for the domain `attacker.com`, and you
would like to trick a recursive resolver into accepting a record for
`victim.com`. Why is it **not** possible?

**Solution**:
The recursive resolver checks that the records it receives are
*in-bailiwick*, that is,
`Data for which the server is either authoritative, or else authoritative for an ancestor of the owner name.`
(from RFC 7719).

The name server for `attacker.com` is not authoritative for the
`victim.com` zone: any out-of-bailiwick glue record relative to 
the victim zone will be rejected.

**2.2.** (2 points)
The paper describes two classes of attacks against DNS recursive
resolvers: forging and defragmentation attacks. Why are those attack
still present in spite of in-bailiwick checks? Are they likely to
succeed?

**Solution**:
Both attacks exploit a race between the legitimate server answer and the
attacker’s. Remember that UDP is connectionless: if the attacker knows
that a recursive resolver just queried an authoritative server, they can
server. This bypasses in-bailiwick checks entirely: if the attack
succeeds, the recursive resolver cannot distinguish the legitimate
answer from the rogue one.
try to send a UDP packet containing a response before the legitimate

Both attacks are also of limited practical impact in the modern
Internet:

-   forging attacks require the adversary to guess the ephemeral UDP
    port used by the resolver and the DNS transaction ID;

-   defragmentation attacks only succeed if the legitimate server sends
    an answer bigger than the MTU (typically 1500 bytes).

**2.3.** (1 points)
What is a DNS forwarder? How does a forwarder relate to other pieces of
DNS infrastructure, such as recursive resolvers?

**Solution**:
From the paper: ”They sit in between stub and recursive resolvers, and
often serve as ingress servers for DNS clients (e.g., home wireless
routers). When a DNS forwarder receives a query, instead of performing
the resolution recursively, it simply forwards the query to an up-stream
recursive resolver.“

**2.4.** (2 points)
In the defragmentation attack against DNS forwarders described in the
paper, how does an attacker cause IP fragmentation without reducing the
Path Maximum Transmission Unit (PMTU)? Why does the attacker need to
control both a client of the target forwarder and an authoritative
server?

**Solution**:
Since the attacker controls a client and an authoritative server for
`attacker.com`, they can query the forwarder for the domain
`attacker.com`, and have the server return an oversized (more then 1500)
response which will be fragmented.

**2.5.** (2 points)
What are some countermeasures that a vulnerable DNS forwarder should
implement?

**Solution**:
Many DNS servers with forwarding capabilities are not vulnerable to the
attack described here, since they either:

-   cache results by query, rather than by record: this can slightly
    reduce cache performance, but eliminates cache poisoning attacks
    entirely, since a malicious answer will never affect returned
    records for other queries;

-   verify the response: when the forwarder receives CNAME records, it
    can perform an additional query for each of them to verify their
    correctness, or perform DNSSEC validation.

**2.6.** (2 points)
Why does this attack work on DNS forwarders and not on recursive
resolvers?

**Solution**:
A recursive resolver would detect the out-of-bailiwick CNAME from
`attacker.com` and reject it.

The attack does work on a forwarder because the recursive resolver only
sees a fragmented, legitimate response from the attacker authoritative
server: only the forwarder receives, ahead of time, the rogue fragment
containing a CNAME and an A record for `victim.com`. Note that the
forwarder cannot do the bailiwick check itself, since it did not perform
the recursive resolution and does not know which servers are
authoritative for a particular domain.

**2.7.** (2 points)
Let’s assume the attack succeeds, and an attacker in your LAN manages to
inject a poisoned cached A record for `google.com`. If you now try to
visit `google.com` with a web browser, what will happen?

**Solution**:
Your operating system will resolve `google.com` to an IP address
controlled by the attacker. Your web browser will attempt a connection
to a web server at that IP, and, since `google.com` is in the HSTS
preload list, fail immediately (with a huge red warning page) as the TLS
connection detects an invalid certificate.

### Question 3 
On 24th April 2018, the Ethereum wallet webapp MyEtherWallet found out
that a great base of their users was being redirected to a phishing
website[1]. The attackers were able to loot $150k worth of ETH[2] before
the legitimate DNS records were restored.

[1] <https://techcrunch.com/2018/04/24/myetherwallet-hit-by-dns-attack/>

[2] the cryptocurrency, not Eidgenössische Technische Hochschule

**3.1.** (1 points)
What kind of DNS attack is this?

**Solution**:
The attackers managed to impersonate the DNS service Amazon Route 53
through a BGP hijacking attack. From the DNS point of view, this can be
considered as an impersonation attack and DNS hijacking attack (since
the DNS responses were hijacked). A consequence of the hijack attack is
the poisoning of caches (since Google’s 8.8.8.8 open recursive resolver
and many others kept the malicious IP cached for some time), but this
can hardly be classified as a cache poisoning attack. Many media outlets
incorrectly did so.

**3.2.** (3 points)
How does this kind of DNS attack work? What section of the DNS reply is
abused?

**Solution**:
Attackers that gain access or impersonate an authoritative DNS server
for any domain can hijack any section of the replies, that is, they can
send wrong IPs as a reply. In this case, the abused section was ANSWER,
since the fake server just replied with a wrong IP address.

With regard to cache poisoning attacks, please note that just adding a
new record for the victim domain to the ADDITIONAL section (a so-called
*lame record*) is not enough: *bailiwick* checks, which allow a resolver
to detect unrelated resource records and reject the answer, exists since
early days of DNS (1980s). A 2008 vulnerability, discovered by Dan
Kaminsky, exploits low entropy in transaction IDs
(<http://unixwiz.net/techtips/iguide-kaminsky-dns-vuln.html>), and has
been fixed by all main DNS resolver implementations at least a decade
ago. For more information about attacks in the early 2010s read
<https://www.cs.cornell.edu/~shmat/shmat_securecomm10.pdf>.

**3.3.** (2 points)
Why are these attacks particularly challenging to mitigate?

**Solution**:
Because the source of the attack – BGP hijacking – is out of the control
of the legitimate owners of the DNS records. Furthermore the malicious
data can remain cached in servers for a very long time, and because the
cached results can be diffused very quickly to the other DNS resolvers.
Especially if the poisoned server is a widely used one.

**3.4.** (2 points)
The unfortunate users that had their money stolen missed a very
important hint in their browsing sessions. Which one? Why is this
alarming?

**Solution**:
The attackers did not have a valid TLS certificate for the domain. Just
think about how many people still ignore the browser security warnings.
This was last year.

### Question 4 
Due to privacy (<https://dnsdiag.org/>), security (see previous
exercise) concerns, DNS over HTTPS (**DoH**) is starting to see
widespread deployment. Some privacy enthusiasts welcomed this
development as positive[1], while many ISP and governments already
oppose it[2][3]. The last issue of the Internet Protocol Journal[4], and
many academic publications[5][6], on the other hand, have a more
skeptical position.

[1] <https://hacks.mozilla.org/2018/05/a-cartoon-intro-to-dns-over-https/>

[2] <https://www.theregister.co.uk/2019/07/06/mozilla_ukisp_vallain/>

[3] <https://arstechnica.com/tech-policy/2019/09/isps-worry-a-new-chrome-feature-will-stop-them-from-spying-on-you/>

[4] <http://ipj.dreamhosters.com/wp-content/uploads/2019/07/ipj222.pdf>,
you should really read this

[5] <https://arxiv.org/pdf/1906.09682.pdf>

[6] <https://dl.acm.org/citation.cfm?id=2665959>, you can use ETH’s ACM
subscription

**4.1.** (2 points)
DoH may effectively prevent your ISP form analyzing your DNS traffic –
but who is the implicit trusted party in DoH? Try to answer this
question for Firefox’s and Chrome’s DoH deployments (or deployment
plans).

**Solution**:
You are trusting your DoH provider. In the case of Firefox, Cloudflare.
Chrome upgrades to DoH if your (already configured) DNS has it available
and is in their list of supported services.

**4.2.** (2 points)
What are the economic incentives for running an DoH open DNS resolver?
What do Cloudflare’s 1.1.1.1 policies state on that purpose?

**Solution**:
The only real economic inventive for running an open DNS resolver is
data extraction and analysis. Current terms of service for both Google
and Cloudflare state that data from the resolvers won’t be used for
advertising, but those are non-binding and can change.

“Gaining reputation” can also be considered a valid answer, but it’s
surprising that some of DoH’s privacy promises seem to be based on the
presumed philanthropy of two internet giants.

**4.3.** (3 points)
What is the general effect of DoH with regards to the decentralization
of the Internet? Consider the case of Google and Cloudflare – what would
each of those companies control?

**Solution**:
DoH concentrates even more power in the hands of centralized players in
the Internet.

Google would control: browser, DNS queries and content (google’s own
content makes up a major fraction of Internet’s traffic). Cloudflare
would control: DNS queries and content (thought Cloudflare’s extensive
CDN)

**4.4.** (2 points)
Centralization poses both a political and technical problem: only
considering the latter, what is the effect of DoH on DNS caches? Why is
this not a problem for DoH performance?

**Solution**:
Local caches are no longer possible – each query needs to reach the
remote DoH resolver. In the case of large providers, load and latency
are not a problem: anycast is used to respond to the queries in a
geographically distributed way.
