# Multisets for Scala

Based on [_A New Look at Multisets_ by Norman J Wildberger](https://www.researchgate.net/publication/251497534_A_new_look_at_multisets).

The data structure provided by this library is `MSet`. An `MSet[M,A]` is a multiset of values of type A with multiplicities in M.

For example, an `MSet[Natural,A]` is a multiset where values of type `A` can have zero or more occurrences. An `MSet[Boolean,A]` is an ordinary set.

## Using this library

To get all the functionality of this library, add the following imports:

``` scala
import spire.syntax.all._
import mset._
import Realm._
```

## Why this?

This type differs from most implementations of multisets in that the multiplicities can be integers (for negative occurrences), rational numbers (for fractional occurrences), intervals and probability distributions (for non-deterministic or "fuzzy" occurrences), or any other type of value. The algebra on that type will determine what operations are available on the `MSet`. In particular, `MSet` provides operations for any type of measure which forms a `Realm`, which this library also provides:

``` scala
trait Realm[A] {
  def meet(x: A, y: A): A
  def join(x: A, y: A): A
  def sum(x: A, y: A): A
  def zero: A
}
```

Notably, `MSet[M,A]` forms a `Realm` for any `Realm[M]`.

A `Realm[A]` is a distributive lattice on `A` (through `meet` and `join`), and a commutative monoid (on `sum` and `zero`). Realms must obey the following laws:

### Commutative laws

``` scala
m + n ≡ n + m
m ∨ n ≡ n ∨ m
m ∧ n ≡ n p m
```

### Associative laws

``` scala
k + (m + n) ≡ (k + m) + n
k ∨ (m ∨ n) ≡ (k ∨ m) ∨ n
k ∧ (m ∧ n) ≡ (k ∧ m) ∧ n
```

### Distributive laws

``` scala
k + (m ∨ n) ≡ (k + m) ∨ (k + n)
k + (m ∧ n) ≡ (k + m) ∧ (k + n)
k ∧ (m ∨ n) ≡ (k ∧ m) ∨ (k ∧ n)
k ∨ (m ∧ n) ≡ (k ∨ m) ∧ (k ∨ n)
```

### Identity laws

```
zero + m ≡ m
zero ∨ m ≡ m
zero ∧ m ≡ mempty
```

### Absorption laws

``` scala
m ∨ (m ∧ n) ≡ m
m ∧ (m ∨ n) ≡ m
```

### Idempotent laws

``` scala
m ∨ m ≡ m
m ∧ m ≡ m
```

### Summation law

``` scala
(m ∨ n) + (m ∧ n) ≡ m + n
```

### Partial order

[[Realm]] extends [[spire.algebra.Order]] because any join semilattice
defines an order:

``` scala
m ≤ n ≡ m ∨ n = n
```

### Cancellative law

Some realms may additionally obey a cancellation law, and we call
these _cancellative realms_:

``` scala
(k + n = m + n) => (k = m)
```

### Products and inverses

Two `Realm` subtypes are also provided which are `RigRealm` (realms with products) and `RingRealm` (realms with products and additive inverses).

A well-behaved realm with products requires products to distribute over the realm operations:

``` scala
a * (b ∨ c) ≡ (a * b) ∨ (a * c)
a * (b ∧ c) ≡ (a * b) ∧ (a * c)
a * (b + c) ≡ (a * b) + (a * c)
```

A realm with inverses must obey a De Morgan law:

``` scala
-(a ∨ b) ≡ (-a) ∧ (-b)
-(a ∧ b) ≡ (-a) ∨ (-b)
```

