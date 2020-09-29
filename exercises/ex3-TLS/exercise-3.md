# Exercise sheet 3: Transport Layer Security

*29 September 2020*

Handing in this exercise sheet is optional.
If you want individual feedback for your solutions, you have to hand in your solution by the **Wednesday following exercise publication, October 07, at 23:59**. 
The hand-in procedure is as follows:

- copy this document, and answer the questions in the appropriate spaces;
- create a new issue on [the GitLab issue repo](https://gitlab.inf.ethz.ch/PRV-PERRIG/netsec-course/netsec-2020-issues);
- the issue title must be in the form `[exercise-hand-in] Exercise 2 {YOUR NETHZ ID}` (without curly braces); 
- you should set the issue as confidential;
- paste the modified document with your solution in the body of the issue.

### Question 1 
Consider the following protocols and applications: do they provide Perfect Forward Secrecy? Briefly motivate your answer.

**1.1.** (1 points)
Signal[1]

[1] <https://www.signal.org/docs/>


*Solution:* Your solution here

**1.2.** (1 points)
WhatsApp[1]

[1] <https://www.whatsapp.com/security/>


*Solution:* Your solution here

**1.3.** (1 points)
Telegram Secret Chats [1]

[1] <https://core.telegram.org/api/end-to-end>


*Solution:* Your solution here

**1.4.** (2 points)
PGP over email[1]

[1] <https://tools.ietf.org/html/rfc4880>


*Solution:* Your solution here

**1.5.** (1 points)
Google Duo


*Solution:* Your solution here

**1.6.** (1 points)
Skype


*Solution:* Your solution here

**1.7.** (1 points)
WeChat


*Solution:* Your solution here

### Question 2 
You have been hired as a server administrator by the newest startup in
town. You have been tasked with the choice of the cipher suites that
your server should prefer. Translate the cipher suite names in natural
language, and explain why you should or should not use them to protect 
your server's communications.

**2.1.** (2 points)

`TLS_DH_WITH_AES_256_CBC_SHA`


*Solution:* Your solution here

**2.2.** (2 points)

`TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA`


*Solution:* Your solution here

**2.3.** (2 points)

`TLS_DHE_RSA_WITH_AES_128_GCM_SHA256`


*Solution:* Your solution here

**2.4.** (2 points)

`TLS_DH_anon_WITH_DES_CBC_SHA`


*Solution:* Your solution here

**2.5.** (2 points)

`TLS_RSA_WITH_RC4_128_MD5`


*Solution:* Your solution here

### Question 3 
With each update to TLS, researchers fix more and more vulnerabilities and design flaws in the protocol
TLS 1.3, for instance, discontinues EXPORT-strength ciphers and non-PFS cipher suites.
Answer the following questions on the attacker's capabilities, considering the attacker model provided.

**3.1.** (2 points)
An attacker can drop packet on the network. Can they downgrade the
negotiated TLS version?


*Solution:* Your solution here

**3.2.** (2 points)
An attacker can modify packets in the network. Can they downgrade the
negotiated TLS version?


*Solution:* Your solution here

**3.3.** (3 points)
Some (buggy) legacy web servers used to close the connection when
receiving an unknown TLS version field. As a workaround, many clients
started implementing a fallback mechanism: if connection was closed by
the server, they would attempt a new TLS handshake, but with a lower TLS
version number. What are the implications of this behaviour on downgrade
attacks?


*Solution:* Your solution here

**3.4.** (2 points)
What is the fundamental reason why this an attack is possible? (Think
about the content of the transcript when a fallback is executed)


*Solution:* Your solution here

**3.5.** (3 points)
What attack would be possible if we removed the transcript from the
ClientFinished message, while retaining it in the ServerFinished?


*Solution:* Your solution here

**3.6.** (2 points)
We want to prevent downgrade attacks on modern servers, while still
maintaining compatibility with legacy servers that exhibit aberrant
behaviour. What is a possible solution?


*Solution:* Your solution here

**3.7.** (2 points, bonus)
Why should a downgrade to SSLv3 be prevented at all costs?


*Solution:* Your solution here

### Question 4 (4 points)
You discover that your TLS library uses an insecure pseudo-random number
generator for nonces in the handshake. This PRNG has a very short period
— its output is repeated every 10<sup>5</sup> accesses. Propose a
possible attack, assuming a Dolev-Yao adversary[1].

[1] <https://en.wikipedia.org/wiki/Dolev%E2%80%93Yao_model>

*Solution:* Your solution here
