# GCD and Extended GCD — CryptoHack Notes

# 1. Greatest Common Divisor

## Main Idea

The Greatest Common Divisor, or GCD, is the largest integer that divides two positive integers without remainder.

For example:

| Numbers | Common divisors | GCD |
|---|---:|---:|
| 12 and 8 | 1, 2, 4 | 4 |
| 7 and 9 | 1 | 1 |
| 81 and 57 | 1, 3 | 3 |

The GCD is important because it tells us whether two numbers share a factor.

If:

```text
gcd(a, b) = 1
```

then `a` and `b` are called **coprime**.

<details>
<summary>Русский вывод</summary>

НОД показывает самый большой общий делитель двух чисел.

Если НОД равен `1`, значит числа взаимно простые.

</details>

---

## Challenge

<details>
<summary>Original task (EN)</summary>

Greatest Common Divisor

The Greatest Common Divisor (GCD), sometimes known as the highest common factor, is the largest number which divides two positive integers `(a,b)`.

For `a=12,b=8` we can calculate the divisors of `a`: `{1,2,3,4,6,12}` and the divisors of `b`: `{1,2,4,8}`. Comparing these two, we see that `gcd(a,b)=4`.

Now calculate `gcd(a,b)` for:

```text
a = 66528
b = 52920
```

</details>

<details>
<summary>Русский перевод задания</summary>

Найдите наибольший общий делитель для:

```text
a = 66528
b = 52920
```

То есть нужно найти самое большое число, на которое оба числа делятся без остатка.

</details>

---

# 2. Solving the GCD Challenge

We need to calculate:

```text
gcd(66528, 52920)
```

A naive method would be:
1. list all divisors of `66528`;
2. list all divisors of `52920`;
3. compare both lists;
4. choose the largest common divisor.

But this is slow for large numbers.

Instead, we use the **Euclidean Algorithm**.

---

## Euclidean Algorithm Formula

```text
gcd(a, b) = gcd(b, a mod b)
```

This means:

```text
Instead of solving gcd(a, b),
solve gcd(b, remainder).
```

We repeat this until the remainder becomes zero.

The last non-zero remainder is the GCD.

---

## Step-by-step Table

| Step | Current `a` | Current `b` | Operation | Remainder |
|---:|---:|---:|---|---:|
| 1 | 66528 | 52920 | `66528 % 52920` | 13608 |
| 2 | 52920 | 13608 | `52920 % 13608` | 12096 |
| 3 | 13608 | 12096 | `13608 % 12096` | 1512 |
| 4 | 12096 | 1512 | `12096 % 1512` | 0 |

When the remainder becomes `0`, we stop.

The last non-zero remainder was:

```text
1512
```

So:

```text
gcd(66528, 52920) = 1512
```

<details>
<summary>Русский вывод</summary>

Ответ:

```text
1512
```

Последний ненулевой остаток в алгоритме Евклида — это и есть НОД.

</details>

---

## Same Solution in Division Form

Sometimes the Euclidean Algorithm is easier to understand if written as ordinary division:

```text
66528 = 1 × 52920 + 13608
52920 = 3 × 13608 + 12096
13608 = 1 × 12096 + 1512
12096 = 8 × 1512  + 0
```

The final non-zero remainder is:

```text
1512
```

Therefore:

```text
gcd = 1512
```

---

## Why the Division Form Helps

Each line has this structure:

```text
a = quotient × b + remainder
```

For example:

```text
66528 = 1 × 52920 + 13608
```

This means:

```text
13608 = 66528 - 1 × 52920
```

So the remainder is built from the previous two numbers.

This is the key idea behind both:
- Euclidean Algorithm;
- Extended Euclidean Algorithm.

---

## Python Solution

```python
def gcd(a, b):
    while b != 0:
        # Replace the larger problem gcd(a, b)
        # with the smaller problem gcd(b, a % b)
        a, b = b, a % b

    # When b becomes 0, a is the last non-zero remainder
    return a


print(gcd(66528, 52920))
```

Output:

```text
1512
```

---

## Code Explanation

```python
while b != 0:
```

Keep going until the second number becomes zero.

```python
a, b = b, a % b
```

This line does two things at once:

| Old value | New value |
|---|---|
| `a` | becomes old `b` |
| `b` | becomes `a % b` |

So this:

```text
gcd(66528, 52920)
```

becomes:

```text
gcd(52920, 13608)
```

then:

```text
gcd(13608, 12096)
```

then:

```text
gcd(12096, 1512)
```

then:

```text
gcd(1512, 0)
```

At that point the answer is `1512`.

---

# 3. Why Euclid's Algorithm Works

Suppose:

```text
a = q × b + r
```

where:
- `q` is the quotient;
- `r` is the remainder.

Then:

```text
r = a - q × b
```

Any number that divides both `a` and `b` also divides:

```text
a - q × b
```

So it also divides `r`.

That means:

```text
gcd(a, b) = gcd(b, r)
```

This is why we are allowed to replace:

```text
gcd(a, b)
```

with:

```text
gcd(b, a mod b)
```

---

## Visual Flow

```text
gcd(66528, 52920)
        |
        v
gcd(52920, 13608)
        |
        v
gcd(13608, 12096)
        |
        v
gcd(12096, 1512)
        |
        v
gcd(1512, 0)
        |
        v
1512
```

---

# 4. Extended GCD

## Main Idea

The Extended Euclidean Algorithm finds not only:

```text
gcd(a, b)
```

but also numbers `u` and `v` such that:

```text
a × u + b × v = gcd(a, b)
```

This equation is called **Bézout's Identity**.

---

## Why This Matters

In cryptography, Extended GCD is used to find modular inverses.

A modular inverse is needed in:
- RSA private key generation;
- modular division;
- finite fields;
- Chinese Remainder Theorem;
- many cryptographic attacks and constructions.

---

## Challenge

<details>
<summary>Original task (EN)</summary>

Extended GCD

Let `a` and `b` be positive integers.

The extended Euclidean algorithm is an efficient way to find integers `u,v` such that

```text
a × u + b × v = gcd(a,b)
```

Using the two primes:

```text
p = 26513
q = 32321
```

find the integers `u,v` such that

```text
p × u + q × v = gcd(p,q)
```

Enter whichever of `u` and `v` is the lower number as the flag.

</details>

<details>
<summary>Русский перевод задания</summary>

Даны два простых числа:

```text
p = 26513
q = 32321
```

Нужно найти такие целые числа `u` и `v`, что:

```text
p × u + q × v = gcd(p, q)
```

В качестве ответа нужно ввести меньшее из чисел `u` и `v`.

</details>

---

# 5. Solving the Extended GCD Challenge

We need:

```text
26513 × u + 32321 × v = gcd(26513, 32321)
```

Since both numbers are prime and different:

```text
gcd(26513, 32321) = 1
```

So the equation becomes:

```text
26513 × u + 32321 × v = 1
```

---

## Extended GCD Python Solution

```python
def extended_gcd(a, b):
    # Base case:
    # gcd(a, 0) = a
    if b == 0:
        return a, 1, 0

    # Recursive step:
    # solve smaller problem first
    gcd_value, x1, y1 = extended_gcd(b, a % b)

    # Rebuild coefficients for the original pair (a, b)
    x = y1
    y = x1 - (a // b) * y1

    return gcd_value, x, y


g, u, v = extended_gcd(26513, 32321)

print("gcd:", g)
print("u:", u)
print("v:", v)
print("lower:", min(u, v))
```

Output:

```text
gcd: 1
u: 10245
v: -8404
lower: -8404
```

So the answer is:

```text
-8404
```

<details>
<summary>Русский вывод</summary>

Ответ:

```text
-8404
```

Потому что:

```text
u = 10245
v = -8404
```

Меньшее из этих двух чисел — `-8404`.

</details>

---

## Verification

Check the equation:

```text
26513 × 10245 + 32321 × (-8404)
```

Compute:

```text
26513 × 10245 = 271825685
32321 × (-8404) = -271825684
```

Then:

```text
271825685 - 271825684 = 1
```

So:

```text
26513 × 10245 + 32321 × (-8404) = 1
```

That matches:

```text
gcd(26513, 32321) = 1
```

---

# 6. Extended GCD by Table

Extended GCD can also be tracked with a table.

For every step, we write each remainder as:

```text
remainder = p × coefficient_for_p + q × coefficient_for_q
```

Initial values:

| Value | As combination of `p` and `q` |
|---:|---|
| `p = 26513` | `1 × p + 0 × q` |
| `q = 32321` | `0 × p + 1 × q` |

The algorithm then keeps updating these coefficients until the remainder becomes `1`.

Final useful row:

| GCD | Coefficient of `p` | Coefficient of `q` |
|---:|---:|---:|
| 1 | 10245 | -8404 |

So:

```text
1 = 26513 × 10245 + 32321 × (-8404)
```

---

# 7. Difference Between GCD and Extended GCD

| Algorithm | Finds | Example Output |
|---|---|---|
| Euclidean Algorithm | only `gcd(a,b)` | `1512` |
| Extended Euclidean Algorithm | `gcd(a,b)` and coefficients | `gcd=1, u=10245, v=-8404` |

Normal GCD answers:

```text
What is the greatest common divisor?
```

Extended GCD answers:

```text
How can this GCD be built from the original numbers?
```

---

# 8. Connection to Modular Inverse

If:

```text
a × x + n × y = 1
```

then:

```text
a × x ≡ 1 mod n
```

So `x` is the modular inverse of `a` modulo `n`.

That means Extended GCD can solve:

```text
a^(-1) mod n
```

Example:

```text
15 × 7 = 105
105 mod 26 = 1
```

So:

```text
15^(-1) mod 26 = 7
```

---

# 9. Why Extended GCD Works

Euclidean Algorithm repeatedly creates remainders:

```text
a = q × b + r
```

which can be rewritten as:

```text
r = a - q × b
```

So every new remainder is made from previous values.

Extended GCD simply keeps track of how each remainder was made.

Eventually, when the remainder becomes the GCD, we also know how to express that GCD using the original numbers.

---

# 10. Typical Mistakes

## Mistake 1: Using `/` instead of `//`

Wrong:

```python
a / b
```

Correct:

```python
a // b
```

Why?

`/` gives a floating-point number.

`//` gives integer division.

Extended GCD needs integer quotients.

---

## Mistake 2: Returning coefficients in the wrong order

This line matters:

```python
x = y1
y = x1 - (a // b) * y1
```

If you swap `x` and `y`, your final equation may not verify.

Always check:

```python
a * x + b * y == gcd
```

---

## Mistake 3: Forgetting negative coefficients are normal

In Extended GCD, one coefficient is often negative.

This is not an error.

Example:

```text
26513 × 10245 + 32321 × (-8404) = 1
```

The negative coefficient is required to cancel the larger positive value.

---

## Mistake 4: Thinking prime numbers always have GCD equal to themselves

For two different primes:

```text
gcd(p, q) = 1
```

Only if the numbers are the same:

```text
gcd(p, p) = p
```

---

## Mistake 5: Confusing GCD with LCM

| Concept | Meaning |
|---|---|
| GCD | greatest common divisor |
| LCM | least common multiple |

They are related, but not the same.

---

# 11. Pattern Summary

| Task Type | What to Look For | Tool |
|---|---|---|
| Find greatest divisor | `gcd(a,b)` | Euclidean Algorithm |
| Check coprime | `gcd(a,b)==1` | GCD |
| Find `u,v` | `au+bv=gcd(a,b)` | Extended GCD |
| Find modular inverse | `ax ≡ 1 mod n` | Extended GCD |
| RSA private exponent | `ed ≡ 1 mod φ(n)` | Extended GCD |

---

# 12. Quick Code Cheatsheet

## Built-in Python

```python
import math

print(math.gcd(66528, 52920))
```

---

## Manual GCD

```python
def gcd(a, b):
    while b:
        a, b = b, a % b
    return a
```

---

## Extended GCD

```python
def extended_gcd(a, b):
    if b == 0:
        return a, 1, 0

    g, x1, y1 = extended_gcd(b, a % b)

    x = y1
    y = x1 - (a // b) * y1

    return g, x, y
```

---

## Modular Inverse with Python

```python
inverse = pow(a, -1, n)
```

This works only if:

```text
gcd(a, n) = 1
```

---
