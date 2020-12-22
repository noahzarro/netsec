# Exercise sheet 14: Probabilistic Traffic Monitoring

*21 December 2020*

You are **not** expected to hand in this exercise sheets.
Solutions are provided, but do try to solve the exercises on your own
before looking at them!

Please open an issue on [the GitLab issue repo](https://gitlab.inf.ethz.ch/PRV-PERRIG/netsec-course/netsec-2020-issues) if you need clarifications.
(As most of the teaching team is on break, do expect delays)


### Question 1
In the lecture, we have seen how to estimate the number of flows by
hashing each flow, interpreting the output as a value in the interval
\[0, 1), and keeping track of the smallest $`k`$ values. In this exercise,
we will look at this technique in further detail.

**1.1.** (1 points)
How could an adversary attack this system such that it outputs an
incorrect estimation of the number of flows? How can the system defend
against this?


**1.2.** (2 points)
Assume there are $`n\in\mathbb{N}, n > 0`$ different flows. Mathematically we
can model their hashes as $`n`$ independent and identically distributed
(i.i.d.) random variables $`X_i`$ with a probability density
function
```math
f_{X_i}(x) =^1
  \begin{cases}
        1,& x\in [0,1),\\
        0,& \text{otherwise}.
    \end{cases}
```
Consider now the minimum of these variables,
$`Y = \min_{i\in [n]}X_i`$ (where $`[n]=\{1, \dots, n\}`$). Calculate
$`\mathrm{Pr}[Y>x]`$.


**1.3.** (2 points)
Calculate the expectation value $`\mathbb{E}[Y]`$.
*Hint:* For a non-negative random variable $`X`$, the expectation value
can be calculated as[1]
```math
\mathbb{E}[X] =^4 \int_0^\infty \mathrm{Pr}[X > x]\mathrm{d}x.
```

[1] <https://en.wikipedia.org/wiki/Expected_value#General_case_2>


**1.4.** (3 points)
Let us now look at the $`k`$th smallest value of the random variables
$`X_i`$, which we denote as $`Y_k`$ ($`Y_1`$ is equal to $`Y`$ from the previous subproblems). Calculate
the probability $`\mathrm{Pr}[Y_{k+1} > x]`$ for $`k \geq 1`$.

*Hint:* $`\mathrm{Pr}[Y_{k+1} > x]`$ can be understood as the probability of
*at most* $`k`$ of the values $`X_i`$ being smaller than $`x`$.
This allows you to write $`\mathrm{Pr}[Y_{k+1} > x]`$ as
$`\mathrm{Pr}[Y_{k} > x]`$ with one additional term.


**1.5.** (4 points)
Show that
$`\mathbb{E}[Y_{k+1}] =^7 \frac{k+1}{n+1}`$

*Hint:* Remember the *integration by parts*,


```math
    \int_a^b f'(x) \cdot g(x) \mathrm{d} x =^8 \left[f(x)\cdot g(x)\right]_a^b - \int_a^b f(x) \cdot g'(x) \mathrm{d} x
```

and show that
$`\binom{n}{k} \int_0^1 x^{k} (1-x)^{n-k} \mathrm{d} x =^9 \frac{1}{n+1}`$.


**1.6.** (5 points, bonus)
Calculate the variance of $`Y_k`$ for $`k\in \{1, 2\}`$,
$`\mathrm{Var}[Y_k] =^{14} \mathbb{E}[Y_k^2] - \mathbb{E}[Y_k]^2`$

*Hint:* You can use $`\mathrm{Pr}[Y^2 > x] = \mathrm{Pr}[Y > \sqrt{x}]`$.


### Question 2
In the context of network monitoring, Bloom filters can be used to
quickly find duplicates. Our duplicate detection system is comprised of
two filter, B0 and B1. We divide the time in intervals *Δ*, which must
be greater than the maximum propagation time of packets *δ*. We ignore
time sync errors for simplicity. At the beginning of each interval, one
of the two filters is reset and is filled with new packets, while the
other one is frozen. For each new packet, both filters are checked for
duplicates.

**2.1.** (1 points)
What kind of answers does a Bloom filter give?


**2.2.** (1 points)
Why does *Δ* need to be bigger than *δ*? What happens if a packet that
is older than the two periods is received?


**2.3.** (1 points)
What is the probability of false positives? Assume the hash functions
are not correlated and the probabilities of single bit values are not
correlated either. Provide both the accurate and the asymptotic
approximation formula.


**2.4.**
Let us now analyze a real world situation. Your monitored router must be
able to process 10<sup>5</sup> packets per second and monitors
duplicates with the system described above. You analyze the network and
decide that *Δ* = 0.5*s* is enough for your purposes.

- (2 points) You want to maintain a maximum probability of false positives
$`P[FP] \leq 0.005`$ throughout the whole process. What is the optimal
number of bits m of each of your Bloom filters? Include the formula you
used. What is the total space taken up by the filters in the router's
memory?


- (2 points) What is the optimal value of k given the previous m and n? Once again,
include the formula you used.


- (2 points) We said or router must be able to process at least 10<sup>5</sup>
packets per second. That includes the Bloom filtering and the routing.
You decide to use a dedicate chip for parallel hashing, that computes 4
parallel hashes in 100*n*s. What is the relative and absolute time
overhead of the filters?


- (1 points) You are dissatisfied with the overhead and want to improve the
performance of the system. Halving the number of hash functions would
half the overhead, but what would the new probability of false
positives?


### Question 3
**Reversible Bloom filters**
Bloom filters are a great tool to find duplicates without false
negatives. With a small addition, they can be used to identify elephant
flows without false negatives. Also, an estimate on the length of the
large flows can be computed. To do this, one has to give inverting
properties to the Bloom filters in use. This exercise will focus on this
addition to Bloom filters.

**3.1.** (2 points)
In a regular Bloom filter, an entry is added to the filter by computing
k different hash functions and using the output of the hash functions as
indices to a bit vector. The bit vector is set to 1 at the computed
indices.
It is easy to tell if a value is in the filter. One has to again compute
the hash of the value and check if all bits are set to 1 at the
positions indicated by the hash functions.
Why is it not possible to create a list with all values in the Bloom
filter?


**3.2.** (2 points)
Why is it not possible to remove something from the filter?


**3.3.** (2 points)
By using counters, the Bloom filter can be made reversible. This means
that one can remove values that were previously added. The bits of the
filter is replaced by counters. Instead of flipping a bit from 0 to 1
when adding a value, the counter is increased. E.g., when a value A
which is added to the filter as is added to a filter which has the
following state: the resulting state would look as follows: Explain why
the counting Bloom filter is invertible and explain how one can remove a
value from the filter.


**3.4.** (1 points)
By using counters, we created a reversible Bloom filter. However, this
introduces a source of possible false negatives. Analyze what happens
when elements are removed from the filter and explain how this can lead
to false negatives afterward.


**3.5.** (4 points)
It is still not possible to list the entries in the Bloom filter. To do
this, we have to further increase the stored state. In addition to the
couter array, we also keep an array (of the same size) in which we store
the bitwise XOR of the input values that map to the respective index.
Example: value 1: value 2: Here, the upper numbers are the input values
and the lower ones are the counter values. After adding values 1 and 2
to a filter, it would look as follows: Note that (1 XOR 2 = 3).
How would you extract a value that was stored in this special Bloom
filter?
