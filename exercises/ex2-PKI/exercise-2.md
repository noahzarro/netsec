# Exercise sheet 2: Public Key Infrastructure

*21 September 2020*

Handing in this exercise sheet is optional.
If you want individual feedback for your solutions, you have to hand in your solution by the **Wednesday following exercise publication, September 30, at 23:59**. 
The hand-in procedure is as follows:

- copy this document, and answer the questions in the appropriate spaces;
- create a new issue on [the GitLab issue repo](https://gitlab.inf.ethz.ch/PRV-PERRIG/netsec-course/netsec-2020-issues);
- the issue title must be in the form `[exercise-hand-in] Exercise 1 {YOUR NETHZ ID}` (without curly braces); 
- you should set the issue as confidential;
- paste the modified document with your solution in the body of the issue.


### Question 1 
In December 2011, a Turkish CA named TurkTrust was involved in a rather
concerning incident. Back in August, the CA had wrongly issued two intermediate
certificates instead of normal ones. From that moment on, the two
certificate holders had effectively become intermediate CAs: they could
sign valid certificates for any domain they saw fit. On the
24<sup>*t**h*</sup> of December, Google detected a bogus certificate for
one of their domains, and the certificate was revoked shortly after.

**1.1.** (1 points)
What kind of attack was attempted by the fake CA?


*Solution:* Your solution here

**1.2.** (1 points)
Are the MitM victims able to tell that they are being attacked if no
HTTPS extension is being used?


*Solution:* Your solution here

**1.3.** (1 points)
Describe what the HTTP Strict Transport Layer Security (HSTS) header is, and if it could prevent a MitM attack.


*Solution:* Your solution here

**1.4.** (1 points)
Does the fact that Google has an EV certificate prevent a MitM attack?


*Solution:* Your solution here

**1.5.** 
Let us now examine how Certificate Transparency (CT) could prevent the
attack:


- (3 points)
List the parties involved in CT, and explain what role they play.


*Solution:* Your solution here

- (1 points)
How can the MitM attack be prevented by CT?


*Solution:* Your solution here

- (2 points)
Assume that the log server is controlled by the adversaries. Why can’t
this malicious log server send two different versions of the log, one to the victim and
one to the CAs (split view attack)?


*Solution:* Your solution here

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


*Solution:* Your solution here

**2.2.** (1 points)
CT is a great way of realizing just how many entities are signing certificates.
Think about it: the compromise of just one of them could have catastrophic
effects on all the users. How many CAs have issued certificates just for the
domain found in the previous question, since the logs started? Did you know
that the Government has its own CA?


*Solution:* Your solution here

**2.3.** (1 points)
Once a certificate is published in the CT, it stays there forever.
Therefore, CT contains a historical view of all active and expired
certificates. How does the number from the previous question vary if you
tick the other checkbox "Include certificates that have
expired", and click on the lens icon again? What do you think about this
number?


*Solution:* Your solution here

**2.4.** (2 points)
What happens if a certificate is revoked? How does CT handle revocations and
why?


*Solution:* Your solution here

### Question 3 (4 points)
In its current state, the Internet PKI requires the user to trust
hundreds[1] of certificate authorities. If even a single one of those
entities is compromised (or operated by malicious actors), security of
all the digital certificates in the PKI are susceptible of attacks. What
is a possible alternative architecture?

[1] <https://ccadb-public.secure.force.com/mozilla/IncludedCACertificateReport>

*Solution:* Your solution here

### Question 4 
In 2015, it emerged that many laptops sold by Lenovo contained a
preinstalled software package: “Superfish”. Superfish injected advertisement in
webpages visited by the users — notably, it was used to interfere with HTTPS
domains, too[1].

[1] [ https://www.eff.org/deeplinks/2015/02/further-evidence-lenovo-breaking-https-security-its-laptops]( https://www.eff.org/deeplinks/2015/02/further-evidence-lenovo-breaking-https-security-its-laptops)

**4.1.** (2 points)
How would you implement a software that intercepts HTTPS connections? Are you
exposing the users of your software to MITM by arbitrary parties?
 

*Solution:* Your solution here

**4.2.** (2 points)
Chances are high that you did a better job than Superfish authors in the
previous answer. Superfish installed an universal self-signed Certificate
Authority on victim's laptops. This CA was the same for all the instances of
Superfish, and its private key was included in the package. Let's say you
somehow [1] obtain this private key - how could you attack the unlucky owners
the laptops?
    
[1] <https://blog.erratasec.com/2015/02/extracting-superfish-certificate.html>

*Solution:* Your solution here


**4.3.** (2 points)
Which PKI extension measures could be used to prevent this or similar [1]
abuses of locally installed root CAs? (HPKP? HSTS? OCSP? CT?)

[1] <https://bugzilla.mozilla.org/show_bug.cgi?id=1567114>


*Solution:* Your solution here

**4.4.** (1 points)
It should by now be clear that intercepting TLS traffic presents many
challenges. Nevertheless, corporations and computer security applications
(so-called "antivirus software" [1]), are still widely used. Can you think of
other problems that could emerge even with a carefully designed lawful
interception?

[1] <https://bugs.chromium.org/p/project-zero/issues/detail?id=978>

*Solution:* Your solution here

### Question 5 
HTTP Public Key Pinning (HPKP) is a mechanism that could help with the
trust issues we described, or at least it was designed to do so.
In this question, we analyze the possible negative effects of using
HPKP. It’s interesting to note that, on May 2018, Google Chrome
officially deprecated this method.

**5.1.** (2 points)
Briefly explain how HPKP works and how it helps security.


*Solution:* Your solution here

**5.2.** (2 points)
When could a MitM attack succeed even if HPKP is used?


*Solution:* Your solution here

**5.3.** (3 points)
Sometimes managing HPKP policies can be problematic. The administrators
of the website Smashing Magazine learnt this the hard way, as their
pages became unavailable for most of the visitors for 4 days in 2016.
What could have happened? \[Hint: here is their PICKUP policy header\]

```
Public-Key-Pins: 
pin-sha256="35L+K6PY5ynTu15SYPrT8KXp5TRH8kzP46mYLpv9k30=";
pin-sha256="78j8kS82YGC1jbX4Qeavl9ps+ZCzb132wCvAY7AxTMw=";
pin-sha256="GQGOWh/khWzFKzDO9wUVtRkHO7BJjPfzd0UVDhF+LxM=";
max-age=31536000; includeSubDomains

```


*Solution:* Your solution here

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


*Solution:* Your solution here

**6.2.** (1 points)
Why are root certificates used only to sign intermediate
certificates, and not all other certificates?


*Solution:* Your solution here

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


*Solution:* Your solution here

**7.2.** (2 points)
A malicious user has access to a GIT repository. The user can push a
commit with a binary blob containing arbitrary data, and a code section.
Knowing that GIT will only rely on the SHA-1 of the commit for
identifying it, how could the malicious user exploit the SHA-1 weakness
to insert malicious code in the repository?


*Solution:* Your solution here

**7.3.** (1 points)
Some applications keep using SHA-1 — in fact it is believed to be hard,
given a document *d*<sub>1</sub>, to find a second document
*d*<sub>2</sub> = *d*<sub>1</sub> such that
*S**H**A*<sub>1</sub>(*d*<sub>1</sub>) = *S**H**A*<sub>1</sub>(*d*<sub>2</sub>).
Which property of an hash function SHAH-1 still appears to be able to
guarantee?


*Solution:* Your solution here

**7.4.** (2 points)
If somehow second pregame resistance of SHAH-1 were to be broken, what
would be the effect on GIT?


*Solution:* Your solution here

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

*Solution:* Your solution here
