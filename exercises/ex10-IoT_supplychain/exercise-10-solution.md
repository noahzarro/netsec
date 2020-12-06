# Exercise sheet 10: IoT, Supply Chain attacks

*22 November 2020*

Handing in this exercise sheet is optional.
If you want individual feedback for your solutions, you have to hand in your solution by the **Saturday following exercise publication, November 28, at 23:59**.
The hand-in procedure is as follows:

- copy this document, and answer the questions in the appropriate spaces;
- create a new issue on [the GitLab issue repo](https://gitlab.inf.ethz.ch/PRV-PERRIG/netsec-course/netsec-2020-issues);
- the issue title must be in the form `[exercise-hand-in] Exercise 10 {YOUR NETHZ ID}` (without curly braces); 
- you should set the issue as confidential;
- paste the modified document with your solution in the body of the issue. 


### Question 1 
Shodan[1] is a web service that monitors devices connected to the
Internet. It scans IP addresses and tries to get information about their
location, their users and their possible security vulnerabilities. It’s
like a search engine for devices on the Internet. For instance,
<https://www.shodan.io/report/0Wew7Zq7> contains a report for the
well-known Heartbleed vulnerability in TLS.

[1] <https://www.shodan.io/>

**1.1.** (2 points)
Shodan is advertised as a useful tool to monitor your devices and ensure
they are not accessible from unauthorized people/entities. Also, you can
for example collect usage data for your products. Next to these positive
usages, can you think of any negative aspects this platform inherently
brings?

**Solution**:
Although Shodan does not do anything illegal, it might expose
information that people wish to keep hidden. Also, it is a very useful
database for attackers, say for example in case of new zero-day
exploits.  
However, it must be said that Shodan does not expose any new
information: an experienced attacker would probably just use his usual
tools (NMAP etc) to scan the network, regardless of the existence of
Shodan. Also, to unlock all the potential of the engine, you need to
make an account, which is not ideal for any attacker.

**1.2.** (1 points)
Refresher about NATs and firewalls: is a camera mapped by Shodan if it’s
behind a common home router? Does it make the situation better?

**Solution**:
Devices are visible if they are forwarded with port forwarding, or there
is a remote management protocol that bypasses the NAT barriers. It
doesn’t make the situation better as far as privacy is concerned, but
the potential attack surface is surely reduced.

**1.3.** 
In January 2012, a blog post[1] brought many enthusiastic customers of
IP cameras back to reality. The blogger unpacked the firmware of some
cameras by US-based company Trendnet, only to discover a way that
allowed him to view the video streams.

[1] <http://console-cowboys.blogspot.com/2012/01/trendnet-cameras-i-always-feel-like.html>

- (3 points) What was the problem with the cameras? Does changing the default
password help?

**Solution**:
The root directory of the camera’s server had, next to the management
directory, another script called `mjpg.cgi`.  
This script, accessible at `https://IP_ADDR/anony/mjpg.cgi`, streamed
the captured video in real-time without the need of any authentication.
Of course, changing passwords did not help (but changing them is still
always a good idea).

- (2 points) In this context, what was Shodan misused for?

**Solution**:
The capabilities of Shodan, together with the fact that the blogpost
mentioned the engine, brought some curious readers to peak into the
cameras of hundreds of clueless user. These peekers were able to see the
private life of many families: feeding a cat, leaving and returning
home, taking care of their infant child, etc. And not to mention,
cameras can sometimes videotape less innocent things.

- (3 points) The company released updates to the firmware in order to fix the problem
and the spokesperson invited customers to update in an interview. Is
this enough? What is the weakest link in this fixing strategy?

**Solution**:
The weakest link is undoubtedly the user. In the case of IP cameras, the
user is not necessarily computer-literate, and will inevitably forget to
update, or not know that a vulnerability needs to be fixed. This is not
enough, as many devices will never get updates and remain vulnerable.  
On the other hand, would an automatic update system be the best solution
here? Updates patch vulnerabilities, but they can introduce new
problems. Consider a nuclear power plant control software: do you really
want automatic updates?.  
The best solution would be to nudge the user to update: automatically
send a notification to the phone via the IPCam app, showing the
changelog and a confirmation or refuse button. Upon confirmation, the
update is installed automatically, with no further user action required.

- (4 points) How could this problem have been avoided altogether? How can we make IoT
devices better in general? Discuss some advantages and disadvantages of
your proposed solution.

**Solution**:
The easiest solution would be to enforce some minimal security standards
and testing for IoT devices. This will ensure that devices so close to
us, like cameras, will not deliberately leak video feeds of our everyday
life. On the other hand, such a change will undoubtedly face a strenuous
resistance from the industry.

-  (2 points) "We are scrambling to discover how the code was introduced and at
this point it seems like a coding oversight" said Zak Wood, Trendnet’s
director of marketing. Do you believe this is the case?

**Solution**:
This is my personal opinion, but this seems too simple to be a coding
mistake. Leaving a critical CGI script available without authentication
is a rookie mistake. On the other hand, it is entirely possible that
this was left from testing.

### Question 2 
In 2018, a Bloomberg Businessweek article reported a Chinese
infiltration in Supermicro’s servers[1]. Supermicro’s server
motherboards in use in many big US tech companies (notably, Apple and
Amazon among them) allegedly contained an hardware implant, designed by
Chinese military, that allowed for remote infiltration. The report has
since been disputed, with Apple and Amazon publicly declaring that no
such attack was known to them. Bloomberg has never provided definitive
evidence, and some even suspect that the reporters were mislead by a US
covert operation aimed to fuel Trump’s anti-Chinese narrative. We have
no way to know what really happened, but the attack is technically
plausible[2].

<figure>
<img src="assets/bloomberg.jpg" id="fig:bloomberg" style="width:30.0%" alt="The Great Hack (that maybe was not)." /><figcaption aria-hidden="true"><strong>The Great Hack</strong> (that maybe was not).</figcaption>
</figure>

[1] <https://www.bloomberg.com/news/features/2018-10-04/the-big-hack-how-china-used-a-tiny-chip-to-infiltrate-america-s-top-companies>

[2] <https://www.lightbluetouchpaper.org/2018/10/05/making-sense-of-the-supermicro-motherboard-attack/>

**2.1.** (2 points)
The implant was allegedly added to Supermicro boards in Chinese
factories, unknowingly to the company (and to its downstream customers).
How is this class of attacks typically called?

**Solution**:
Supply chain attacks.

**2.2.** (4 points)
What are the theoretical capabilities of the implant described in
Bloomberg’s article? How would the implant interact with the target
system?

**Solution**:
In the Supermicro case, it was connected to the BMC, Baseboard
Management Controller, which sits at a lower level respect to the OS,
and runs a separate firmware (on a separate cpu) in order to allow
remote administration operations, like reinstalling the OS or restarting
an unresponsive machine. The BMC reads its firmware from an SPI flash –
the implant, if connected to the SPI wire, could replace the entire
firmware or otherwise tamper with it.

The implant would then have all the capabilities the BMC has – which is
to say, full access to the entire machine. It could compromise the
operating system to run malicius code, or simply exfiltrate information.

**2.3.** (2 points)
How would such an implant network traffic evade detection from the
operating system running on the server?

**Solution**:
The implant can be directly connected to the Ethernet controller,
therefore it would be completely transparent to the the operating
system.

In the Supermicro case, the BMC has direct access to the network
controller.

**2.4.** (2 points)
We don’t know whether China really carried out this attack, but the
Snowden revelations gave us proof that US was running a similar program
between 2007 and 2012. Can you name the intelligence agency which was
involved? Link the original documents in support of your claims[1].

[1] Bonus points for unpublished material.

**Solution**:
NSA – more precisely, the Persistence Division of NSA’s Tailored Access
Operatons unit.

Internal wiki page about supply chain attacks (Inetllipedia):
<https://theintercept.com/document/2019/01/24/intellipedia-supply-chain-cyber-threats/>

Intercepting network hardware:
<https://web.archive.org/web/20190709195103/https://www.spiegel.de/media/media-35669.pdf>

TAO presentation slides:
<https://theintercept.com/document/2019/01/24/tailored-access-operations-2007/>

**2.5.** (2 points)
Board management units are really common in servers, where physically
accessing thousands of machines for changing an operating system or
rebooting would be impractical. Naturally, the presence of such hardware
features increases the attack surface of the system. Is your laptop
equipped with similar hardware?

**Solution**:
If your laptop is less than 10 years old, your chipset almost certainly
includes either Intel Management Engine or AMD Secure technology. Those
autonomous susbystems continuously run as long as the motherboard is
powered (including when your pc is turned off,
<https://www.blackhat.com/eu-17/briefings/schedule/#how-to-hack-a-turned-off-computer-or-running-unsigned-code-in-intel-management-engine-8668>,
and independently of your operating system) and allow remote operation
and administration of the machine when they are provisioned.
