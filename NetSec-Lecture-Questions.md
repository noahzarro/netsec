Questions about NetSec Lecture
==============================

Lecture 7 (2020-10-27)
----------------------

- Why are ToR packets easily identified when looking at encrypted
    network traffic? Isn’t all the ToR specific payload contained in the
    encrypted payload of IP packets?

  - The boring answer is: by looking at the IP destination, if that
        IP address is a known entry guard. I also believe that TOR
        packets may, to a statistical analysis, look very much unlike
        normal traffic (in particular, the circuit estabishing part). We
        also know that TOR uses a custom protocol, and by packet
        inspection it may be possible to distinguish some of the TOR
        headers. *(Also answered during lecture at slide 55)*

- Is this telescopic setup build upon another protocol such as TLS or
    is Tor using its own protocol? Because it looks like its between L3
    and L4.

  - It's a custom KEX (Key Exchange protocol). It builds upon IP, so
        definitively L4.

  - A personal comment: I see no reason for TOR not to use TLS
        between circuits, if not header size.

- How is the connection between Bob and C1 (or OR2 here? Slide 51)
    secured or is it one way only from Alice to Bob?

  - The last hop of communication is normal IP, and not protected in
        any way. Think about TOR as a network layer: TOR anonymizes your
        source IP. It is up to Alice, now, to setup real end-to-end
        encryption, e.g. by using TLS to communicate with Bob.

  - Thanks for the great answer!

- Is there anything preventing the end-server that we connect to from
    fingerprinting our end-to-end TLS connection and be able to more or
    less identifiable anyhow? (Let's assume we aren't using the TOR
    browser which does some of this.) Is there even a way to do this?
    Seperate browser only used for TOR?

  - In short: no, the end server can fingerprint our browser. The
        solution is to use the standard TOR Browser
        <https://www.torproject.org/download/>
        or a separate virtual machine/OS dedicated to TOR (e.g. Whoonix
        or Tails, but the latter also uses the TOR Browser)

- What is the difference between Direct circuit setup and the TOR
    circuit creation? (It looks the same for me)

  - The TOR setup is telescopic: in a direct setup, the
        establishment of the circuit happens in a signle RTT, and uses
        the long term keys of all the circuit nodes involved (the client
        has no other cryptographic material established with the
        nodes).
        In a telescopic setup, instead, the circuit itself is forward
        secret: information about the second node is encrypted with an
        ephemeral key established with the first node during (and so on
        for all the nodes in the circuit).

  - Thank you :)

- So for the realy\_early DoS protection we rely on the first circuit
    node to be trustworthy in counting the number if extend request
    correctly, right? Otherwise it could just report a lower count. This
    only extends the path by the number of malicious nodes used in the
    beginning of the circuit.

  - Any node can do this check (not just the first), and any node
        that does not do this check will be part on the path of the
        attack, and is therefore incentivized to do it. *(Also answered
        during lecture at  slide 56)*

- So does an hidden service provider need to register its service at
    the Introduction Points \[IP\]? One? Or all? Do IPs communicate
    registered services?

  - The hidden (onion) service provider registers to a small set of
        Introduction Points. When resolving an onion address, a client
        will use a Distributed Hash Table (DHT, also use in bittorrent
        to exchange nodes) to get information on how to setup a circuit
        to the hidden service.

  - A more complete explanation:
        <https://community.torproject.org/onion-services/overview/>

  - (Hope this answers both questions)

Exercise 6 (2020-10-22)
-----------------------

- Can everyone attend the question hour, or is it one-to-one for those
    who asked questions beforehand? If everyone can attend, what would
    be the zoom link for the meeting?
  - The question hour seems to be taking place in the lecture hall
        today.
  - It's by Zoom, but as usual we stick around after exercise
        sessions to answer questions.
  - But there is no collective zoom meeting to discuss questions,
        right? Only one-to-one meetings for those who posted on
        GitLab?
    - Yes. If there is request for it, we can do a collective zoom
            meeting.
- Hi, I posted a private question for the question hour, but only an
    hour ago. Would it still be possible to get a zoom session with an
    assistant?
  - Sure. Hang on.
  - Ok, thanks

Lecture 6 (2020-10-20)
----------------------

- What happens in DH(E<sub>I</sub>, S<sub>R</sub>)?
  - We make a Diffie-Hellman key exchange between the client's and
        the server's ephemeral keypair.
  - \[ML\] I will add some details to the corresponding slide.
        Take private keys x and y; then x^{pub} = g^x and y^{pub}=g^y
        and DH(x, y) = (x^{pub})^y = (y^{pub})^x = g^{xy}.
- Shouldn't S\_R in the DH calcuation for the MAC be S\_R\_Pub?
    Because this looks like the initiator has access to the private key
    of the responder.
  - The shorthand notation indicates that we are using the private
        key we have access to and the public key of the other party,
        depending on who computes the DH share. (So yes, you are right:
        S\_R denotes the public key of the server in this case).
  - \[ML\] See also details in previous question.
- In DSSS: How can a receiver know that there is a communcation
    starting?
  - The receiver constantly listens while applying a "despreading
        code" to the signals it receives. The signal, once despread,
        will be the same as the original signal, and it is possible to
        see the communication start normally. (SOWN course cover this
        and similar topics in more detail)
- In the case of cascade of multiple proxies, could an adversary
    sitting on the links infer that Alice communicates with Bob based on
    the timing at which the packets are sent?
  - Yes, those systems are vulnerable to statistical attacks (this
        includes TOR). Mix-nets try to make this kind of correlation
        harder.
- Is the cascade of multiple proxies similar to tor network?
  - That is correct. Each of the proxy hops is similar to a layer of
        the "onion".
- Is there a zoom link? video.ethz.ch is really buggy and slow for me.
    If no, how do I access the recording?
  - No, this lecture is only broadcast through ETH streaming. The
        recording can be accessed at
        <https://video.ethz.ch/lectures/d-infk/2020/autumn/263-4640-00L.html>
  - If there are more reports of video.ethz.ch being slow, we can
        probably report this.
- How is the ID of the next relay protected (slide 28)? How can TLS
    help us?
  - I think slide 29 explains this, but if you have other questions
        I can try to answer them/relay them to Markus. TLS is used to
        implement the secure channel form the sender to each of the
        relays.
- Will exercice 4 solution be online soon?
  - I did not notice it was missing, thank you for reporting this.
        I'll upload it soon.
  - many thanks

Exercise 5 (2020-10-15)
-----------------------

### Guest Lecture

- What's the zoom link?
    [ahttps://ethz.zoom.us/j/96794315694](https://ethz.zoom.us/j/96794315694)
    ​​​​​​​

- Are there any simple but useful methods to hide your trace (such as
    ip, personal info) on the internet? Such as using a VPN.

  - Stay tuned for the anonymous-communication lecture next week. :)

### Exercise Session

- Is it possible to upload the slides for ex. sessions before the
    actual sessions so we can take notes on them? (If they are already
    on gitlab, could you tell me where?) Thanks!
  - I'll ask Matteo. I personally don't want to do it to encourage
        participation. (i. e. people can't already see the solution)
  - It is very convenient to have all material for the class in one
        place (lecture+ex slides) instead of having notes all over the
        place. However, I completely understand your point :))
- Can the bank also simply stop accepting 0-RTT for requests chaning
    state, i.e. accept 0-RTT only for GET requests? This would not
    require anything complicated for keeping track of PSK-s.
  - I believe that Cloudflare have implemented this way 
        "<https://blog.cloudflare.com/introducing-0-rtt/>"
        **GET requests with no query parameters - **but article from
        2017...
  - Pretty dated, but I think still works
  - =&gt; Works in principle but not all applications have GET
        requests which don't change state. So it would have to be kept
        in mind for designing the bank application. (answered shortly
        after 17:45)

Lecture 5 (2020-10-13)
----------------------

- Chaining multiple VPN's would make it harder for a single VPN to
    know who you are unless they all communicate with eachother.

  - That is correct.

- VPNs can be used to enable secure communication with legacy services
    which do not support TLS or other types of transport security.

  - Yes, that would work!

    - But the traffic between the VPN and the service would still
            not be encrypted, it only helps against an eavesdropper in
            our local network

      - Also, it helps if the legacy service provider is the one
                who offers the VPN endpoint. Think about using ETH VPN
                to access some HTTP-only resource inside ETH's
                network.

      - \[ML\] Indeed, this always depends on your attacker
                model / expectations. If you can reasonably assume that
                there is no eavesdropper between the VPN endpoint and
                the legacy service, a VPN is a good mechanism to use.

Exercise 4 (2020-10-08)
-----------------------

- No questions

Lecture 4 (2020-10-06)
----------------------

- So far we have always seen that diversity in encryption standards is
    a "bad" thing. But doesn't it also make it harder for attacker to
    attack a broader range of users if a vulnerability is discovered?
  - If the cryptographic primitives used in TLS 1.3 are all deemed
        very unlikely to be broken -- if they are, TLS traffic could be
        the last of our problems. New primitives can be added if
        necessary, by publishing new versions of the standard (that also
        aggressively remove old, insecure cryptography).
        Also note that breaks in modern cryptography tend to be gradual
        rather then explosive e.g. SHA-1: \~10 years from first attacks
        to full strong collision resistance violation (SHAttered).
- Does the "\*" after clientCertificate (and other components of
    slide 48) mean anything in particular?
  - They mean the message is optional (in slide 48, this is because
        client auth is optional).
- When using HTTPS: will there be a new TLS handshake for every GET
    request?
  - No: the underlying TLS (and TCP) connection will stay alive.
        They will be closed, eventually, after a timeout.
- The key computed from DH (g^xy) is used to encrypt the handshake. Is
    that also the key used for the AEAD part? My understanding is that
    we use a new key for that. Is that key also established during the
    handshake?
  - Slide 54 should answer this: a lot of different keys are derived
        in different parts of the handshake: in this case, the key used
        for AEAD are the client traffic key and the server traffic key,
        which are derived from CATS and SATS, which in turn are the
        result of a chain of HKDF evaluation that also takes the DH
        value as an input.

Exercise 3 (2020-10-01)
-----------------------

### Guest Lecture

- where are the exercise slides for the guest lecture? I cant find
    them on git

  - They have been added to the GitLab repository in the meantime.

- during the process to assign ipv6 addresses, what happens if an
    attacker prevent another device that already uses the advertised
    adress from responding?

  - Ok, so based on the question I just asked: IPv6 routers cache
        this kind of information for some time, until they detect the
        fact that the device is offline. (but I'm still not sure I fully
        understand your question

### Exercise Session

- Can we do other challenges or events besides the ETHZ network
    security one?
  - On Hacking Lab, you can do any challenges you have access to. We
        won't provide you with access to additional challenges, but
        there was an advent calender of challenges last year.
  - A shameless plug: ETH has a CTF team, flagbot. We play CTFs on
        weekends, both remotely and (now more rarely) at ETH.
        <https://flagbot.ch/>

Lecture 3 (2020-09-29)
----------------------

- Just curious, why is there a 10% decrease at the end of the line of
    windows (slide 5)?
  - Some statistical variations on the stats are common, they can be
        caused by events such as changing the way traffic is measured/
        integrating a new source of data. *(Answered only online)*
- Is there any compatibility between QUIC (over UDP, but which is
    reliable) and TLS?
  - TLS1.3 has a 0RTT resumption mode, and a TLS1.3-over-UDP
        protocol exists (DTLS), which is quite close to QUIC. In
        general, QUIC is not compatible with any TLS version, but the
        QUIC design influenced many TLS drafts, and most of the
        performance advantages originally brought by QUIC are today
        present in DTLS/TLS1.3. Moreover, the IETF version of QUIC now
        uses the same handshake as TLS1.3, but encapsulated in its own
        packet format. *(Also answered during explanation of slide
        22/23)*
- In the cipher-suite examples, SHA256 is mentionned. Do we talk about
    SHA1, SHA2 or SHA3 here?
  - SHA&lt;bits no&gt; usually refers to SHA2. For SHA-3, you would
        use SHA-3-256. Also note that SHA1 is 160-bit only *(Answered
        only online)*
- Slide 5 shows that google mail uses TLS at 100% for encryption of
    the mails. However, this is just encryption at the transport level,
    not end-to-end encryption, isn't it? Does it mean that on the google
    mail servers, the mails are not encrypted and they could still read
    your mail?
  - That is correct, TLS only protects transport level. Emails in
        gmail are not e2e encrypted, and they are (or, at least, used to
        be,
        <https://www.theguardian.com/technology/2017/jun/26/google-will-stop-scanning-content-of-personal-emails>)
        used for targeted advertisement. *(Answered only online)*
  - -&gt; But TLS at least encrypts the data "over the wire", right?
        But then the data is in plaintext on the email server?
    - TLS protects client-to-server and (often) server-to-server
            communication, so yes, someone looking at your traffic won't
            be able to read your emails. Google is likely to also
            encrypt data at rest, but has access to all plaintext. Some
            secure email solutions encrypt user data with a key derived
            from the user password, so that only the user can decrypt
            them. *(Answered only online)*
- What's a reflection attack?
  - In short: when you get a TLS endpoint to establish a connection
        to itself. *(Answered only online)*

Exercise 2 (2020-09-24)
-----------------------

- No questions

Lecture 2 (2020-09-22)
----------------------

- Why do intermediate CA certificates suffice for doing MitM?
  - The difference between root and intermediate is that root
        certificates are self-signed (and must be pre-installed to be
        trusted) whereas intermediate CAs are signed by another
        certificate with the authority to do so. Both kinds of
        certificates have the authority to issue new (non-CA)
        certificates and therefore suffice for MitM. *(Answered only
        online)*
- Question about short lived certificates. Isn't another approach to
    attack the CA authority issuing new certificates such that their
    service is not reachable anymore? This would cause a website to not
    be able to renew its certificate. Resulting in an invalid
    certificate used for user requests.
  - Good point, this is a point of concern. Fortunately, this attack
        isn't easy to do because the attacker would have to keep the
        attack on the CA up for a long time. Currently, short lived
        certificates are valid in the order of days and will be renewed
        some days in advance. *(Answered in the middle of second half,
        Let's Encrypt slide)*
- what is the visual difference between domain validation and OV/High
    assurance? they look the same to me
  - There are standards for these different validation levels.
        However, there are still differences between different CAs (OV
        and "High assurance" should be the same, the explanation was
        more about OV vs. EV) *(Answered shortly later, when on
        slide 32)*
- If a CA is compromised, can the attacker just hand out EV
    certificates? If not, what prevents the attacker from doing that?
  - Sometimes there are different entities which issue certificates
        of different levels (within the CA Organization). Potentially,
        the EV ones are better secured. *(Answered after the explanation
        of slide 33)*

Exercise 1 (2020-09-17)
-----------------------

- iBGP is uncommon for intra-domain routing?
  - --&gt; Yes, it is uncommon. While BGP messages are present
        within the domain, they usually concern routing on the outside.
        There may still be applications of BGP but they aren't common,
        so the rule of thumb shown on the slides applies. *(Recorded
        answer is shortly before the break.)*
- The question was about internal vs. external BGP (iBGP vs. eBGP). Is
    internal BGP uncommon for intra-domain routing?
  - --&gt; This question requires a more detailed explanation,
        please open an issue on GitLab for it. Then Markus Legner will
        answer it there.​​​​​​​
- Related to the question above: Why are there different routing
    protocols used for internal vs external networks? (e.g. BGP for
    external vs OSPF for internal). Is it because in the internal case
    we know the exact topology and how the network is layed out in
    detail? (vs. kind of a blackbox in the external case?)
  - --&gt; The protocols have different strengths. BGP is better
        equipped to deal with the policies between ASs. *(Recorded
        answer is directly after the break).*

Lecture 1 (2020-09-15)
----------------------

- Can lecture slides be made available ahead of time?
  - --&gt;
        [https://gitlab.inf](https://gitlab.inf.ethz.ch/PRV-PERRIG/netsec-course/netsec-2020-resources/-/tree/master/lectures)[.ethz.ch/PRV-PERRIG/netsec-course/netsec-2020-resources/-/tree/master/lectures](https://gitlab.inf.ethz.ch/PRV-PERRIG/netsec-course/netsec-2020-resources/-/tree/master/lectures)
- Will the lecture on thursday be recorderd and or livestreamed?
  - --&gt; yes, both.
- will the solutions be posted to each exercices, and if so when?
  - --&gt; yes, on Thursday before the exercise session
- When will the registration link of hacking lab be distributed?
  - In a few weeks, there will be an exercise session with more
        information on HackingLab.
- will there be other keyboard types different from US and DE?
  - --&gt; No, the ETH Exam Center only provides CH (QWERTZ) and US
        (QWERTY) keyboards. Regarding software keyboard layouts, we will
        check which ones are available on the examination computers.
- If I don't register for the exam, do project grades leave a (fail)
    mark on my transcript?If I deregister for the exam, do project
    grades leave a mark on my transcript?
  - --&gt; No. If you do not register for the exam, it will look
        like if you had not taken the course at all.
- What link can we use to watch the livestream of the exercises? is it
    the same as the lecture one? Thanks!
  - --&gt; No, it's the livestream of HG F1:
        <https://video.ethz.ch/live/lectures/zentrum/hg/hg-f-1/projector.html>
- How can we ensure that the IV is random & only used once in
    practice ?
  - --&gt; IVs are generated by a random number generator, which is
        typically implemented to generate randomness from highly variant
        system background data (e.g., mouse movements, CPU load
        fluctuations etc.). However, no random number generator is
        perfect, so crypto systems can always be attacked by finding
        imperfections in the used RNG.
