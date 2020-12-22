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

**Solution**:
If a publicly known hash function were used, an adversary could locally
search for flows that are mapped to very low values and thus influence
the result. The system can use a hidden or keyed hash function for the
estimation to avoid this.

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

**Solution**:
For $`x < 0`$, we obviously have $`\mathrm{Pr}[Y > x]=1`$; for $`x\geq 1`$, we find
$`\mathrm{Pr}[Y>x]=0`$. For $`x\in[0,1)`$, we can calculate

```math
    \begin{aligned}
    \mathrm{Pr}[Y > x] &=^2 \mathrm{Pr}\big[\forall i \in [n]. X_i > x\big]\\
        &= \prod_{i=1}^n Pr[X_i > x]\\
        &= \prod_{i=1}^n (1-x)\\
        &= (1-x)^n
    \end{aligned}
```

To obtain (2) we have used the fact that the $`X_i`$ are all independent.
Overall, we can write

```math
\mathrm{Pr}[Y>x] =^3
     \begin{cases}
        1,& x<0,\\
        (1-x)^n, & 0 \leq x<1,\\
        0, & 1\leq x.
     \end{cases}
```

**1.3.** (2 points)
Calculate the expectation value $`\mathbb{E}[Y]`$.
*Hint:* For a non-negative random variable $`X`$, the expectation value
can be calculated as[1]
```math
\mathbb{E}[X] =^4 \int_0^\infty \mathrm{Pr}[X > x]\mathrm{d}x.
```

[1] <https://en.wikipedia.org/wiki/Expected_value#General_case_2>

**Solution**:
Using (3) and (4) we obtain

```math
    \begin{aligned}
        \mathbb{E}[Y] &=^5 \int_0^\infty \mathrm{Pr}[Y>x] \mathrm{d}x\\
        &= \int_0^1 (1-x)^n \mathrm{d}x\\
        &= \left[-\frac{1}{n+1}\cdot (1-x)^{n+1}\right]_0^1\\
        &= \frac{1}{n+1}.
    \end{aligned}
```

**1.4.** (3 points)
Let us now look at the $`k`$th smallest value of the random variables
$`X_i`$, which we denote as $`Y_k`$ ($`Y_1`$ is equal to $`Y`$ from the previous subproblems). Calculate
the probability $`\mathrm{Pr}[Y_{k+1} > x]`$ for $`k \geq 1`$.

*Hint:* $`\mathrm{Pr}[Y_{k+1} > x]`$ can be understood as the probability of
*at most* $`k`$ of the values $`X_i`$ being smaller than $`x`$.
This allows you to write $`\mathrm{Pr}[Y_{k+1} > x]`$ as
$`\mathrm{Pr}[Y_{k} > x]`$ with one additional term.

**Solution**:
Considering the hint, we notice that the probability of at most $`k`$
values being smaller than $`x`$ ($`\mathrm{Pr}[Y_{k+1}]`$) is equal to the
probability of at most $`k - 1`$ values being smaller than $`x`$
($`\mathrm{Pr}[Y_{k}]`$) *plus* the probability of *exactly* $`k`$ values being
smaller than $`x`$. We therefore obtain
```math
    \begin{aligned}
        \mathrm{Pr}[Y_{k+1}>x] =^6 \mathrm{Pr}[Y_{k} > x] + \binom{n}{k}x^{k} (1-x)^{n-k}.
    \end{aligned}
```

**1.5.** (4 points)
Show that
$`\mathbb{E}[Y_{k+1}] =^7 \frac{k+1}{n+1}`$

*Hint:* Remember the *integration by parts*,


```math
    \int_a^b f'(x) \cdot g(x) \mathrm{d} x =^8 \left[f(x)\cdot g(x)\right]_a^b - \int_a^b f(x) \cdot g'(x) \mathrm{d} x
```

and show that
$`\binom{n}{k} \int_0^1 x^{k} (1-x)^{n-k} \mathrm{d} x =^9 \frac{1}{n+1}`$.

**Solution**:
Using (6), we find
$`\mathbb{E}[Y_{k+1}] =^{10} \mathbb{E}[Y_{k}] + \binom{n}{k} \int_0^1 x^{k} (1-x)^{n-k} \mathrm{d} x`$.

To calculate the remaining integral, we use integration by parts and
identify $`g(x) = x^k`$ and
$`f(x) = (1 − x)^{n - k}`$. We therefore obtain

```math
    \begin{aligned}
        \int_0^1 (1-x)^{n-k}x^{k} \mathrm{d} x &=^{11} \left[-\frac{1}{n-k+1} (1-x)^{n-k+1} x^k\right]_0^1 -\int_0^1-\frac{1}{n-k+1} (1-x)^{n-k+1} kx^{k-1} \mathrm{d} x\\
        &=0 + \frac{k}{n-k+1} \int_0^1 (1-x)^{n-k+1} x^{k-1} \mathrm{d} x.
    \end{aligned}
```

Including the binomial coefficient, we get

```math
    \binom{n}{k}\, \int_0^1 x^{k} (1-x)^{n-k} \mathrm{d} x = \binom{n}{k-1}\, \int_0^1 x^{k-1} (1-x)^{n-k+1} \mathrm{d} x.
```

By iterating this process, we find

```math
    \begin{aligned}
        \binom{n}{k}\, \int_0^1 x^{k} (1-x)^{n-k} \mathrm{d} x &=^{12} \int_0^1 (1-x)^{n} \mathrm{d} x\\
        &= \frac{1}{n+1},\\
        \mathbb{E}[Y_{k+1}] &= \mathbb{E}[Y_k] + \frac{1}{n+1}.
    \end{aligned}
```

Overall, by induction we obtain

```math
    \begin{aligned}
        \mathbb{E}[Y_{k+1}] &=^{13} \mathbb{E}[Y_1] + \frac{k}{n+1}\\
        &=\frac{k+1}{n+1}.
    \end{aligned}
```

**1.6.** (5 points, bonus)
Calculate the variance of $`Y_k`$ for $`k\in \{1, 2\}`$,
$`\mathrm{Var}[Y_k] =^{14} \mathbb{E}[Y_k^2] - \mathbb{E}[Y_k]^2`$

*Hint:* You can use $`\mathrm{Pr}[Y^2 > x] = \mathrm{Pr}[Y > \sqrt{x}]`$.

**Solution**:
Using the hint, we see (for $`x\in[0, 1]`$)
$`\mathrm{Pr}[Y_1>x] =^{15} \left(1-\sqrt{x}\right)^n`$.

The expectation value $`\mathbb{E}[Y_1^2]`$ is thus given by
$`\mathbb{E}[Y_1^2] =^{16} \int_0^1 \left(1-\sqrt{x}\right)^n \mathrm{d} x`$.

Using the substitution $`y^{2} = x`$, $`2y\mathrm{d} y=\mathrm{d} x`$ and
integration by parts, we obtain

```math
    \begin{aligned}
    \mathbb{E}[Y_1^2] &=^{17} \int_0^1 (1-y)^n\cdot 2y \mathrm{d} y\\
    &= \frac{2}{n+1} \int_0^1 (1-y)^{n+1}\mathrm{d} y\\
    &= \frac{2}{(n+1)(n+2)}.
    \end{aligned}
```

The variance of $`Y_1`$ is thus given by

```math
    \begin{aligned}
    \mathrm{Var}[Y_1] &=^{18} \mathbb{E}[Y_1^2] - \mathbb{E}[Y_1]^2\\
    &= \frac{2}{(n+1)(n+2)} - \frac{1}{(n+1)^2}\\
    &= \frac{n}{(n+1)^2(n+2)}\\
    &\approx\frac{1}{n^2}.
    \end{aligned}
```

Similarly, for $`Y_2`$ we find (for $`x\in[0, 1]`$)
```math
    \mathrm{Pr}[Y_2>x] =^{19} \mathrm{Pr}[Y_1>x] + n\sqrt{x}\left(1-\sqrt{x}\right)^{n-1}.
```

This yields (applying the same substitution and using integration by
parts multiple times)

```math
    \begin{aligned}
    \mathbb{E}[Y_2^2] &=^{20} \mathbb{E}[Y_1^2] + 2n\int_0^1 y^2(1-y)^{n-1}\mathrm{d} y\\
    &= \mathbb{E}[Y_1^2] + \frac{4n}{n}\int_0^1 y(1-y)^{n}\mathrm{d} y\\
    &= \mathbb{E}[Y_1^2] + \frac{4}{n+1}\int_0^1 (1-y)^{n+1}\mathrm{d} y\\
    &= \mathbb{E}[Y_1^2] + \frac{4}{(n+1)(n+2)}\\
    &= \frac{6}{(n+1)(n+2)}.
    \end{aligned}
```

This leads to the variance

```math
    \begin{aligned}
    \mathrm{Var}[Y_2] &=^{21} \mathbb{E}[Y_2^2] - \mathbb{E}[Y_2]^2\\
    &= \frac{6}{(n+1)(n+2)} - \frac{4}{(n+1)^2}\\
    &= 2\cdot\frac{n-1}{(n+1)^2(n+2)}\\
    &\approx\frac{2}{n^2}.
    \end{aligned}
```

We see that $`\mathrm{Var}[Y_2] \approx 2\cdot\mathrm{Var}[Y_1]`$, i.e., the
variance of $`Y_2`$ is *larger* than the variance of
$`Y_1`$. However, the standard deviation $`\sigma`$, which roughly
corresponds to the expected error, is the square root of the variance,
$`\sigma_i = \sqrt{\mathbb{E}[Y_i]}`$. This means that the *relative
error*, i.e., the standard deviation divided by the expectation value,
is *smaller* for $`Y_2`$:

```math
    \frac{\sigma_1}{\mathbb{E}[Y_1]} \approx^{22} 1,\quad \frac{\sigma_2}{\mathbb{E}[Y_2]} \approx \frac{1}{\sqrt{2}}.
```

Generally, using $`Y_k`$, the error of the estimation can be
reduced by a factor of roughly $`\sqrt{k}`$ compared to $`Y_1`$.

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

**Solution**:
A Bloom filter is built to answer with "surely not present" or "possibly
present", that is it will give the requester false positives but never
false negatives. To be very concise, if it says no, it means no.

**2.2.** (1 points)
Why does *Δ* need to be bigger than *δ*? What happens if a packet that
is older than the two periods is received?

**Solution**:
The choice of *Δ* should prevent very old packets from arriving at the
destination router. In fact, if a packet older than 2*Δ* arrives, the
Bloom filters will have been both reset, so detection will fail. If such
a packet arrives, it is dropped.

**2.3.** (1 points)
What is the probability of false positives? Assume the hash functions
are not correlated and the probabilities of single bit values are not
correlated either. Provide both the accurate and the asymptotic
approximation formula.

**Solution**:
After the n insertions, we get a new element that is not in the filter.
For a false positive to happen, we would need to have all k bits already
set to 1. By independence:
$`P\left[FP\right] = \left(1-\left[1-\frac{1}{m}\right]^{nk}\right)^k`$
Asymptotically:
$`P\left[FP\right] = \left(1-e^{-\frac{nk}{m}}\right)^k`$

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

**Solution**:
Since the probability increases while the filter fills up, we will
consider n equal to the total number of packets we get in *Δ*.
$`n = 10^5 \frac{pkt}{s} \times 0.5 s  = 5\times10^4`$
The best m is given by the following formula:
$`m = -\frac{n\ln{p}}{\left(\ln2\right)^2}`$
Therefore:
$`m = -\frac{5\times10^4\ln{0.005}}{\left(\ln2\right)^2} \approx 5.5\times10^5 b = 550 Kb`$
The total memory needed is 1.1 Mb.

- (2 points) What is the optimal value of k given the previous m and n? Once again,
include the formula you used.

**Solution**:
The best k is given by the following formula:
*k* =  − log<sub>2</sub>*p*
Therefore:
*k* =  − log<sub>20.005</sub> ≈ 7.64
Alternatively,
$`k = \frac{m}{n}\ln{2} = \frac{5.5\times10^5}{5\times10^4}\ln{2} = 7.62`$
(difference due to approximations).
Approximating to integer, k=8.

- (2 points) We said or router must be able to process at least 10<sup>5</sup>
packets per second. That includes the Bloom filtering and the routing.
You decide to use a dedicate chip for parallel hashing, that computes 4
parallel hashes in 100*n*s. What is the relative and absolute time
overhead of the filters?

**Solution**:
Each packet gets hashed 8 times, then checked against the two filters
and, if conditions are met, added to the active filter. The hashes used
in these operations are the same, so we have k hashing operations per
packet. Processing 10<sup>5</sup> packets per second, we have that the
number of hash operations is:
*n*<sub>*H*</sub> = 8 × 10<sup>5</sup>
Since we can compute 4 hashes is 100*n*s, we have that the total time
spent hashing is:
$`t_H = 8 \times 10^5 \times \frac{10^{-7}s}{4} = 0.02s`$
Relative overhead:
$`OH = \frac{0.02s}{1s} = 0.02 = 2\%`$

- (1 points) You are dissatisfied with the overhead and want to improve the
performance of the system. Halving the number of hash functions would
half the overhead, but what would the new probability of false
positives?

**Solution**:
$`P\left[FP\right] = \left(1-e^{-\frac{nk}{m}}\right)^k = \left(1-e^{-\frac{5\times10^4\times4}{550\times10^3}}\right)^4 \approx 0.009`$
The probability of false positives increases with less hash functions.

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

**Solution**:
To be able to create a list, one would have to try with every possible
value if it is in the Bloom filter. This is not practical for large
input sets. Furthermore, due to hash collisions, it is not possible to
exactly recreate the values that were added to the filter.

**3.2.** (2 points)
Why is it not possible to remove something from the filter?

**Solution**:
There might be hash collisions. Assume that a value A is added to the
filter as and a value B as After adding values A and B to a Bloom
filter, it would look as follows: If one now tries to remove value A by
setting all bits according to the hashes of A to 0 this would result in
Which means that value B is now not in the filter anymore.

**3.3.** (2 points)
By using counters, the Bloom filter can be made reversible. This means
that one can remove values that were previously added. The bits of the
filter is replaced by counters. Instead of flipping a bit from 0 to 1
when adding a value, the counter is increased. E.g., when a value A
which is added to the filter as is added to a filter which has the
following state: the resulting state would look as follows: Explain why
the counting Bloom filter is invertible and explain how one can remove a
value from the filter.

**Solution**:
For removing elements from the filter, hash collisions are no problem
anymore because they would only result in higher counter values in the
filter which \"store\" the information about the collision. Removing a
value involves computing the hash functions and decrementing the
counters at the computed indices.
If we inspect the example from the previous subtask we see that when
adding values A and B to the filter, we get After removing value A we
see that the resulting state is This means that value B is still in
filter.

**3.4.** (1 points)
By using counters, we created a reversible Bloom filter. However, this
introduces a source of possible false negatives. Analyze what happens
when elements are removed from the filter and explain how this can lead
to false negatives afterward.

**Solution**:
Hash collisions are still possible and common. If an element A that was
never added is removed from the filter, a check for the presence of
value B might fail even if value B was initially added to the filter.

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

**Solution**:
The values can be extracted as follows:

1.  Find a filter position in which the counter is 1. This means that
    there was no collision and that the input value can be directly seen
    in the filter.

2.  Remove that value from the filter by decrementing the counters
    according to the hashes of the value and XOR the stored values in
    the same filter locations.

3.  If there are still values in the filter (if the filter is not all
    0s), repeat from step 1.

Note that there might not exist a counter equal to 1; however, this is
very unlikely in practice as you can make the filter large enough such
that the collision rate is small.

The question might arise on why not to simply use a regular list to
store the elements. There are multiple benefits when using these kind of
Bloom filters. First of all, they are fast: For a Bloom filter with *k*
hash functions, deletions and lookups take *O*(*k*). Another good thing
is that you can dimension it as you like. You have a design tradeoff
between memory usage and error rate. Error meaning in this case that a
value cannot be read from the filter because there were too many hash
collisions. This property makes it useful for the use in memory limited
devices where it is not feasible to store all possible values, e.g.,
flows in switches.
