# Exercise sheet 2: Public Key Infrastructure

### Question 1 
In December 2011, a Turkish CA named TurkTrust was involved in a rather
concerning incident. Back in August, the CA had wrongly issued two intermediate
certificates instead of normal ones. From that moment on, the two
certificate holders had effectively become intermediate CAs: they could
sign valid certificates for any domain they saw fit. On the
24<sup>*th*</sup> of December, Google detected a bogus certificate for
one of their domains, and the certificate was revoked shortly after.

**1.1.** (1 points)
What kind of attack was attempted by the fake CA?

**Solution**: The fake CA performed
a Man in the Middle attack: they could impersonate Google to its
customers, monitor traffic, display bogus webpages instead of the
authentic ones. This is especially bad because it can lead to identity
theft and disclosure of private personal information.

**1.2.** (1 points)
Are the MitM victims able to tell that they are being attacked if no
HTTPS extension is being used?

**Solution**: No. From an HTTPS standpoint, the server
is using a valid certificate from a valid trust root, and is therefore
authentic. This attack breaks the assumptions of TLS, not the protocol
itself.

**1.3.** (1 points)
Describe what the HTTP Strict Transport Layer Security (HSTS) header is, and if it could prevent a MitM attack.

**Solution**: HSTS is a header that forces the browser to connect to the server with
HTTPS instead of HTTP. It would not help, since the attackers can
already read, drop or insert messages exchanged over HTTPS.

**1.4.** (1 points)
Does the fact that Google has an EV certificate prevent a MitM attack?

**Solution**: No. The EV status has no extra security if the trust chain is broken. It
could prevent the attack if the attackers forget about the EV, sign the
bogus certificate as normal, and the user notices the change.
Some browsers (e.g. Firefox 70, https://blog.mozilla.org/security/2019/10/15/improved-security-and-privacy-indicators-in-firefox-70/) even dropped the UI indicator for EV certificates in the URL bar.
Supposedly, EV certificates are only issued by a restricted subset of CAs which meets some additional criteria. This set is not really restricted: for example, Mozilla's root store includes 148 CA certificates, 77 of which are EV-enabled.
https://ccadb-public.secure.force.com/mozilla/IncludedCACertificateReport

**1.5.** 
Let us now examine how Certificate Transparency (CT) could prevent the
attack

- (3 points)
List the parties involved in CT, and explain what role they play.

**Solution**:
1. Log Server. Contains a list of certificates that is publicly available. Must
add the certificates submitted by the CAs.  
2. CAs (monitors). Request the addition of every newly issued cert,
check that the log server actually adds the certificates, monitor what
the other CAs are doing.  
3. Clients (auditors). Can verify the existence of certificates by
checking the log server, and exchange information with the monitors
about the log server status, in order to ensure the log server is not
compromised.

- (1 points)
How can the MitM attack be prevented by CT?

**Solution**: If TurkTrust implemented CT in the first place, the bogus intermediate CAs 
would have been immediately noticed.

Otherwise, the attacker would have had two choices:

-   Insert the certificate in CT: monitors observing the log would
    realise that a bogus certificate has been added and should not be
    there.  
    This way, the time the attacker has to succeed is drastically
    reduced, as his fake certificate is detected and revoked quickly.

-   Keep the certificate out of CT: auditors may notice the certificate
    is not present in any CT log; browsers that expect a log SCT stapled
    to the certificate won’t recognize it as valid.

The CT could therefore pose a mayor problem for the attacker — avoiding
CT insertion might become harder as more CAs integrate it in their
systems — but as of now the deployment of OSCP stapling combined with CT
is not yet widespread, and an out-of-log certificate still represents a
risk.

- (2 points)
Assume that the log server is controlled by the adversaries. Why can’t
this malicious log server send two different versions of the log, one to the victim and
one to the CAs (split view attack)?

**Solution**: In the log server, certificates are saved
in a signed Merkle tree. The digital signature is non-repudiable, so it
could be easily proven that the server was the culprit and actually sent
two different versions. Auditors and CA will notice, because they
independently exchange their view of the log.

### Question 2 
The Certificate Transparency Logs provide an interesting way of acquiring
information about websites, subdomains and CAs. For this assignment,
please visit Google CT website at
<https://transparencyreport.google.com/https/certificates>. When the
page has finished loading, you can search for "admin.ch", the Swiss
Government website, ticking the checkbox "Include subdomains".

**2.1.** (1 points)
The Certificate Transparency logs turns subdomains that are not intended
to be publicly known into public entries. Can you find a subdomain that
belongs to this category?

**Solution**: A good example could be `certificate-renewal.a-pki.admin.ch`.

**2.2.** (1 points)
CT is a great way of realising just how many entities are signing certificates.
Think about it: the compromise of just one of them could have catastrophic
effects on all the users. How many CAs have issued certificates just for the
domain found in the previous question, since the logs started? Did you know
that the Government has its own CA?

**Solution**: The solution depends on how you interpret the term CA in this
context. Either you look at the organization field, arguing that it's really
the organizations that you have to trust. Or you count each issuer as they are listed
in the search results. This effectively counts all the intermediate CA certificates
that have signed certificates for the domain.

For the domain `admin.ch` (including subdomains), there are 19 organizations and 
53 issuers.

For the domain `certificate-renewal.a-pki.admin.ch`, there are 2 organizations and
3 issuers.

**2.3.** (2 points)
What happens if a certificate is revoked? How does CT handle revocations and
why?

**Solution**: The certificates stay on the log forever: Merkle trees allow for
easy insertion but not for immediate deletion. The current system
implements revocation transparency, which is very similar to CT, but for
revocation requests.

### Question 3 (4 points)
In its current state, the Internet PKI requires the user to trust
hundreds[1] of certificate authorities. If even a single one of those
entities is compromised (or operated by malicious actors), security of
all the digital certificates in the PKI are susceptible of attacks. What
is a possible alternative architecture?

[1] <https://ccadb-public.secure.force.com/mozilla/IncludedCACertificateReport>

**Solution**: Many alternative architectures to the Internet PKI have been proposed. ARPKI,
for example, increases the number of entities an attacker would need to
compromise before being able to impersonate a domain. A completely
different approach is common in the PGP community: the “web of trust” is
a distributed public key infrastructure, in which user personally verify
and sign each other’s keys — given enough cross-signatures, it is likely
that there exist a trusted chain of signature between every two users.

Trust agility — the ability for the user to choose which entities to
trust — could represent an important element in future developments of
PKIs. An user could pick a few authorities they really trusts, like a
national government or a particular organization, and require additional
certification for entities not directly covered by them.

### Question 4 
In 2015, it emerged that many laptops sold by Lenovo contained a
preinstalled software package: “Superfish”. Superfish injected adverisement in
webpages visited by the users — notably, it was used to interfere with HTTPS
domains, too[1].

[1] [ https://www.eff.org/deeplinks/2015/02/further-evidence-lenovo-breaking-https-security-its-laptops]( https://www.eff.org/deeplinks/2015/02/further-evidence-lenovo-breaking-https-security-its-laptops)

**4.1.** (2 points)
How would you implement a software that intercepts HTTPS connections?
Are you exposing the users of your software to MITM by arbitrary
parties?

**Solution**: In general, it
is not a good idea to intercept TLS traffic — if asked to do so, you
should question the ethics of the request before proceeding.

You can install a self-signed local root CA and mount a MITM attack
using this CA to sign fake certificates for the domains the user is
visiting. If the CA is different for every user you are MITMing, you are
not exposing the users to MITM by arbitrary parties.

**4.2.** (2 points)
Chances are high that you did a better job than Superfish authors in the
previous answer. Superfish installed an universal self-signed Certificate
Authority on victim's laptops. This CA was the same for all the instances of
Superfish, and its private key was included in the package. Let's say you
somehow [1] obtain this private key - how could you attack the unlucky owners
the laptops?
    
[1] <https://blog.erratasec.com/2015/02/extracting-superfish-certificate.html>

**Solution**: All (unwitting) users of Superfish are now exposed to MITM attacks
from you — nothing restricts the certificates generated by the CA to
only be used locally, and with the private key you can sign arbitrary
certificates.

e.g. you could impersonate ubs.com — the browser would accept the
certificate signed by Superfish CA as valid without showing any warning.

**4.3.** (2 points)
Which PKI extension measures could be used to prevent this or similar [1]
abuses of locally installed root CAs? (HPKP? HSTS? OCSP? CT?)

[1] <https://bugzilla.mozilla.org/show_bug.cgi?id=1567114>

**Solution**:

-   HSTS: only enforces HTTPS, no information on which certificate to
    use.

-   HPKP: could in principle, but in practice:

    > *Chrome does not perform pin validation when the certificate chain
    > chains up to a private trust anchor. A key result of this policy
    > is that private trust anchors can be used to proxy (or MITM)
    > connections, even to pinned sites. “Data loss prevention”
    > appliances, firewalls, content filters, and malware can use this
    > feature to defeat the protections of key pinning. (extract from
    > <https://chromium.googlesource.com/chromium/src/+/master/docs/security/faq.md#how-does-key-pinning-interact-with-local-proxies-and-filters>)*

-   OCSP: will stop the attack — the browser will send the rogue
    certificate to an OSCP server for verification, which will recognize
    it as invalid.

-   CT: will help to expose the misbehaviour (if the certificate is
    logged/an auditor fetches it), won’t stop the browser from accepting
    the certificate.

-   CT+OCSP Stapling: will stop the attack — the browser will expect a
    CT log signature to be stapled to the certificate, but the local CA
    would be unable to produce that.

Nauturally, even the OCSP and CT+OSCP will fail if rogue OSCP and CT log
servers were installed.

**4.4.** (1 points)
It should by now be clear that intercepting TLS traffic presents many
challenges. Nevertheless, corporations and computer security applications
(so-called "antivirus software" [1]), are still widely used. Can you think of
other problems that could emerge even with a carefully designed lawful
interception?

[1] <https://bugs.chromium.org/p/project-zero/issues/detail?id=978>

**Solution**:

-   User interaction is required for some TLS errors — responsibility
    for this interaction would be moved to the MITM box.

-   EV certificates are displayed differently in the browser — the MITM
    box should handle them and forge a certificate for the correspondent
    legal entity.

-   The MITM box should regularly update its CRLs for correctly handling
    revoked certificates.

### Question 5 
HTTP Public Key Pinning (HPKP) is a mechanism that could help with the
trust issues we described, or at least it was designed to do so.
In this question, we analyze the possible negative effects of using
HPKP. It’s interesting to note that, on May 2018, Google Chrome
officially deprecated this method.

**5.1.** (2 points)
Briefly explain how HPKP works and how it helps security.

**Solution**: A server implementing HPKP will provide the client with a list of hashes of valid
certificates for the domain. This list is delivered in an HTTP header
that also contains a `max-age` value. The client will then “pin” those
hashes — subsequent connection within the `max-age` period will only
succeed if the certificate offered by the server matches the pinned
hashes for that domain. A MitM with a different certificate would
therefore fail.

**5.2.** (2 points)
When could a MitM attack succeed even if HPKP is used?

**Solution**:HPKP is TOFU — Trust On First Use.
If the first policy received comes from an attacker,
the browser will be happy to communicate with the bogus website and will
block the legitimate server. Also if a weak hash function is used, the
attacker could generate a certificate with a colliding hash — but in
that case HPKP would be the last of our problems.

**5.3.** (3 points)
Sometimes managing HPKP policies can be problematic. The administrators
of the website Smashing Magazine learnt this the hard way, as their
pages became unavailable for most of the visitors for 4 days in 2016.
What could have happened? \[Hint: here is their HPKP policy header\]

```
Public-Key-Pins: 
pin-sha256="35L+K6PY5ynTu15SYPrT8KXp5TRH8kzP46mYLpv9k30=";
pin-sha256="78j8kS82YGC1jbX4Qeavl9ps+ZCzb132wCvAY7AxTMw=";
pin-sha256="GQGOWh/khWzFKzDO9wUVtRkHO7BJjPfzd0UVDhF+LxM=";
max-age=31536000; includeSubDomains

```


**Solution**: The certificates for the website were renewed (this happens 
very frequently if LetsEncrypt is used, for instance) but every previously 
connected user still held the old policy valid, as max-age hadn’t expired yet. When the
new packets arrived, the browsers just concluded that they were being
attacked with a bogus certificate. The admins DoSsed themselves. For a
not-so-enthusiastic description of HPKP:

<https://www.smashingmagazine.com/be-afraid-of-public-key-pinning/>

### Question 6 
In 2018, the CEO of Trustico (a SSL certificate reseller) sent 23,000
private keys in an e-mail to DigiCert[1] after DigiCert refused to
revoke the corresponding certificates requiring a proof that the
certificates had been compromised.

[1] <https://goo.gl/xwVyX6>

**6.1.** (2 points)
There are many security concerns with the CEO’s action. Firstly, what can be
done with a compromised private key? What is the biggest concern regarding bad
practices in Trustico [Hint: think about who usually knows private keys]?

**Solution**:
With a certificate private key we can decrypt any traffic encrypted by
the public key in the certificate. This enables us to mount a MitM
attack.  
Private keys should be known only by the owner of the website. Trustico
shouldn’t have even known the private keys in the first place.

**6.2.** (1 points)
Why are root certificates used only to sign intermediate
certificates, and not all other certificates?

**Solution**:
Root certificates need to be as safe as possible. Revoking even one of
them would mean that all the certificates down the chain become invalid,
with disastrous consequences. This is the reason why root keys are kept
offline and used as rarely as possible.

### Question 7 
Git[1] is a version control system. Git relies strongly on SHA-1 for
file integrity and for identifying changes to the code (*commits*).
SHA-1 is known[2] to be susceptible of collision attacks.

[1] <https://git-scm.com/>

[2] <https://shattered.io/>

**7.1.** (1 points)
The SHAttered attack on SHA-1 proved it possible to generate two
documents, *d*<sub>1</sub> ≠ *d*<sub>2</sub>, such that
*S**H**A*<sub>1</sub>(*d*<sub>1</sub>) = *S**H**A*<sub>1</sub>(*d*<sub>2</sub>).
Which (desirable) property of an hash function has been violated?

**Solution**: Strong collision resistance.

**7.2.** (2 points)
A malicious user has access to a GIT repository. The user can push a
commit with a binary blob containing arbitrary data, and a code section.
Knowing that GIT will only rely on the SHA-1 of the commit for
identifying it, how could the malicious user exploit the SHA-1 weakness
to insert malicious code in the repository?

**Solution**: The user can generate two
commits, only one of which contains malicious code. He could then
manipulate the binary blob until the commits have the same hash. At this
point, he could push the innocuous commit, get it accepted and signed in
the official repo, but distribute copies of the repo containing the
malicious commit — signatures would stay valid. For an user getting the
modified version of the repository, it would be hard to even notice that
two different version of the code even exists.

**7.3.** (1 points)
Some applications keep using SHA-1 — in fact it is believed to be hard,
given a document *d*<sub>1</sub>, to find a second document
*d*<sub>2</sub> ≠ *d*<sub>1</sub> such that
*SHA*<sub>1</sub>(*d*<sub>1</sub>) = *SHA*<sub>1</sub>(*d*<sub>2</sub>).
Which property of an hash function SHA-1 still appears to be able to
guarantee?

**Solution**: Weak collision resistance, also known as second preimage
resistance. Note that the formulations of strong and weak collision
resistance are only apparently similar:

Strong:
∃(*d*<sub>1</sub>, *d*<sub>2</sub>), *d*<sub>1</sub> ≠ *d*<sub>2</sub>.*SHA*<sub>1</sub>(*d*<sub>1</sub>) = *SHA*<sub>1</sub>(*d*<sub>2</sub>)

Weak: given *d*<sub>1</sub>,
∃*d*<sub>2</sub>, *d*<sub>1</sub> ≠ *d*<sub>2</sub>.*SHA*<sub>1</sub>(*d*<sub>1</sub>) = *SHA*<sub>1</sub>(*d*<sub>2</sub>)

**7.4.** (2 points)
If somehow second preimage resistance of SHA-1 were to be broken, what
would be the effect on GIT?

**Solution**: Users would be able to generate collision
for commits produced by other users — proving the authenticity of code
in the repository would become difficult.

### Question 8 (4 points)
Let *h* be a cryptographic hash function. The probability of a collision
between two hash values is *p*<sub>*c*</sub>. An attacker has limited
computing power, and can only generate *n* = 2<sup>*k*</sup>, *k* &gt; 1
messages and the relative hashes. Calculate the probability of:

-   an hash collision between the *n* generated messages and a given
    message *m*

-   an hash collision between *n*/2 generated messages with a certain
    content and the remaining *n*/2 messages.

In which case is the collision more likely?

**Solution**: The probability of finding a collision is
*P*(*h*<sub>*i*</sub> = *h*<sub>*j*</sub>) = 1 − *P*(∀*i*, *j*.*h*<sub>*i*</sub> ≠ *h*<sub>*j*</sub>).
We can assume all the hash are independent, and since the probability of
a collision is *p*<sub>*c*</sub>,
*P*(*h*<sub>*i*</sub> ≠ *h*<sub>*j*</sub>) = 1 − *p*<sub>*c*</sub>. It
follows that
*P*(*h*<sub>*i*</sub> = *h*<sub>*j*</sub>) = 1 − (1 − *p*<sub>*c*</sub>)<sup>*a*</sup>,
where *a* is the number of attempts.

In the first case (weak collision resistance), the number of attempts
would be *n*.

In the second case (strong collision resistance), the number of attempts
would be (*n*/2)<sup>2</sup>.

It easy to see that *n* &lt; (*n*/2)<sup>2</sup> for *n* &gt; 2, and
that *P*(*h*<sub>*i*</sub> = *h*<sub>*j*</sub>) is monotonic
non-decreasing. Therefore, given the same computing power, strong
collision resistance is more likely to be violated than weak resistance.
