# Exercise sheet 3: TLS

*28 September 2020*

Handing in this exercise sheet is optional.
If you want individual feedback for your solutions, you have to hand in your solution by the **Wednesday following exercise publication, October 7, at 23:59**. 
The hand-in procedure is as follows:

- copy this document, and answer the questions in the appropriate spaces;
- create a new issue on [the GitLab issue repo](https://gitlab.inf.ethz.ch/PRV-PERRIG/netsec-course/netsec-2020-issues);
- the issue title must be in the form `[exercise-hand-in] Exercise 2 {YOUR NETHZ ID}` (without curly braces); 
- you should set the issue as confidential;
- paste the modified document with your solution in the body of the issue.

### Question 1 
Consider the following protocols and applications and research whether
they provide Perfect Forward Secrecy. Briefly motivate your answer.

**1.1.** (1 points)
Signal[1]

[1] <https://www.signal.org/docs/>

**Solution**:
Yes. X3DH and fresh DH in the ratchet chains.

**1.2.** (1 points)
WhatsApp[1]

[1] <https://www.whatsapp.com/security/>

**Solution**:
Yes. It uses the Signal Protocol.

**1.3.** (1 points)
Telegram Secret Chats [1]

[1] <https://core.telegram.org/api/end-to-end>

**Solution**:
Arguable. Re-keying every 100 messages.

**1.4.** (2 points)
PGP over email[1]

[1] <https://tools.ietf.org/html/rfc4880>

**Solution**:
No. PGP has no perfect forward secrecy, if the private key is leaked it
can be used to decrypt all past communication.

**1.5.** (1 points)
Google Duo

**Solution**: 
Signal Protocol. <https://www.gstatic.com/duo/papers/duo_e2ee.pdf>
(Note: if you read the whitepaper, the *state recovery* part is slimy worrying)

**1.6.** (1 points)
Skype

**Solution**:
Current (2020) status of end-to-end encryption for normal conversations unclear. Many media outlet report that Skype is now using the Signal protocol by default, but there is no official source, and no documentation of how the Signal protocol is instantiated for calls.

**1.7.** (1 points)
WeChat

**Solution**: 
<https://citizenlab.ca/2020/05/we-chat-they-watch/>

### Question 2 
You have been hired as a server administrator by the newest startup in
town. You have been tasked with the choice of the cipher suites that
your server should prefer. Translate the cipher suite names in natural
language and write your thoughts about the following suites, explaining
if you would like them to be on your whitelist.

**2.1.** (2 points)
TLS\_DH\_WITH\_AES\_256\_CBC\_SHA

**Solution**:
Key exchange: Fixed Diffie-Hellman  
Authentication: missing! This is not a valid cypher suite name!  
Encryption: 256-bit AES CBC mode  
MAC: 160-bit SHA-1  
Fixed Diffie-Hellman does not provide perfect forward secrecy. SHA-1 is
not collision resistant, but does not pose significant security
concerns. CBC mode should be avoided due to padding oracle attacks. Not
in whitelist.

**2.2.** (2 points)
TLS\_ECDHE\_RSA\_WITH\_AES\_128\_CBC\_SHA

**Solution**: 
Key exchange: Ephemeral Elliptic Curve Diffie-Hellman  
Authentication: RSA  
Encryption: 128-bit AES CBC mode  
MAC: 160-bit SHA-1  
Ephemeral Diffie-Hellman provides perfect forward secrecy and is secure
against MitM attacks. HMAC-SHA1 and CBC mode are not the best, but still
very difficult to mount an attack. Can be in whitelist.

**2.3.** (2 points)
TLS\_DHE\_RSA\_WITH\_AES\_128\_GCM\_SHA256

**Solution**:
Key exchange: Ephemeral Diffie-Hellman  
Encryption: 128-bit AES GCM mode  
MAC: 256-bit SHA-2  
Ephemeral Diffie-Hellman provides perfect forward secrecy. You can
safely use this suite.

**2.4.** (2 points)
TLS\_DH\_anon\_WITH\_DES\_CBC\_SHA

**Solution**:
Key exchange: Diffie-Hellman  
Authentication: None  
Encryption: DES  
MAC: 160-bit SHA-1  
Anonymous DH does not authenticate senders and is therefore vulnerable
to MitM attacks. DES is not secure, SHA-1 is not secure. Definitely not
in the whitelist.

**2.5.** (2 points)
TLS\_RSA\_WITH\_RC4\_128\_MD5

**Solution**:
Key exchange: RSA  
Encryption: 128-bit RC4  
MAC: MD5  
RSA does not provide perfect forward secrecy. RC4 is not secure, MD5 is
broken. Terrible cipher suite.

### Question 3 
Recent version of TLS fixed a number of vulnerabilities and design
problems — TLS1.3, for instance, discontinues EXPORT-strength ciphers
and non-PFS cipher suites.

**3.1.** (2 points)
An attacker can drop packet on the network. Can they downgrade the
negotiated TLS version?

**Solution**:
No. ClientFinished and ServerFinished messages contain hases for full
transcript of the TLS handshake — missing messages would make the
transcript verification fail.

**3.2.** (2 points)
An attacker can modify packet on the network. Can they downgrade the
negotiated TLS version?

**Solution**:
Still no. The transcript are authenticated, and they would show if the
connection had been tampered with.

**3.3.** (3 points)
Some (buggy) legacy web servers used to close the connection when
receiving an unknown TLS version field. As a workaround, many clients
started implementing a fallback mechanism: if connection was closed by
the server, they would attempt a new TLS handshake, but with a lower TLS
version number. What are the implications of this behaviour on downgrade
attacks?

**Solution**:
A downgrade attack is now possible: the attacker drops packets when the
client attempts to use higher TLS version, the client will fall back to
a previous version.

**3.4.** (2 points)
What is the fundamental reason why this an attack is possible? (Think
about the content of the transcript when a fallback is executed)

**Solution**:
The client transcript after a fallback will be incomplete — it will not
include the previous handshake messages.

**3.5.** (3 points)
What attack would be possible if we removed the transcript from the
ClientFinished message, while retaining it in the ServerFinished?

**Solution**:
No attack is possible if the client and the server correctly implement
the TLS protocol to specification. The server would send ServerFinished, the
client would compare it with its version, realize that the session has been
tampered with and abort. The symmetry was introduced for the protocol to be
future proof: this counteracts any bugs in TLS implementations and any possible
more esoteric attacks.

**3.6.** (2 points)
We want to prevent downgrade attacks on modern servers, while still
maintaining compatibility with legacy servers that exhibit aberrant
behaviour. What is a possible solution?

**Solution**:
A version number should be somehow inserted in the transcript.
TLS\_FALLBACK\_SCSV (RFC7507) is a workaround that does exactly this.

**3.7.** (2 points, bonus)
Why should a downgrade down to SSLv3 be prevented at all costs?

**Solution**:
POODLE (2014), but the core idea of the attack was already known in 2004
(Moeller) and mentioned as a serious threat for SSLv3 already in the
Lucky 13 paper from 2013. Using SSLv3 with RC4 as a cipher is still
problematic, due to statistical analysis attacks on the cipher.

### Question 4 (4 points)
You discover that your TLS library uses an insecure pseudo-random number
generator for nonces in the handshake. This PRNG has a very short period
— its output is repeated every 10<sup>5</sup> accesses. Propose a
possible attack, assuming a Dolev-Yao adversary.

**Solution**:
If the TLS (1.3) handshake includes a Diffie-Hellman Ephemeral key exchange,
attacks are unlikely: the Hello messages still contain an unpredictable
source of randomness.

In the case of a PSK-only handshake (no DHE) or if the servers reuses
Diffie-Hellman key shares (e.g. to save computational cycles) replay attacks
become possible: if the attacker can predict when a nonce in the server repeats
itself, a transcript of a previously recorded connection can be replayed — all
the other parameters in the TLS key establishment will stay the same.
