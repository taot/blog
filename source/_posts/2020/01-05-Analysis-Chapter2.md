---
title: Axioms in Analysis Chapter 2 - Natural Numbers
date: 2020-01-05 14:25:36
tags: math
---

# 2.1 The Peano axioms

**Axiom 2.1.** *$0$ is a natural number.*

**Axiom 2.2.** *If $n$ is a natural number, then $n\text{++}$ is also a natural number.*

**Axiom 2.3.** *$0$ is not the successor of any natural number; i.e., we have $n \text{++} \ne 0$ for every natural number $n$.*

**Axiom 2.4.** *Different natural numbers must have different successors; i.e., if $n$, $m$ are natural numbers and $n \ne m$, then $n\text{++} \ne m\text{++}$. Equivalently, if $n\text{++} = m\text{++}$, then we must have $n = m$.*

<!-- more -->

**Axiom 2.5 (Principle of mathematical induction).** *Let $P(n)$ be any property pertaining to a natural number $n$. Suppose that $P(0)$ is true, and suppose that whenever $P(n)$ is true, $P(n\text{++})$ is also true. Then $P(n)$ is true for every natural number n.*

**Proposition 2.1.16 (Recursive definitions).** Suppose for each natural number $n$, we have some function $f_n : N \rightarrow N$ from the natural numbers to the natural numbers. Let $c$ be a natural number. Then we can assign a unique natural number $a_n$ to each natural number $n$, such that $a_0 = c$ and $a_{n++} = f_n(a_n)$ for each natural number $n$.

# 2.2 Adittion

**Definition 2.2.1 (Addition of natural numbers).** Let $m$ be a natural number. To add zero to $m$, we define $0 + m := m$. Now suppose inductively that we have defined how to add n to m. Then we can add $n\text{++}$ to $m$ by defining $(n\text{++}) + m := (n + m)\text{++}$.

**Lemma 2.2.2.** For any natural number $n$, $n + 0 = n$.

**Lemma 2.2.3.** For any natural numbers $n$ and $m$, $n + (m\text{++}) = (n + m)\text{++}$.

**Proposition 2.2.4 (Addition is commutative).** For any natural numbers $n$ and $m$, $n + m = m + n$.

**Proposition 2.2.5 (Addition is associative).** For any natural numbers $a$, $b$, $c$, we have $(a + b) + c = a + (b + c)$.

**Proposition 2.2.6 (Cancellation law).** Let $a$, $b$, $c$ be natural numbers such that $a + b = a + c$. Then we have $b = c$.

**Definition 2.2.7 (Positive natural numbers).** A natural number $n$ is said to be *positive* iff it is not equal to $0$.

**Proposition 2.2.8.** If $a$ is *positive* and $b$ is a natural number, then $a + b$ is *positive* (and hence $b + a$ is also).

**Corollary 2.2.9.** If $a$ and $b$ are natural numbers such that $a + b = 0$, then $a = 0$ and $b = 0$.

**Lemma 2.2.10.** Let $a$ be a *positive* number. Then there exists exactly one natural number $b$ such that $b\text{++} = a$.

**Definition 2.2.11 (Ordering of the natural numbers).** Let $n$ and $m$ be natural numbers. We say that $n$ is *greater than or equal* to $m$, and write $n \ge m$ or $m \le n$, iff we have $n = m + a$ for some natural number $a$. We say that $n$ is *strictly greater than m*, and write $n > m$ or $m < n$, iff $n \ge m$ and $n \ne m$.

**Proposition 2.2.13 (Trichotomy of order for natural numbers).** Let $a$ and $b$ be natural numbers. Then exactly on of the following statements is true: $a < b$, $a = b$, or $a > b$.

**Proposition 2.2.14 (Strong principle of induction).** Let $m_0$ be a natural number, and let $P(m)$ be a property pertaining to an arbitrary natural number m. Suppose that for each $m \ge m_0$, we have the following implication: if $P(m')$ is true for all natural numbers $m_0 \le m' < m$, then $P(m)$ is also true. (In particular, this means that $P(m_0)$ is true, since in this case the hypothesis is vacuous.) Then we can conclude that $P(m)$ is true for all natural numbers $m \ge m_0$.

# 2.3 Multiplication

**Definition 2.3.1 (Multiplication of natural numbers).** Let $m$ be a natural number. To multiply zero to $m$, we define $0 \times m := 0$. Now suppose inductively that we have defined how to multiply $n$ to $m$. Then we can multiply $n\text{++}$ to $m$ by defining $(n\text{++}) \times m := (n \times m) + m$.

**Lemma 2.3.2 (Multiplication is commutative).** Let $n$, $m$ be natural numbers. Then $n \times m = m \times n$.

**Lemma 2.3.3 (Natural numbers have no zero divisors).** Let $n$, $m$ be natural numbers. Then $n \times m = 0$ if and only if at least one of $n$, $m$ is equal to zero. In particular, if $n$ and $m$ are both positive, then $nm$ is also positive.

**Proposition 2.3.4 (Distributive law).** For any natural numbers $a$, $b$, $c$, we have $a(b + c) = ab + ac$ and $(b + c)a = ba + ca$.

**Proposition 2.3.5 (Multiplication is associative).** For any natural numbers $a$, $b$, $c$, we have $(a \times b) \times c = a \times (b \times c)$.

**Proposition 2.3.6 (Multiplication preserves order).** If $a$, $b$ are natural numbers such that $a < b$, and $c$ is positive, then $ac < bc$.

**Corollary 2.3.7 (Cancellation law).** Let $a$, $b$, $c$ be natural numbers such that $ac = bc$ and $c$ is non-zero. Then $a = b$.

**Proposition 2.3.9 (Euclidean algorithm).** Let n be a natural number, and let $q$ be a positive number. Then there exist natural numbers $m$, $r$ such that $0 \le r \lt q$ and $n = mq + r$.

**Definition 2.3.11 (Exponentiation for natural numbers).** Let $m$ be a natural number. To raise $m$ to the power $0$, we define $m^0 := 1$. Now suppose recursively that $m^n$ has been defined for some natural number $n$, then we define $m^{n++} := m^n \times m$.