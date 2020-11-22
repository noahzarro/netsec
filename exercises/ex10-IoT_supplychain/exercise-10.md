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

*Solution:* Your solution here

**1.2.** (1 points)
Refresher about NATs and firewalls: is a camera mapped by Shodan if it’s
behind a common home router? Does it make the situation better?

*Solution:* Your solution here

**1.3.** 
In January 2012, a blog post[1] brought many enthusiastic customers of
IP cameras back to reality. The blogger unpacked the firmware of some
cameras by US-based company Trendnet, only to discover a way that
allowed him to view the video streams.

[1] <http://console-cowboys.blogspot.com/2012/01/trendnet-cameras-i-always-feel-like.html>

- (3 points) What was the problem with the cameras? Does changing the default
password help?

*Solution:* Your solution here

- (2 points) In this context, what was Shodan misused for?

*Solution:* Your solution here

- (3 points) The company released updates to the firmware in order to fix the problem
and the spokesperson invited customers to update in an interview. Is
this enough? What is the weakest link in this fixing strategy?

*Solution:* Your solution here

- (4 points) How could this problem have been avoided altogether? How can we make IoT
devices better in general? Discuss some advantages and disadvantages of
your proposed solution.

*Solution:* Your solution here

-  \[2\] "We are scrambling to discover how the code was introduced and at
this point it seems like a coding oversight" said Zak Wood, Trendnet’s
director of marketing. Do you believe this is the case?

*Solution:* Your solution here

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

*Solution:* Your solution here

**2.2.** (4 points)
What are the theoretical capabilities of the implant described in
Bloomberg’s article? How would the implant interact with the target
system?

*Solution:* Your solution here

**2.3.** (2 points)
How would such an implant network traffic evade detection from the
operating system running on the server?

*Solution:* Your solution here

**2.4.** (2 points)
We don’t know whether China really carried out this attack, but the
Snowden revelations gave us proof that US was running a similar program
between 2007 and 2012. Can you name the intelligence agency which was
involved? Link the original documents in support of your claims[1].

[1] Bonus points for unpublished material.

*Solution:* Your solution here

**2.5.** (2 points)
Board management units are really common in servers, where physically
accessing thousands of machines for changing an operating system or
rebooting would be impractical. Naturally, the presence of such hardware
features increases the attack surface of the system. Is your laptop
equipped with similar hardware?

*Solution:* Your solution here
