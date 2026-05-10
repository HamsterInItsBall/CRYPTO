# GCD and Extended GCD — CryptoHack Notes

# Greatest Common Divisor (GCD)

## Main Idea

The Greatest Common Divisor (GCD) is the largest number that divides two integers without remainder.

The entire idea behind GCD:
- repeatedly reduce the problem;
- use remainders;
- eventually reach zero;
- the last non-zero remainder is the answer.

This is the foundation of:
- RSA;
- modular arithmetic;
- modular inverse;
- Bézout identity;
- finite field arithmetic.

---

## Challenge

<details>
<summary>Original task (EN)</summary>

Greatest Common Divisor

The Greatest Common Divisor (GCD), sometimes known as the highest common factor, is the largest number which divides two positive integers (a,b).

For a=12,b=8 we can calculate the divisors of a: {1,2,3,4,6,12} and the divisors of b: {1,2,4,8}. Comparing these two, we see that gcd(a,b)=4.

Now calculate gcd(a,b) for a=66528,b=52920.

</details>

<details>
<summary>Русский перевод</summary>

Наибольший общий делитель (НОД) — это наибольшее число, которое делит два числа без остатка.

Найдите gcd(a,b) для:
- a = 66528
- b = 52920

</details>

---

# Solving the Challenge

We use Euclid's Algorithm:

\[
gcd(a,b)=gcd(b,a \bmod b)
\]

Continue until remainder becomes zero.

---

## Step-by-step

\[
66528 \mod 52920 = 13608
\]

\[
52920 \mod 13608 = 12096
\]

\[
13608 \mod 12096 = 1512
\]

\[
12096 \mod 1512 = 0
\]

Last non-zero remainder:

\[
gcd = 1512
\]

---

## Python Solution

```python
def gcd(a, b):
    while b != 0:
        a, b = b, a % b
    return a

print(gcd(66528, 52920))
```

---

## Why This Works

If a number divides both:
- a
- b

then it also divides:

\[
a - qb
\]

which is exactly the remainder operation.

This means:
- we can safely replace numbers;
- without changing the common divisor.

That is why the algorithm works.

---

# Visual Representation

```text
66528
 └── 52920 remainder 13608
        └── 13608 remainder 12096
               └── 12096 remainder 1512
                      └── 1512 remainder 0

Answer = 1512
```

---

# Common Mistakes

## 1. Swapping modulo order incorrectly

Wrong:

```python
a % b
b % a
```

without updating variables correctly.

Correct:

```python
a, b = b, a % b
```

---

## 2. Forgetting termination condition

Wrong:

```python
while a != 0
```

Usually easier:

```python
while b != 0
```

---

## 3. Confusing division with modulo

Modulo gives remainder, not quotient.

---

# Extended GCD

## Main Idea

Extended Euclidean Algorithm not only finds:

\[
gcd(a,b)
\]

but also integers:

\[
u,v
\]

such that:

\[
au+bv=gcd(a,b)
\]

This is called Bézout's Identity.

---

## Challenge

<details>
<summary>Original task (EN)</summary>

Using the two primes p=26513,q=32321, find the integers u,v such that

\[
p\cdot u + q\cdot v = gcd(p,q)
\]

Enter whichever of u and v is the lower number as the flag.

</details>

<details>
<summary>Русский перевод</summary>

Используя:
- p = 26513
- q = 32321

найдите числа u и v такие, что:

\[
p\cdot u + q\cdot v = gcd(p,q)
\]

Введите меньшее число.

</details>

---

# Important Observation

Both numbers are prime.

Therefore:

\[
gcd(p,q)=1
\]

This is extremely important in cryptography.

RSA depends on coprime numbers.

---

# Solving

We use Extended Euclidean Algorithm.

---

## Python Code

```python
def extended_gcd(a, b):
    if b == 0:
        return a, 1, 0

    gcd, x1, y1 = extended_gcd(b, a % b)

    x = y1
    y = x1 - (a // b) * y1

    return gcd, x, y


g, u, v = extended_gcd(26513, 32321)

print(g)
print(u, v)
```

---

## Result

```text
u = 10245
v = -8404
```

Smaller value:

\[
-8404
\]

---

<details>
<summary>Русский вывод</summary>

Ответ:

\[
-8404
\]

</details>

---

# Why Extended GCD Works

Normal Euclidean Algorithm:

```text
a = qb + r
```

replaces numbers.

Extended GCD tracks:
- how every remainder was built;
- from previous values.

Eventually:

\[
1 = au+bv
\]

which gives coefficients directly.

---

# Core Cryptography Importance

Extended GCD is used for:

| Topic | Why |
|---|---|
| RSA | Private key generation |
| Modular inverse | Needed for division mod n |
| ECC | Finite field operations |
| CRT | Chinese Remainder Theorem |

---

# Modular Inverse

If:

\[
ax \equiv 1 \pmod n
\]

then:

\[
x=a^{-1}\pmod n
\]

Extended GCD finds this inverse.

---

# Example

Find inverse of:

\[
15^{-1}\pmod{26}
\]

We solve:

\[
15x+26y=1
\]

Result:

\[
x=7
\]

because:

\[
15\cdot7=105\equiv1\pmod{26}
\]

---

# Full Algorithm Intuition

## Euclidean Algorithm

```text
gcd(a,b)
↓
gcd(b,a mod b)
↓
gcd(r1,r2)
↓
...
↓
0
```

---

## Extended GCD

Tracks:

```text
remainder = ax + by
```

for every step.

---

# Typical Errors

## 1. Returning coefficients in wrong order

Very common mistake.

---

## 2. Forgetting floor division

Use:

```python
a // b
```

not:

```python
a / b
```

---

## 3. Wrong recursive update

Correct:

```python
x = y1
y = x1 - (a // b) * y1
```

---

## 4. Confusing inverse with division

Modular inverse is NOT normal division.

---

# Pattern Recognition

| Problem Type | Method |
|---|---|
| gcd(a,b) | Euclidean Algorithm |
| ax+by=gcd(a,b) | Extended GCD |
| inverse mod n | Extended GCD |
| RSA key generation | Extended GCD |
| coprime check | gcd(a,b)==1 |

---

# Mental Model

GCD:
- removes unnecessary parts;
- compresses numbers.

Extended GCD:
- reconstructs how the gcd was created.

---

# Quick Cheatsheet

```python
# GCD
while b:
    a, b = b, a % b

# Extended GCD
gcd, x, y = extended_gcd(a, b)

# Modular inverse
pow(a, -1, mod)
```
