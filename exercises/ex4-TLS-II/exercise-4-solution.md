# Exercise sheet 4: Transport Layer Security II

*6 October 2020*

Handing in this exercise sheet is optional.
If you want individual feedback for your solutions, you have to hand in your solution by the **Wednesday following exercise publication, October 14, at 23:59**.
The hand-in procedure is as follows:

- copy this document, and answer the questions in the appropriate spaces;
- create a new issue on [the GitLab issue repo](https://gitlab.inf.ethz.ch/PRV-PERRIG/netsec-course/netsec-2020-issues);
- the issue title must be in the form `[exercise-hand-in] Exercise 4 {YOUR NETHZ ID}` (without curly braces);
- you should set the issue as confidential;
- paste the modified document with your solution in the body of the issue.

### Question 1

Last week's exercise sheet contained exercises on TLS handshakes, even if
the lecture focused mainly on the record protocol. To compensate, here
are some questions on the record protocol!

**1.1.** (2 points)
As we have learnt in the last lecture, the TLS 1.3 record protocol offers an
optional padding feature to hide the true length of fragments. Can you think of
a possible way to recover the true length of the fragments? Think of the way
you would write a function to remove the padding. (Note: it’s not a padding
oracle)

**Solution**:
It is still possible to guess the length of the padding using a
side-channel attack: it would be hard to make a padding-removal function
perfectly constant-time, and even then the output of the function would
probably be fed into data-dependant functions. Therefore, it can be possible
to observe how long a given TLS implementation takes to remove the padding
from a record (e.g. by measuring the covariance of the response time to
requests for which the amount of padding is known), and use those measurements
to infer the unpadded length of a record.

**1.2.** (4 points)
In the TLS 1.3 record protocol, the per-record nonce is based upon a
64-bit counter.

- (1 points)
The counter always starts at 0. Why is this not a problem?

**Solution**:
First, because it is XORed with a per-session IV before being used in
the AEAD cipher. Second, it only needs to be a nonce: a number used
once, and it does not really matter if it is predictable as long as it
is not reused with the same keys. As keys are newly generated at the beginning
of each session, this condition is fulfilled.

- (1 points)
Given an average record length of 100 bytes and a modern connection
speed of 100Mb/s, and ignoring the fact that any sane TLS implementation
would terminate the connection earlier, how many years would it take to
reset the counter back to 0?

**Solution**:
Possible nonce values: 2<sup>64</sup>

Average record length: 8⋅100 bits

=> Amount of data to be transmitted before nonces repeat:  2<sup>64</sup> ⋅ 8⋅100 bits


Data transmission rate: 100⋅10<sup>6</sup> bits/s

Seconds in a year: 3600⋅24⋅365 s/year

=> Transmission rate per year: 100⋅10<sup>6</sup> ⋅ 3600⋅24⋅365 bits/year

Years until nonce repeats: (2<sup>64</sup> ⋅ 8⋅100 bits) / (100⋅10<sup>6</sup> ⋅ 3600⋅24⋅365 bits/year) = 4, 679, 539 years

- (2 points)
What would happen if some records were to be reordered?

**Solution**:
As authentication and decryption uses the locally stored counter
as a nonce, they would fail and the connection would be torn down. An
incorrect nonce would result in an incorrect plaintext, but due to the
authentication failure, it would be discarded.

### Question 2

One famous OpenSSL attack, Heartbleed [1], exploits some server
implementations of keep-alive (heartbeat) messages. These messages can
be sent even before the handshake is complete. They are meant to check
if the other side is still online and verify that data transfer works
correctly. One side sends a buffer and requests a certain amount of
characters from that buffer. Unfortunately, the vulnerable servers had a
missing bounds check...

[1] <https://heartbleed.com/>

**2.1.** (2 points)
Let’s assume that your website uses login cookies (e.g., string
LOGIN\_COOKIE=/16 byte value/) and stores recent request data in nearby
memory locations. How would you use Heartbleed to hijack a session of
someone who logged in recently?

**Solution**:
<img src="images/heartbleed_explanation.png" alt="image" />

<https://xkcd.com/1354/>

**2.2.** (1 points)
Does client authentication (e.g., using client certificates) offer
protection against Heartbleed?

**Solution**:
No, it doesn’t help because heartbeat messages can be sent and be replied
to during the handshake, i.e. before the client is authenticated.

**2.3.** (2 points)
Let’s assume that you always used a cipher suite with Ephemeral
Diffie-Hellman key agreement and a known secure encryption and hashing
algorithm and you are unfortunate enough that your private key gets
leaked using Heartbleed. Your adversary has recorded your previous
communications to the same web service. Can he decrypt those messages?
What if you had not used a cipher suite with PFS?

**Solution**:
Because we used a cipher suite with PFS, the adversary won’t be able to
decrypt previous messages. He has the private key, but those messages
are encrypted using a temporary key, which no one has anymore.
If we hadn’t had used a cipher suite with PFS, the adversary would have
been able to decrypt all the messages that we have sent.

### Question 3

The Heartbleed attack we have just seen belongs to the category of
implementation bug exploits. The TLS protocol has had, during the years,
several of these attacks, which have been solved by simply patching
software libraries and servers.

**3.1.** (2 points)
In 2014, Apple discovered an implementation error in SecureTransport,
the TLS library used in iPhones and Macs. What is the problem with this
C code?

    static OSStatus SSLVerifySignedServerKeyExchange (SSLContext *ctx, bool isRsa,
            SSLBuffer signedParams, uint8_t *signature, UInt16 signatureLen) {
        OSStatus err;
        ...
        if ((err = SSLHashSHA1.update(&hashCtx, &serverRandom)) != 0)
                goto fail;
        if ((err = SSLHashSHA1.update(&hashCtx, &signedParams)) != 0)
                goto fail;
                goto fail;
        if ((err = SSLHashSHA1.final(&hashCtx, &hashOut)) != 0)
                goto fail;
        ...
        err = sslRawVerify(...);
    fail:
            SSLFreeBuffer(&signedHashes);
            SSLFreeBuffer(&hashCtx);
            return err;
    }

**Solution**:
After the second if, there is a second repetition of the line goto fail,
probably caused by a copy-paste error. Said line is not tied to any if
structure, so it will be executed every time. If all the checks prior to
this succeeded, The program will always return an error of 0 (no error)
and skip the actual TLS verification below.

**3.2.** (3 points)
What kind of attack can take advantage of this bug? You can find a hint
in the method signature.

**Solution**:
The affected method signature tells us that it is responsible for
verifying the signature of the server key exchange message. Now, since
this always returns true, an attacker could MitM and use the certificate
of a legitimate website signed with a mismatched private key. After
that, the MitM will sign the ServerKeyExchange message with any key, or
not sign it at all. This breaks the authentication of the ephemeral key
in DHE and ECDHE cipher suites, and thus the authentication of all the
messages encrypted using that key.

The result is of course the ability to serve a fake website instead of
the original one.

**3.3.** (2 points)
Apple is not the only organization with this kind of problems. In the
same year, the TLS library GnuTLS had a similar code bug. Can you spot
it? What are the security implications of this bug?

Hints: The function checks if a certificate is a CA cert and returns
true if so.
\_gnutls\_x509\_get\_signed\_data returns values &lt;0 if the cert is
verified as invalid.

    static int check_if_ca (gnutls_x509_crt_t cert, gnutls_x509_crt_t issuer,
                                                  unsigned int flags) {
        int result;
        result = _gnutls_x509_get_signed_data (issuer->cert, "tbsCertificate",
                                            &issuer_signed_data);
        if (result < 0) {
            gnutls_assert ();
            goto cleanup;
        }
        result =  _gnutls_x509_get_signed_data (cert->cert, "tbsCertificate",
                                          &cert_signed_data);
        if (result < 0) {
            gnutls_assert ();
            goto cleanup;
        }
        ...
        result = 0;
    cleanup:
        // do the cleanup
        return result;
    }

**Solution**:
If one of the function calls returns negative (=invalid cert), the
execution goes directly to cleanup and the function returns that value.
In C, any nonzero value is interpreted as true, so any invalidly signed
certificate would be classified as a valid CA certificate.

**3.4.** (3 points)
What kinds of attack can the malicious user achieve?

**Solution**:
Any invalid certificate is considered a valid CA certificate by the
browser. This means that the cert is wrongfully inserted into the trust
chain. In turn, any certificate signed with the invalid one will be
considered valid and legitimate ...

### Question 4

One of the design goals of TLS 1.3 was to reduce the overhead generated
by the handshake: a 0-RTT option was introduced, allowing clients who
recently connected to a server to send ’early’ application data in the
first flight of protocol messages. The improved performance comes at a
price: the 0-RTT data does not enjoy forward security. Furthermore, the
mechanism used for generating resumption tickets is not specified in
rfc8446, making some implementation more resilient to attacks than
others. This question spotlights some of the possible security
implications of 0-RTT early data under different resumptions mechanisms.

**4.1.** (2 points)
A trivial way for a server to deal with resumption tickets is to keep a
database of all the issued tickets information and the relative PSK.
Once a client uses the PSK in a resumption handshake, it is removed from
the database. Is this server vulnerable to 0-RTT data replay?

**Solution**:
No: the replay attempt will result in the server rejecting the handshake
because of an unknown PSK.

**4.2.** (2 points)
The administrators of the server from the previous question ran out of
disk space, and want to get rid of the huge resumption database they are
keeping. They devise a new strategy: the server derives a secret key.
Every time a resumption ticket is issued, the session information and
the resumption PSK are encrypted under the secret key and set as the
label in ticket. During a resumption handshake, the client will provide
the server with the label, and the secret can now decrypt all the
information it needs to resume the session. Is the server now vulnerable
to 0-RTT data replay?

**Solution**:
Yes: the server now is not keeping track of which PSKs have already been
used for resumption handshakes, and which one are fresh. If attacker
replays a handshake with 0-RTT data, this early data will be accepted
and acted upon by the application using TLS (even if the handshake will
eventually fail, cfr last part of this question)

**4.3.** (2 points)
Alice is regularly paying her bills using the eBanking services. To
improve the user experience, her bank has recently updated its web
servers to support TLS 1.3 and 0-RTT data on resumption, but it turns
out that the bank’s sysadmins are the guys from the last question.
Alice sends a request containing an order to transfer some money to
Mallory. Mallory is able to record connection between Alice’s computer
and the server of the eBanking service. What can Mallory do with the
recorded packets? What could the bank do to prevent this?

**Solution**:
Mallory can launch a replay attack. Because the bank allows 0-RTT data
for all requests, Mallory can resend the packets containing Alice’s
request to transfer the money. In the absence of anti-replay mechanisms,
she can trigger the same transaction repeatedly.
To avoid this, the bank should only allow 0-RTT data for idempotent
requests, i.e. requests that do not change the state on the server, or
implement some other anti-replay defense at TLS layer (single-use
tickets, client hello recording), or, even better, at application layer
(single-use tokens for requests—any serious bank is probably using
those already).

**4.4.** (5 points)
Your favorite cloud provider announces its web hosting will switch to
TLS 1.3 servers with session resumption and 0-RTT data enabled by
default. The provider offers redundancy over two servers, Server A and
Server B, with an embedded DNS load balancer: clients will either
resolve your domain name to the IP address of Server A or the one of
Server B. A and B are identical, but the resumption PSK is not portable
between the servers (i.e. a connection to server A will result in a
resumption PSK that cannot be used in a resumption handshake with server
B). Knowing that 0-RTT data is replayable, the provider set up the
servers to immediately invalidate a resumption PSK immediately after it
is used for a resumption handshake. How can the usage of TLS 1.3 0-RTT
data still lead to a replay attack in this setup? Assume an attacker has
full control over the network, and that clients automatically try to
reconnect with a full handshake if the PSK handshake is rejected.

*Hint: consider what happens on connection failure.*

**Solution**:
This attack exploits the natural client behavior of retrying after
failures. The following scenario is possible:

1. The client had connected to server A (regular handshake) in a
    previous moment.

2. The client issues another request to server A. A resumption
   handshake is used, and the request is transmitted as 0-RTT data.

3. The attacker duplicates the request and sends a copy to server B.

4. Server A fulfils the request. Server B does not accept the
   resumption handshake and will decline the request.

5. The attacker blocks the response from A and lets the one from B
   through, properly patching up the TCP headers to make the latter
   look like it came from A.

6. The client receives a failure alert rejecting the resumption PSK,
   and will retry—this time, with a full handshake. The new request
   will be a duplicate of the one originally sent to server A. The
   attacker can direct this request to Server B.

7. Server B fulfils the new request. In absence of additional
    (distributed) anti-replay mechanisms, the request was now handled by
    both server A and B, resulting in a successful replay attack.

**4.5.** (3 points)
We know that TLS 1.3 resumption handshake offers an optional (EC)DHE key
exchange, for the sake of forward security. Are 0-RTT data forward
secret in a resumption (EC)DHE exchange? Justify your answer.

**Solution**:
No: 0-RTT data are encrypted and sent over the first flight of messages—the
Diffie-Hellman key exchange will only come into play later,
guaranteeing forward secrecy for the normal application traffic keys.
The key used for encrypting early data, the early application traffic
key, is non-forward secure even in a (EC)DHE resumption handshake.

Note that here we are defining forward secrecy with respect to the
keys the handshake itself is instantiated with: for a full handshake, this 
would be long-term keypairs, while for the PSK handshakes it is the PSKs.
0-RTT data in a resumption handshake will therefore be non-forward secret 
(w.r.t. the resumption PSK) independently of the particular implemented
resumption mechanism.

**4.6.** (3 points)
Can a TLS 1.3 resumption handshake without (EC)DHE key exchange be
replayed in its entirety? Justify your answer.

**Solution**:
No. Even if the handshake is not forward secret, this does mean it is
not protected against replays: specifically, for the handshake to be
completed, the client and the server must agree on the content of the
respective ClientHello and ServerHello message. Those Hello messages
contain a random nonce: an attacker trying to replay a client (resp. a
server) session will fail as soon as the server (resp. the client)
verifies the Finished message, which contains a MAC over the session
transcript. (Or even earlier, if you consider that the handshake
encryption keys computed by the client and the server will not match).

### Question 5 (bonus)

In the paper ["On the Security of RC4 in TLS"](https://www.usenix.org/conference/usenixsecurity13/technical-sessions/paper/alFardan)
(AlFardan, Bernstein, Paterson, Poettering, and Schuldt, USENIX ’13), the
authors present an attack on the usage of the RC4 cipher in TLS version 1.2 and
previous (note that RC4 has been since prohibited in rfc7465 for those TLS
versions).  The attack allows them to fully recover the TLS-protected
plaintext, given enough repeated encryptions of the same data. You want to
implement this attack, targeting a secure cookie in the HTTPS session between a
client running a web browser *B* and a web server *W*.

**5.1.** (4 points)
How would you force the browser to transmit the same data
multiple times? You don’t have direct control of the browser nor of the
web server, but you know *W* serves secure cookies over HTTPS, and you
control a second web server, *E*, the client visits.

**Solution**:
The client will run javascript code served from *E*. While this code
cannot directly access cookies from *W* (due to the same-origin policy,
*SOP*), it can make requests to *W*: the cookies will be automatically
attached by the browser to the new requests. The javascript can repeat
enough request to make the attack succeed—allowing the attacker to
recover the plaintext cookie.

If you are confused by SOP and CORS, and are unsure of which connections
would be allowed, check out
<https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS>.

**5.2.** (2 points)
Single-byte biases in the RC4 cipher are stronger at the start of the
keystream. Propose a possible mitigation for the attack.

**Solution**:
It is possible discard the initial keystream of RC4—note that this
will not stop the double-byte bias attack.
