---
title: Axioms in Analysis Chapter 3 - Set Theory
date: 2020-01-05 16:05:19
tags: math
---

# Fundamentals

**Axiom 3.1 (Sets are objects).** *If $A$ is a set, then $A$ is also an object. In particular, given two sets $A$ and $B$, it is meaningful to ask whether $A$ is also an element of $B$.*

**Definition 3.1.4 (Equality of sets).** Two sets $A$ and $B$ are *equal*, $A = B$, iff every element of $A$ is an element of $B$ and vice versa. To put it another way, $A = B$ if and only if every elemnt $x$ of $A$ belongs also to $B$, and every element $y$ of $B$ belongs also to $A$.

**Axiom 3.2 (Empty set).** *There exists a set $\emptyset$, known as the empty set, which contains no elements, i.e., for every object x we have $x \in \emptyset$.*

**Lemma 3.1.6 (Single choice).** Let $A$ be a non-empty set. Then there exists an object $x$ such that $x \in A$.

<!-- more -->

**Axiom 3.3 (Singleton sets and pair sets).** *If $a$ is an object, then there exists a set $\\{a\\}$ whose only element is $a$, i.e., for every object $y$, we have $y \in \\{a\\}$ if and only if $y = a$; we refer to $\\{a\\}$ as the singleton set whose element is $a$. Furthermore, if $a$ and $b$ are objects, then there exists a set $\\{a, b\\}$ whose only elements are $a$ and $b$; i.e., for every object $y$, we have $y \in \\{a, b\\}$ if and only if $y = a$ or $y = b$; we refer to this set as the pair set formed by $a$ and $b$.*

**Axiom 3.4 (Pairwise union).** *Given any two sets $A$, $B$, there exists a set $A \cup B$, called the union $A \cup B$ of $A$ and $B$, whose elements consists of all the elements which belongs to $A$ or $B$ or both. In other words, for any object $x$,*

$$
x \in A \cup B \Longleftrightarrow (x \in A\ or\ x \in B)
$$

**Lemma 3.1.13.** If $a$ and $b$ are objects, then $\\{a, b\\} = \\{a\\} \cup \\{b\\}$. If $A$, $B$, $C$ are sets, then the union operation is commutative (i.e., $A \cup B = B \cup A$) and associative (i.e., $(A \cup B) \cup C = A \cup (B \cup C)$). Also, we have $A \cup A = A \cup \emptyset = \emptyset \cup A = A$.

**Definition 3.1.15 (Subsets).** Let $A$, $B$ be sets. We say that $A$ is a *subset* of B, denoted $A \subseteq B$, iff every element of $A$ is also an element of $B$, i.e.

$$
For\ any\ object\ x, x \in A \Longrightarrow x \in B.
$$

We say that $A$ is a *proper subset* of $B$, denoted $A \subset B$, if $A \subseteq B$ and $A \neq B$.

**Proposition 3.1.18 (Sets are partially ordered by set inclusion).** Let $A$, $B$, $C$ be sets. If $A \subseteq B$ and $B \subseteq C$ then $A \subseteq C$. If $A \subseteq B$ and $B \subseteq A$, then $A = B$. Finally, if $A \subset B$ and $B \subset C$ then $A \subset C$.

**Axiom 3.5 (Axiom of specification).** *Let $A$ be a set, and for each $x \in A$, let $P(x)$ be a property pertaining t $x$ (i.e., $P(x)$ is either a true statement or a false statement). Then there exists a set, called $\\{ x \in A : P(x) is true \\}$ (or simply $\\{ x \in A : P(x) \\}$ for short), whose elements are precisely the elements $x$ in $A$ for which $P(x)$ is true. In other words, for any object y,*

$$
y \in \\{ x \in A : P(x)\ is\ true \\} \Longleftrightarrow (y \in A\ and\ P(y)\ is\ true).
$$

**Definition 3.1.23 (Intersection).** The *intersection* $S_1 \cap S_2$ of two sets is defined to be the set

$$
S_1 \cap S_2 := \\{ x \in S_1 : x \in S_2 \\}
$$

**Definition 3.1.27 (Difference sets).** Given two sets $A$ and $B$, we define the set $A - B$ or $A \backslash B$ to be the set $A$ with any elements of $B$ removed:

$$
A \backslash B := \\{ x \in A : x \not\in B \\}
$$

**Proposition 3.1.28 (Sets form a boolean algebra).** Let $A$, $B$, $C$ be sets, and let $X$ be a set containing $A$, $B$, $C$ as subsets.

(a) (Minimal element) We have $A \cup \emptyset = A$ and $A \cap \emptyset = \emptyset$.

(b) (Maximal element) We have $A \cup X = X$ and $A \cap X = A$.

(c) (Identity) We have $A \cap A = A$ and $A \cup A = A$.

(d) (Commutativity) We have $A \cup B = B \cup A$ and $A \cap B = B \cap A$.

(e) (Associativity) We have $(A \cup B) \cup C = A \cup (B \cup C)$ and $(A \cap B) \cap C = A \cap (B \cap C)$.

(f) (Distributivity) We have $A \cap (B \cup C) = (A \cap B) \cup (A \cap B)$, and $A \cup (B \cap C) = (A \cup B) \cap (A \cup C)$.

(g) (Parition) We have $A \cup (X \backslash A) = X$ and $A \cap (X \backslash A) = \emptyset$.

(h) (De Morgan laws) We have $X \backslash (A \cup B) = (X \backslash A) \cap (X \backslash B)$ and $X \backslash (A \cap B) = (X \backslash A) \cup (X \backslash B)$.

**Axiom 3.6 (Replacement).** *Let $A$ be a set. For any object $x \in A$, and any object $y$, suppose we have a statement $P(x, y)$ pertaining to $x$ and $y$, such that for each $x \in A$ there is at most one $y$ for which $P(x, y)$ is true. Then there exists a set $\\{ y : P(x, y)\ is\ true\ for\ some\ x \in A \\}$, such that for any object $z$,*

$$
z \in \\{ y : P(x, y)\ is\ true\ for\ some\ x \in A \\} \Longleftrightarrow P(x, y)\ is\ true\ for\ some\ x \in A
$$

**Axiom 3.7 (Infinity).** *There exists a set $N$, whose elements are called natural numbers, as well as an object $0$ in $N$, and an object $n\text{++}$ assigned to every natural number $n \in N$, such that the Peano axioms (Axioms 2.1 - 2.5) hold.*

# 3.2 Russell's paradox

**Axiom 3.9 (Regularity).** *If $A$ is a non-empty set, then there is at least one element $x$ of $A$ which is either not a set, or is disjoint from $A$.*

# 3.3 Functions

**Definition 3.3.1 (Functions).** Let $X$, $Y$ be sets, and let $P(x, y)$ be a property pertaining to an object $x \in X$ and an object $y \in Y$, such that for every $x \in X$, there is exactly one $y \in Y$ for which $P(x, y)$ is true (this is sometimes known as the *vertical line test*). Then we define the *function* $f : X \rightarrow Y$ *defined by $P$ on the domain $X$ and range $Y$* to be the object which, given any input $x \in X$, assigns an output $f(x) \in Y$, defined to be the unique object $f(x)$ for which $P(x, f(x))$ is true. Thus, for any $x \in X$ and $y \in Y$,

$$
y = f(x) \Longleftrightarrow P(x, y)\ is\ true.
$$

**Definition 3.3.7 (Equality of functions).** Two functions $f : X \rightarrow Y$, $g : X \rightarrow Y$ with the same domain and range are said to be equal, $f = g$, if and only if $f(x) = g(x)\ for\ all\ x \in X$. (If $f(x)$ and $g(x)$ agree for some values of $x$, but not others, then we do not consider $f$ and $g$ to be equal).

**Definition 3.3.10 (Composition).** Let $f : X \rightarrow Y$ and $g : Y \rightarrow Z$ be two functions, such that the range of $f$ is the same set as the domain of $g$. We then define the *composition* $g \circ f : X \rightarrow Z$ of the two functions $g$ and $f$ to be the function defined explicitly by the formula

$$
(g \circ f)(x) := g(f(x))
$$

If the range of $f$ does not match the domain of $g$, we leave the composition $g \circ f$ undefined.

**Lemma 3.3.12 (Composition is associative).** Let $f : X \rightarrow Y$, $g : Y \rightarrow Z$, and $h : Z \rightarrow W$ be functions. Then $f \circ (g \circ h) = (f \circ g) \circ h$.

**Definition 3.3.14 (One-to-one functions).** A function $f$ is *one-to-one (or injective* if different elements map to different elements:

$$
x \ne x' \Longrightarrow f(x) \ne f(x')
$$

Equivalently, a function is one-to-one if

$$
f(x) = f(x') \Longrightarrow x = x'
$$

**Definition 3.3.17 (Onto functions).** A function $f$ is *onto (or surjective)* if $f(X) = Y$, i.e., every element in $Y$ comes from applying $f$ to some element in $X$:

For every $y \in Y$, there exists $x \in X$ such that $f(x) = y$.

**Definition 3.3.20 (Bijective functions).** Functions $f : X \rightarrow Y$ which are both one-to-one and onto are also called *bijective* or *invertible*.

# 3.4 Images and inverse images

**Definition 3.4.1 (Images of sets).** If $f : X \rightarrow Y$ is a function from $X$ to $Y$, and $S$ is a set in $X$, we define $f(S)$ to be the set

$$
f(S) := \\{ f(x) : x \in S \\};
$$

this set is a subset of $Y$, and is sometimes called the *image of $S$ under the map $f$*.

**Definition 3.4.4 (Inverse images).** If $U$ is a subset of $Y$, we define the set $f^{-1}(U)$ to be the set

$$
f^{-1}(U) := \\{ x \in X : f(x) \in U \\}
$$

**Axiom 3.10 (Power set axiom).** *Let $X$ and $Y$ be sets. Then there exists a set, denoted $Y^X$, which consists of all the functions from $X$ to $Y$, thus*

$$
f \in Y^X \Longleftrightarrow (f\ is\ a\ function\ with\ domain\ X\ and\ range\ Y).
$$

**Lemma 3.4.9.** Let X be a set. Then the set

$$
\\{ Y : Y\ is\ a\ subset\ of\ X \\}
$$

is a set.

**Axiom 3.11 (Union).** *Let $A$ be a set, all of whose elements are themselves sets. Then there exists a set $\bigcup A$ whose elements are precisely those objects which are elements of the elements of $A$, thus for all objects $x$*

$$
x \in \bigcup A \Longleftrightarrow (x \in S\ for\ some\ S \in A).
$$

# Cartesian products

**Definition 3.5.1 (Ordered pair).** If $x$ and $y$ are any objects (possibly equal), we define the *ordered pair* $(x, y)$ to be a new object, consisting of $x$ as its first component and $y$ as its second component. Two ordered pairs $(x, y)$ and $(x', y')$ are considered equal if and only if both their components match, i.e.

$$
(x, y) = (x', y') \Longleftrightarrow (x = x'\ and\ y = y').
$$

**Definition 3.5.4 (Cartesian product).** If $X$ and $Y$ are sets, then we define the *Cartesian product* $X \times Y$ to be the collection of ordered pairs, whose first component lies in $X$ and second component lies in $Y$, thus

$$
X \times Y = \\{ (x, y) : x \in X, y \in Y \\}
$$

or equivalently

$$
a \in (X \times Y) \Longleftrightarrow (a = (x, y)\ for\ some\ x \in X\ and\ y \in Y).
$$

**Definition 3.5.7 (Ordered n-tuple and n-fold Cartesian product).** Let $n$ be a natural number. An *ordred n-tuple* $(x_i)_{1 \le i \le n}$ (also denoted $(x_1, \ldots , x_n)$) is a collection of objects $x_i$, one for every natural number $i$ between $1$ and $n$; we refer to $x_i$ as the $i^th$ *component* of the ordered n-tuple. Two ordered n-tuples $(x_i)_{1 \le i \le n}$ and $(y_i)_{1 \le i \le n}$ are said to be equal iff $x_i = y_i$ for all $1 \le i \le n$. If $(X_i)_{1 \le i \le n}$ is an ordered n-tuple of sets, we define their Cartesian product (also denoted $X_1 \times \ldots \times X_n$) by

$$
\prod_{1 \le i \le n} X_i := \\{ (x_i)_{1 \le i \le n} : x_i \in X_i\ for\ all\ 1 \le i \le n \\}.
$$

**Lemma 3.5.12 (Finite choice).** Let $n \ge 1$ be a natural number, and for each natural number $1 \le i \le n$, let $X_i$ be a non-empty set. Then there exists an n-tuple $(x_i)_{1 \le i \le n}$ such that $x_i \in X_i$ for all $1 \le i \le n$. In other words, if each $X_i$ is non-empty, then the set $\prod_{1 \le i \le n} X_i$ is also non-empty.

**Definition 3.6.1 (Equal cardinality).** We say that two sets $X$ and $Y$ have *equal cardinality* iff there exists a bijection $f : X \rightarrow Y$ from $X$ to $Y$.

**Definition 3.6.5.** Let $n$ be a natural number. A set $X$ is said to have *cardinality* $n$, iff it has equal cardinality with $\\{ i \in N : 1 \le i \le n \\}$. We also say that $X$ *has* $n$ elements iff it has cardinality $n$.

**Proposition 3.6.8 (Uniqueness of cardinality).** Let $X$ be a set with some cardinality $n$. Then $X$ cannot have any other cardinality, i.e., $X$ cannot have cardinality $m$ for any $m \ne n$.

**Lemma 3.6.9.** Suppose that $n \ge 1$, and $X$ has cardinality n. Then $X$ is non-empty, and if x is any element of $X$, then the set $X - \\{ x \\}$ (i.e., $X$ with the element $x$ removed) has cardinality $n - 1$.

**Theorem 3.6.12.** *The set of natural numbers $N$ is infinite.*

**Proposition 3.6.14 (Cardinal arithmetic).**

(a) Let $X$ be a finite set, and let $x$ be an object which is not an element of $X$. Then $X \cup \\{ x \\}$ is finite and $\\#(X \cup \\{ x \\}) = \\#(X) + 1$.

(b) Let $X$ and $Y$ be finite sets. Then $X \cup Y$ is finite and $\\#(X \cup Y) \le \\#(X) + \\#(Y)$. If in addition $X$ and $Y$ are disjoint (i.e., $X \cap Y = \emptyset$), then $\\#(X \cup Y) = \\#(X) + \\#(Y)$.

(c) Let $X$ be a finite set, and let $Y$ be a subset of $X$. Then $Y$ is finite, and $\\#(Y) \le \\#(X)$. If in addition $Y \ne X$ (i.e., Y is a proper subset of X), then we have $\\#(Y) \lt \\#(X)$.

(d) If $X$ is a finite set, and $f : X \rightarrow Y$ is a function, then $f(X)$ is a finite set with $\\#(f(X)) \le \\#(X)$. If in addition $f$ is one-to-one, then $\\#(f(X)) = \\#(X)$.

(e) Let $X$ and $Y$ be finite sets. Then Cartesian product $X \times Y$ is finite and $\\#(X \times Y) = \\#(X) \times \\#(Y)$.

(f) Let $X$ and $Y$ be finite sets. Then the set $Y^X$ (defined in Axiom 3.10) is finite and $\\#(Y^X) = \\#(Y)^{\\#(X)}$.