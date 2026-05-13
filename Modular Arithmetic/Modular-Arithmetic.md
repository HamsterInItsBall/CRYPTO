# 1. Main Idea

Modular arithmetic is arithmetic with remainders.

Instead of asking:

```text
What is the full result?
```

we ask:

```text
What is the remainder after division by m?
```

This is written as:

```text
a ≡ b mod m
```

Meaning:

```text
a and b have the same remainder when divided by m
```

or more simply:

```text
a % m = b
```

---

# 2. Challenge

<details>
<summary>Original task (EN)</summary>

Modular Arithmetic 1

Imagine you lean over and look at a cryptographer's notebook. You see some notes in the margin:

```text
4 + 9 = 1
5 - 7 = 10
2 + 3 = 5
```

At first you might think they've gone mad. This is nothing more than modular arithmetic modulo 12.

Formally, "calculating time" is described by the theory of congruences. We say that two integers are congruent modulo `m` if:

```text
a ≡ b mod m
```

Another way of saying this is that when we divide the integer `a` by `m`, the remainder is `b`.

Calculate the following integers:

```text
11 ≡ x mod 6
8146798528947 ≡ y mod 17
```

The solution is the smaller of the two integers `(x, y)` obtained after reducing by the modulus.

</details>

<details>
<summary>Русский перевод задания</summary>

Нужно найти остатки:

```text
11 ≡ x mod 6
8146798528947 ≡ y mod 17
```

То есть:

```text
x = 11 % 6
y = 8146798528947 % 17
```

После этого нужно выбрать меньшее из двух чисел `x` и `y`.

</details>

---

# 3. Solving Without Script

## Part 1

We need:

```text
11 mod 6
```

Divide `11` by `6`:

```text
11 = 1 × 6 + 5
```

The remainder is:

```text
5
```

So:

```text
11 ≡ 5 mod 6
```

Therefore:

```text
x = 5
```

---

## Part 2

We need:

```text
8146798528947 mod 17
```

This means:

```text
What is the remainder when 8146798528947 is divided by 17?
```

Using integer division:

```text
8146798528947 = 479223442879 × 17 + 4
```

The remainder is:

```text
4
```

So:

```text
8146798528947 ≡ 4 mod 17
```

Therefore:

```text
y = 4
```

---

## Compare x and y

We found:

| Variable | Value |
|---|---:|
| `x` | 5 |
| `y` | 4 |

The smaller value is:

```text
4
```

<details>
<summary>Русский вывод</summary>

Ответ:

```text
4
```

Потому что:

```text
x = 5
y = 4
```

Меньшее число — `4`.

</details>

---

# 4. Solving With Python

## Minimal Script

```python
x = 11 % 6
y = 8146798528947 % 17

answer = min(x, y)

print("x =", x)
print("y =", y)
print("answer =", answer)
```

Output:

```text
x = 5
y = 4
answer = 4
```

---

## More Descriptive Script

```python
def reduce_modulo(number, modulus):
    """
    Return the remainder after dividing number by modulus.

    In math notation:
        number ≡ remainder mod modulus
    """
    return number % modulus


x = reduce_modulo(11, 6)
y = reduce_modulo(8146798528947, 17)

print(f"11 mod 6 = {x}")
print(f"8146798528947 mod 17 = {y}")
print(f"Smaller value = {min(x, y)}")
```

---

# 5. What `%` Means in Python

In Python:

```python
a % m
```

means:

```text
remainder after dividing a by m
```

Examples:

| Expression | Meaning | Result |
|---|---|---:|
| `11 % 6` | remainder after `11 / 6` | 5 |
| `10 % 3` | remainder after `10 / 3` | 1 |
| `12 % 12` | remainder after `12 / 12` | 0 |
| `17 % 5` | remainder after `17 / 5` | 2 |

---

# 6. Clock Analogy

Modular arithmetic is like a clock.

A normal clock works modulo 12.

For example:

```text
4 + 9 = 13
```

But on a 12-hour clock:

```text
13 becomes 1
```

So:

```text
4 + 9 ≡ 1 mod 12
```

Another example:

```text
5 - 7 = -2
```

On a 12-hour clock:

```text
-2 becomes 10
```

So:

```text
5 - 7 ≡ 10 mod 12
```

---

## Clock Table Modulo 12

| Normal number | Modulo 12 result |
|---:|---:|
| 12 | 0 |
| 13 | 1 |
| 14 | 2 |
| 15 | 3 |
| 16 | 4 |
| 17 | 5 |
| 18 | 6 |
| 19 | 7 |
| 20 | 8 |
| 21 | 9 |
| 22 | 10 |
| 23 | 11 |
| 24 | 0 |

---

# 7. Why Modular Arithmetic Works

Every integer can be written as:

```text
a = q × m + r
```

where:
- `a` is the original number;
- `m` is the modulus;
- `q` is the quotient;
- `r` is the remainder.

The remainder must satisfy:

```text
0 ≤ r < m
```

So when we say:

```text
a mod m = r
```

we mean:

```text
r is the remainder after dividing a by m
```

Example:

```text
11 = 1 × 6 + 5
```

So:

```text
11 mod 6 = 5
```

---

# 8. Congruence

The notation:

```text
a ≡ b mod m
```

means:

```text
a and b leave the same remainder when divided by m
```

For example:

```text
11 ≡ 5 mod 6
```

because:

```text
11 % 6 = 5
5 % 6 = 5
```

Another example:

```text
29 ≡ 5 mod 6
```

because:

```text
29 % 6 = 5
5 % 6 = 5
```

So all these numbers are equivalent modulo 6:

```text
..., -7, -1, 5, 11, 17, 23, 29, ...
```

They all leave remainder `5` when divided by `6`.

---

# 9. Important Pattern

Modular arithmetic compresses infinite integers into a fixed range.

For modulus `m`, all results are reduced into:

```text
0, 1, 2, ..., m-1
```

Examples:

| Modulus | Possible results |
|---:|---|
| `mod 2` | `0, 1` |
| `mod 5` | `0, 1, 2, 3, 4` |
| `mod 6` | `0, 1, 2, 3, 4, 5` |
| `mod 12` | `0, 1, 2, ..., 11` |
| `mod 17` | `0, 1, 2, ..., 16` |

So:

```text
8146798528947 mod 17
```

must be one of:

```text
0, 1, 2, ..., 16
```

In this challenge it is:

```text
4
```

---

# 10. Why This Matters in Cryptography

Modular arithmetic is one of the foundations of modern cryptography.

It appears in:

| Topic | Use |
|---|---|
| RSA | modular exponentiation |
| Diffie-Hellman | modular powers |
| ECC | finite fields |
| Hashing | fixed-size ranges |
| Block ciphers | operations over limited-size values |
| Number theory | congruences and inverses |

Cryptography often works with huge numbers.

Modulo arithmetic keeps those numbers inside a predictable finite range.

---

# 11. Manual Solving Method

To solve:

```text
a mod m
```

do this:

1. Divide `a` by `m`.
2. Ignore the decimal part.
3. Multiply the integer quotient by `m`.
4. Subtract from `a`.
5. The result is the remainder.

Example:

```text
11 mod 6
```

Step-by-step:

| Step | Operation | Result |
|---:|---|---:|
| 1 | `11 / 6` | `1 remainder something` |
| 2 | integer quotient | `1` |
| 3 | `1 × 6` | `6` |
| 4 | `11 - 6` | `5` |
| 5 | remainder | `5` |

---

# 12. Manual Method for the Large Number

We need:

```text
8146798528947 mod 17
```

A calculator or script is easiest, but conceptually it is the same:

| Step | Operation | Result |
|---:|---|---:|
| 1 | Integer quotient | `479223442879` |
| 2 | Multiply by modulus | `479223442879 × 17 = 8146798528943` |
| 3 | Subtract | `8146798528947 - 8146798528943 = 4` |
| 4 | Remainder | `4` |

So:

```text
8146798528947 mod 17 = 4
```

---

# 13. Typical Mistakes

## Mistake 1: Confusing quotient and remainder

For:

```text
11 / 6
```

the quotient is `1`, but the remainder is `5`.

Modulo asks for the remainder, not the quotient.

---

## Mistake 2: Forgetting that results are smaller than the modulus

For:

```text
a mod 17
```

the answer must be between:

```text
0 and 16
```

So if you get `17`, `20`, or `-1`, something is wrong or not fully reduced.

---

## Mistake 3: Thinking `a ≡ b mod m` means `a = b`

This is not normal equality.

It means:

```text
a and b are equal after reducing modulo m
```

Example:

```text
11 ≠ 5
```

but:

```text
11 ≡ 5 mod 6
```

---

## Mistake 4: Forgetting to choose the smaller answer

The challenge asks for the smaller of `x` and `y`.

Not both.

We found:

```text
x = 5
y = 4
```

So answer is:

```text
4
```

---

# 14. Quick Cheatsheet

```python
# Remainder
a % m

# Reduce number modulo m
reduced = number % modulus

# Compare two reduced values
answer = min(x, y)
```

Math notation:

```text
a ≡ b mod m
```

Programming notation:

```python
a % m == b
```

---
