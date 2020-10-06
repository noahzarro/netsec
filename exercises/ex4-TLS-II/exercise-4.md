# Exercise sheet 4: Transport Layer Security II

*6 October 2020*

Handing in this exercise sheet is optional.
If you want individual feedback for your solutions, you have to hand in your solution by the **Wednesday following exercise publication, October 16, at 23:59**. 
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

*Solution*: Your solution here.

**1.2.** (4 points)
In the TLS 1.3 record protocol, the per-record nonce is based upon a
64-bit counter.

- (1 points)
The counter always starts at 0. Why is this not a problem?

*Solution*: Your solution here.

- (1 points)
Given an average record length of 100 bytes and a modern connection
speed of 100Mb/s, and ignoring the fact that any sane TLS implementation
would terminate the connection earlier, how many years would it take to
reset the counter back to 0?

*Solution*: Your solution here.

- (2 points)
What would happen if some records were to be reordered?

*Solution*: Your solution here.

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

*Solution*: Your solution here.

**2.2.** (1 points)
Does client authentication (e.g., using client certificates) offer
protection against Heartbleed?

*Solution*: Your solution here.

**2.3.** (2 points)
Let’s assume that you always used a cipher suite with Ephemeral
Diffie-Hellman key agreement and a known secure encryption and hashing
algorithm and you are unfortunate enough that your private key gets
leaked using Heartbleed. Your adversary has recorded your previous
communications to the same web service. Can he decrypt those messages?
What if you had not used a cipher suite with PFS?

*Solution*: Your solution here.

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

*Solution*: Your solution here.

**3.2.** (3 points)
What kind of attack can take advantage of this bug? You can find a hint
in the method signature.

*Solution*: Your solution here.

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

*Solution*: Your solution here.

**3.4.** (3 points)
What kinds of attack can the malicious user achieve?

*Solution*: Your solution here.

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

*Solution*: Your solution here.

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

*Solution*: Your solution here.

**4.3.** (2 points)
Alice is regularly paying her bills using the eBanking services. To
improve the user experience, her bank has recently updated its web
servers to support TLS 1.3 and 0-RTT data on resumption, but it turns
out that the bank’s sysadmins are the guys from the last question.
Alice sends a request containing an order to transfer some money to
Mallory. Mallory is able to record connection between Alice’s computer
and the server of the eBanking service. What can Mallory do with the
recorded packets? What could the bank do to prevent this?

*Solution*: Your solution here.

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

*Solution*: Your solution here.

**4.5.** (3 points)
We know that TLS 1.3 resumption handshake offers an optional (EC)DHE key
exchange, for the sake of forward security. Are 0-RTT data forward
secret in a resumption (EC)DHE exchange? Justify your answer.

*Solution*: Your solution here.

**4.6.** (3 points)
Can a TLS 1.3 resumption handshake without (EC)DHE key exchange be
replayed in its entirety? Justify your answer.

*Solution*: Your solution here.


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

*Solution*: Your solution here.

**5.2.** (2 points)
Single-byte biases in the RC4 cipher are stronger at the start of the
keystream. Propose a possible mitigation for the attack.

*Solution*: Your solution here.
