# Exercise sheet 1: Crypto and Networks refresher

*15 September 2020*

Handing in this exercise sheet is optional.
If you want individual feedback for your solutions, you have to hand in your solution by the **Wednesday following exercise publication, September 23, at 23:59**. 
The hand-in procedure is as follows:

- copy this document, and answer the questions in the appropriate spaces;
- create a new issue on [the GitLab issue repo](https://gitlab.inf.ethz.ch/PRV-PERRIG/netsec-course/netsec-2020-issues);
- the issue title must be in the form `[hand-in] Exercise 1 {YOUR NETHZ ID}`; 
- paste the modified document with your solution in the body of the issue.

The issue will be then automatically tagged and made private to protect your privacy.

## Crypto refresher

### Question 1 
Concisely answer the following questions:

**1.1.** (2 points)
Edward wants to prove to Laura he really is the sender of a message. What security property is he trying to achieve?  Which cryptographic primitive could he use?

*Solution*: Your solution here... 

**1.2.** (2 points)
Edward wants to send a secret message to Glenn---they cannot meet to exchange a key, but they deem it unlikely for the NSA to tamper with their messages on the fly~\footnote{Not historically accurate}. What security property are Edward and Glenn trying to achieve? Which cryptographic primitives could they use?

*Solution*:    Your solution here... 

**1.3.** (2 points)
Edward wants to store records of his job assignments at NSA on his NAS. Which property ensures that nobody will alter the record before it reaches the NAS? Can you name a network protocol which provides this guarantee? (assume a fair use of the protocol)

*Solution*: Your solution here... 

**1.4.** (2 points)
Chelsea wants to share some documents with a journalist, without risking to be identified.  What security property is she trying to achieve?  Can you name a technology that could, in principle, protect her?

*Solution*: Your solution here... 

### Question 2 (2 points)
Given a Merkle Hash Tree with n leaves, how many nodes need to be recomputed if a leaf is updated? (Assume a binary balanced fully populated tree).

*Solution*: Your solution here... 

### Question 3 (4 points)
You are using AES in ECB mode to encrypt an uncompressed high-resolution image. What could go wrong?

*Solution*: Your solution here... 

### Question 4 
You are writing a network protocol to control your server remotely---you decide to simply use AES in CBC mode to encrypt communication.

**4.1.** (3 points)
What could go wrong?

*Solution*: Your solution here... 

**4.2 Bonus question.** (3 points)
You learn from your past mistakes and decide to appropriately MAC all commands before encrypting them. Commands are padded to exactly fit in a block using standard PKCS7 padding. The server will report and error if the MAC verification fails, and a different error if padding verification fails.

*Solution*: Your solution here... 

## Network refresher

### Question 5 
Concisely answer the following questions:

**5.1.** (2 points)
Explain what bandwidth allocation is, and what requirements we pose on a bandwidth allocation algorithm.

*Solution*: Your solution here...

**5.2.** (1 points)
How does UDP deal with bandwidth allocation?

*Solution*: Your solution here...

**5.3.** (1 points)
How does TCP deal with bandwidth allocation?

*Solution*: Your solution here...

**5.4.** (2 points)
The simpler nature of UDP is not necessarily a disadvantage. When is UDP preferred over TCP?

*Solution*: Your solution here...

**5.5.** (3 points)
TCP is more complex than UDP. This complexity results in overheads, both in time and storage space. Let's focus on storage: the server has to store the state of each connection in its memory. Do you see a potential problem with this?

*Solution*: Your solution here... 

### Question 6 (3 points)
Explain why is congestion detrimental to networks.

*Solution*: Your solution here... 

### Question 7 
Routing algorithms are at the heart of routers. Open Shortest Path First (OSPF), is a link-state protocol used in many real world scenarios. In OSPF, every link has a specific cost and the goal is to find the best possible route. Here is a simple explanation of how it works:
<https://www.auvik.com/franklymsp/blog/ospf-protocol-explained/>.

**7.1.** (5 points)
Consider the following network. The square nodes are local-area networks (LANs), while the round nodes are routers. LANs can only receive and generate packets, they can't route incoming traffic. Fill in the routing tables below.

![OSPF network](https://gitlab.inf.ethz.ch/PRV-PERRIG/netsec-course/netsec-2020-resources/-/raw/master/exercises/assets/ospf-network.png)

*Solution*:

R1:

| target | next hop |
| :----: | :------: |
|   N1   |   ...    |
|   N2   |   ...    |
|   N3   |   ...    |

R2:

| target | next hop |
| :----: | :------: |
|   N1   |   ...    |
|   N2   |   ...    |
|   N3   |   ...    |

R3:

| target | next hop |
| :----: | :------: |
|   N1   |   ...    |
|   N2   |   ...    |
|   N3   |   ...    |

  
  
R4:

| target | next hop |
| :----: | :------: |
|   N1   |   ...    |
|   N2   |   ...    |
|   N3   |   ...    |

R5:

| target | next hop |
| :----: | :------: |
|   N1   |   ...    |
|   N2   |   ...    |
|   N3   |   ...    |

  
  
N1:

| target | next hop |
| :----: | :------: |
|   N2   |   ...    |
|   N3   |   ...    |

N2:

| target | next hop |
| :----: | :------: |
|   N1   |   ...    |
|   N3   |   ...    |



**7.2.** (3 points)
You want to isolate the flows N1 $`\rightarrow`$ N3 and N1 $`\rightarrow`$ N2. Does this configuration satisfy this property? If not, how would you change the costs so that it does?

*Solution*: Your solution here...

**7.3.** (2 points)
An external authority forces you to add a static route (with maximum priority) that sends any packet that reaches R5 to the link R5 $`\rightarrow`$ R4. Ignoring the longer routes and the unused links, what problems can arise?

*Solution*: Your solution here...
