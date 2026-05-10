## 0. Big Idea: What XOR Is

**XOR** means **exclusive OR**.

It is a bitwise operation that compares two bits:

| A | B | A XOR B |
|---|---|---------|
| 0 | 0 | 0 |
| 0 | 1 | 1 |
| 1 | 0 | 1 |
| 1 | 1 | 0 |

Main rule:

> XOR returns `1` when the bits are different, and `0` when the bits are the same.

In math notation XOR is often written as:

```text
A ⊕ B
```

In Python and many programming languages it is written as:

```python
A ^ B
```

Example:

```text
0110
1010
---- XOR
1100
```

Why?

| Bit 1 | Bit 2 | Result |
|-------|-------|--------|
| 0 | 1 | 1 |
| 1 | 0 | 1 |
| 1 | 1 | 0 |
| 0 | 0 | 0 |

---

## 1. Why XOR Is Important in Cryptography

XOR is extremely common in cryptography because it is:

1. **Fast** — computers can XOR bits very efficiently.
2. **Reversible** — applying the same XOR again restores the original data.
3. **Simple** — encryption and decryption can use the same operation.
4. **Byte-friendly** — it works naturally with binary data.

The most important identity is:

```text
A ⊕ K ⊕ K = A
```

Because:

```text
K ⊕ K = 0
A ⊕ 0 = A
```

So if a message is encrypted like this:

```text
ciphertext = plaintext ⊕ key
```

Then it can be decrypted using the same key:

```text
plaintext = ciphertext ⊕ key
```

This is why XOR is often used in stream ciphers, one-time pads, block cipher internals, masks, and many CTF crypto challenges.

---

# Challenge 1: XOR Starter

## Visible Summary

This challenge teaches the most basic use of XOR on text.

You are given a normal string and a number. For each character:

1. Convert the character to its integer code with `ord()`.
2. XOR that integer with `13`.
3. Convert the result back to a character with `chr()`.
4. Wrap the result inside `crypto{...}`.

---

<details>
<summary>Original English task</summary>

XOR is a bitwise operator which returns 0 if the bits are the same, and 1 otherwise. In textbooks the XOR operator is denoted by ⊕, but in most challenges and programming languages you will see the caret ^ used instead.

A B Output  
0 0 0  
0 1 1  
1 0 1  
1 1 0  

For longer binary numbers we XOR bit by bit: 0110 ^ 1010 = 1100. We can XOR integers by first converting the integer from decimal to binary. We can XOR strings by first converting each character to the integer representing the Unicode character.

Given the string label, XOR each character with the integer 13. Convert these integers back to a string and submit the flag as crypto{new_string}.

The Python pwntools library has a convenient xor() function that can XOR together data of different types and lengths. But first, you may want to implement your own function to solve this.

</details>

<details>
<summary>Русский перевод задания</summary>

XOR — это побитовый оператор, который возвращает `0`, если биты одинаковые, и `1`, если они разные. В учебниках XOR обозначают как `⊕`, но в большинстве задач и языков программирования используется символ `^`.

Для длинных бинарных чисел XOR применяется побитово: `0110 ^ 1010 = 1100`. Целые числа можно XOR-ить, предварительно рассматривая их в бинарном виде. Строки можно XOR-ить, если сначала преобразовать каждый символ в число, соответствующее его Unicode-коду.

Дана строка `label`. Нужно применить XOR к каждому символу с числом `13`, затем преобразовать полученные числа обратно в строку и отправить флаг в формате `crypto{new_string}`.

В библиотеке Python `pwntools` есть удобная функция `xor()`, но для начала полезно реализовать решение самостоятельно.

</details>

---

## How It Works

Characters are stored as numbers.

For example:

| Character | `ord(character)` |
|-----------|------------------|
| `l` | 108 |
| `a` | 97 |
| `b` | 98 |
| `e` | 101 |
| `l` | 108 |

When we XOR each character code with `13`, we get new character codes.

Example for the first letter:

```python
ord('l') ^ 13
```

Then convert back:

```python
chr(ord('l') ^ 13)
```

For the full string:

```python
text = "label"
result = "".join(chr(ord(c) ^ 13) for c in text)
flag = f"crypto{{{result}}}"
print(flag)
```

---

## Why This Works

A string is not magically encrypted as text. Internally, every character has a numeric code.

So this process:

```text
character → number → XOR → new number → new character
```

is just a transformation of character codes.

The important part is that XOR is reversible:

```text
new_char_code = old_char_code ⊕ 13
old_char_code = new_char_code ⊕ 13
```

So the same operation can hide and recover data.

---

## Solution Pattern

```python
text = "label"
key = 13

new_string = "".join(chr(ord(char) ^ key) for char in text)
print(f"crypto{{{new_string}}}")
```

Pattern:

```text
for each character:
    number = ord(character)
    transformed = number XOR key
    output_character = chr(transformed)
```

---

# Challenge 2: XOR Properties

## Visible Summary

This challenge teaches why XOR chains can be simplified.

You are given several XOR relationships between keys and a final encrypted flag. Using XOR properties, you can cancel repeated values and isolate the flag.

The key idea:

```text
A ⊕ A = 0
A ⊕ 0 = A
```

So duplicate XOR terms disappear.

---

<details>
<summary>Original English task</summary>

In the last challenge, you saw how XOR worked at the level of bits. In this one, we're going to cover the properties of the XOR operation and then use them to undo a chain of operations that have encrypted a flag. Gaining an intuition for how this works will help greatly when you come to attacking real cryptosystems later, especially in the block ciphers category.

There are four main properties we should consider when we solve challenges using the XOR operator

Commutative: A ⊕ B = B ⊕ A  
Associative: A ⊕ (B ⊕ C) = (A ⊕ B) ⊕ C  
Identity: A ⊕ 0 = A  
Self-Inverse: A ⊕ A = 0

Let's break this down. Commutative means that the order of the XOR operations is not important. Associative means that a chain of operations can be carried out without order (we do not need to worry about brackets). The identity is 0, so XOR with 0 "does nothing", and lastly something XOR'd with itself returns zero.

Let's put this into practice! Below is a series of outputs where three random keys have been XOR'd together and with the flag. Use the above properties to undo the encryption in the final line to obtain the flag.

KEY1 = a6c8b6733c9b22de7bc0253266a3867df55acde8635e19c73313  
KEY2 ^ KEY1 = 37dcb292030faa90d07eec17e3b1c6d8daf94c35d4c9191a5e1e  
KEY2 ^ KEY3 = c1545756687e7573db23aa1c3452a098b71a7fbf0fddddde5fc1  
FLAG ^ KEY1 ^ KEY3 ^ KEY2 = 04ee9855208a2cd59091d04767ae47963170d1660df7f56f5faf

Before you XOR these objects, be sure to decode from hex to bytes.

</details>

<details>
<summary>Русский перевод задания</summary>

В прошлой задаче было показано, как XOR работает на уровне битов. В этой задаче рассматриваются свойства операции XOR, а затем эти свойства используются, чтобы обратить цепочку операций, которой был зашифрован флаг.

Есть четыре основных свойства XOR:

Коммутативность: `A ⊕ B = B ⊕ A`  
Ассоциативность: `A ⊕ (B ⊕ C) = (A ⊕ B) ⊕ C`  
Нейтральный элемент: `A ⊕ 0 = A`  
Самообратимость: `A ⊕ A = 0`

Коммутативность означает, что порядок XOR-операций не важен. Ассоциативность означает, что цепочку XOR можно выполнять без учёта скобок. XOR с нулём ничего не меняет. А XOR значения с самим собой даёт ноль.

Ниже даны результаты, где три случайных ключа были XOR-нуты между собой и с флагом. Используйте свойства XOR, чтобы обратить шифрование и получить флаг.

Перед XOR нужно декодировать hex-строки в bytes.

</details>

---

## Important XOR Properties

| Property | Formula | Meaning |
|----------|---------|---------|
| Commutative | `A ⊕ B = B ⊕ A` | Order does not matter |
| Associative | `A ⊕ (B ⊕ C) = (A ⊕ B) ⊕ C` | Brackets do not matter |
| Identity | `A ⊕ 0 = A` | XOR with zero changes nothing |
| Self-inverse | `A ⊕ A = 0` | A value cancels itself |

These properties make XOR behave like algebra.

For example:

```text
FLAG ⊕ KEY1 ⊕ KEY2 ⊕ KEY3 ⊕ KEY1 ⊕ KEY2 ⊕ KEY3
```

Can be rearranged:

```text
FLAG ⊕ KEY1 ⊕ KEY1 ⊕ KEY2 ⊕ KEY2 ⊕ KEY3 ⊕ KEY3
```

Then duplicates cancel:

```text
FLAG ⊕ 0 ⊕ 0 ⊕ 0 = FLAG
```

---

## How to Solve the Given Chain

Given:

```text
KEY1
KEY2 ⊕ KEY1
KEY2 ⊕ KEY3
FLAG ⊕ KEY1 ⊕ KEY3 ⊕ KEY2
```

We want:

```text
FLAG
```

Let:

```text
C = FLAG ⊕ KEY1 ⊕ KEY3 ⊕ KEY2
```

If we XOR `C` with:

```text
KEY1 ⊕ KEY2 ⊕ KEY3
```

then all keys cancel:

```text
C ⊕ KEY1 ⊕ KEY2 ⊕ KEY3
= FLAG ⊕ KEY1 ⊕ KEY3 ⊕ KEY2 ⊕ KEY1 ⊕ KEY2 ⊕ KEY3
= FLAG
```

But we are not directly given `KEY2` and `KEY3`. We can derive them.

### Step 1: Recover KEY2

Given:

```text
KEY2 ⊕ KEY1
```

XOR both sides with `KEY1`:

```text
(KEY2 ⊕ KEY1) ⊕ KEY1 = KEY2
```

Because:

```text
KEY1 ⊕ KEY1 = 0
KEY2 ⊕ 0 = KEY2
```

So:

```text
KEY2 = (KEY2 ⊕ KEY1) ⊕ KEY1
```

### Step 2: Recover KEY3

Given:

```text
KEY2 ⊕ KEY3
```

XOR both sides with `KEY2`:

```text
(KEY2 ⊕ KEY3) ⊕ KEY2 = KEY3
```

So:

```text
KEY3 = (KEY2 ⊕ KEY3) ⊕ KEY2
```

### Step 3: Recover FLAG

Given:

```text
FLAG ⊕ KEY1 ⊕ KEY3 ⊕ KEY2
```

XOR with `KEY1`, `KEY2`, and `KEY3`:

```text
FLAG = encrypted_flag ⊕ KEY1 ⊕ KEY2 ⊕ KEY3
```

---

## Python Solution Pattern

```python
from binascii import unhexlify

KEY1 = bytes.fromhex("a6c8b6733c9b22de7bc0253266a3867df55acde8635e19c73313")
KEY2_XOR_KEY1 = bytes.fromhex("37dcb292030faa90d07eec17e3b1c6d8daf94c35d4c9191a5e1e")
KEY2_XOR_KEY3 = bytes.fromhex("c1545756687e7573db23aa1c3452a098b71a7fbf0fddddde5fc1")
FLAG_XOR_KEYS = bytes.fromhex("04ee9855208a2cd59091d04767ae47963170d1660df7f56f5faf")

def xor_bytes(a, b):
    return bytes(x ^ y for x, y in zip(a, b))

KEY2 = xor_bytes(KEY2_XOR_KEY1, KEY1)
KEY3 = xor_bytes(KEY2_XOR_KEY3, KEY2)
FLAG = xor_bytes(xor_bytes(xor_bytes(FLAG_XOR_KEYS, KEY1), KEY2), KEY3)

print(FLAG.decode())
```

Alternative with a helper that accepts many byte strings:

```python
def xor_many(*items):
    result = items[0]
    for item in items[1:]:
        result = bytes(a ^ b for a, b in zip(result, item))
    return result

KEY2 = xor_many(KEY2_XOR_KEY1, KEY1)
KEY3 = xor_many(KEY2_XOR_KEY3, KEY2)
FLAG = xor_many(FLAG_XOR_KEYS, KEY1, KEY2, KEY3)

print(FLAG.decode())
```

---

## Why Hex Must Be Decoded First

The values are written as hex strings:

```text
a6c8b673...
```

This is not the raw byte data yet. It is a textual representation of bytes.

Wrong idea:

```python
b"a6c8b673"
```

This gives ASCII bytes for the characters `a`, `6`, `c`, `8`, etc.

Correct idea:

```python
bytes.fromhex("a6c8b673")
```

This gives the actual bytes:

```text
0xa6 0xc8 0xb6 0x73
```

For XOR crypto tasks, always ask:

> Am I XOR-ing the real bytes or just the text representation?

---

# Challenge 3: Favourite Byte

## Visible Summary

This challenge introduces a classic single-byte XOR brute force.

You are given hex-encoded ciphertext. It was encrypted by XOR-ing every byte with the same unknown byte.

Since one byte has only 256 possible values, we can try all keys from `0` to `255` and look for readable English or the expected flag format.

---

<details>
<summary>Original English task</summary>

For the next few challenges, you'll use what you've just learned to solve some more XOR puzzles.

I've hidden some data using XOR with a single byte, but that byte is a secret. Don't forget to decode from hex first.

73626960647f6b206821204f21254f7d694f7624662065622127234f726927756d

</details>

<details>
<summary>Русский перевод задания</summary>

В следующих задачах нужно использовать знания о XOR, чтобы решить ещё несколько XOR-головоломок.

Некоторые данные скрыты с помощью XOR с одним байтом, но этот байт является секретным. Не забудьте сначала декодировать данные из hex.

73626960647f6b206821204f21254f7d694f7624662065622127234f726927756d

</details>

---

## How Single-Byte XOR Works

Encryption:

```text
cipher_byte = plain_byte ⊕ key_byte
```

Decryption uses the same operation:

```text
plain_byte = cipher_byte ⊕ key_byte
```

If the same single byte is used for the whole message:

```text
P1 ⊕ K = C1
P2 ⊕ K = C2
P3 ⊕ K = C3
...
```

Then brute force is easy because:

```text
possible keys = 256
```

That is tiny for a computer.

---

## Brute Force Strategy

1. Decode the hex string into bytes.
2. Try every key from `0` to `255`.
3. XOR every byte with that key.
4. Decode the result as text if possible.
5. Look for readable English or `crypto{...}`.

---

## Python Solution Pattern

```python
ciphertext = bytes.fromhex(
    "73626960647f6b206821204f21254f7d694f7624662065622127234f726927756d"
)

for key in range(256):
    plaintext = bytes(byte ^ key for byte in ciphertext)

    try:
        text = plaintext.decode()
    except UnicodeDecodeError:
        continue

    if "crypto{" in text or text.isprintable():
        print(key, text)
```

A stricter version:

```python
ciphertext = bytes.fromhex(
    "73626960647f6b206821204f21254f7d694f7624662065622127234f726927756d"
)

for key in range(256):
    plaintext = bytes(c ^ key for c in ciphertext)
    if plaintext.startswith(b"crypto{"):
        print(plaintext.decode())
        break
```

---

## Why Brute Force Works Here

A single byte has 8 bits.

So the number of possible keys is:

```text
2^8 = 256
```

Trying 256 keys is trivial.

This would not work the same way if the key were very long and random.

Comparison:

| Key type | Number of possibilities | Easy to brute force? |
|----------|--------------------------|----------------------|
| 1 byte | 256 | Yes |
| 2 bytes | 65,536 | Usually yes |
| 4 bytes | 4,294,967,296 | Harder |
| 16 bytes | 2^128 | No in practice |
| 32 bytes | 2^256 | No in practice |

---

# General XOR Patterns

## Pattern 1: Character XOR

Used when the input is a normal string.

```python
result = "".join(chr(ord(c) ^ key) for c in text)
```

Mental model:

```text
text character → integer → XOR → integer → text character
```

---

## Pattern 2: Byte XOR

Used when working with binary data.

```python
def xor_bytes(a, b):
    return bytes(x ^ y for x, y in zip(a, b))
```

Mental model:

```text
byte sequence A
byte sequence B
XOR byte by byte
```

---

## Pattern 3: Hex → Bytes → XOR

Very common in CryptoHack and CTFs.

```python
ciphertext = bytes.fromhex(hex_string)
```

Then XOR the bytes.

Important distinction:

| Representation | Meaning |
|----------------|---------|
| `"41"` | two text characters: `4` and `1` |
| `bytes.fromhex("41")` | one byte: `0x41`, which is `A` |

---

## Pattern 4: XOR Cancellation

Used when a challenge gives relationships like:

```text
A ⊕ B
B ⊕ C
FLAG ⊕ A ⊕ C
```

You can rearrange and cancel terms because XOR is commutative, associative, and self-inverse.

Core cancellation:

```text
X ⊕ X = 0
X ⊕ 0 = X
```

---

## Pattern 5: Single-Byte XOR Brute Force

Used when every byte was XOR-ed with the same unknown byte.

```python
for key in range(256):
    candidate = bytes(c ^ key for c in ciphertext)
```

Then filter by:

- readable ASCII;
- English-looking text;
- known prefix like `crypto{`;
- common words;
- expected file header.

---

# Useful Tables

## ASCII / Byte / Hex Example

| Character | Decimal | Binary | Hex |
|-----------|---------|--------|-----|
| `A` | 65 | `01000001` | `0x41` |
| `B` | 66 | `01000010` | `0x42` |
| `a` | 97 | `01100001` | `0x61` |
| `0` | 48 | `00110000` | `0x30` |
| `{` | 123 | `01111011` | `0x7b` |

---

## XOR With Itself

| Value | XOR Same Value | Result |
|-------|----------------|--------|
| `A` | `A ⊕ A` | `0` |
| `KEY` | `KEY ⊕ KEY` | `0` |
| `10101010` | `10101010 ⊕ 10101010` | `00000000` |

---

## XOR With Zero

| Value | Operation | Result |
|-------|-----------|--------|
| `A` | `A ⊕ 0` | `A` |
| `KEY` | `KEY ⊕ 0` | `KEY` |
| `10101010` | `10101010 ⊕ 00000000` | `10101010` |

---

# Common Mistakes

## Mistake 1: XOR-ing hex strings directly

Wrong:

```python
"a6" ^ "c8"
```

Strings cannot be XOR-ed directly.

Correct:

```python
bytes.fromhex("a6")
```

---

## Mistake 2: Forgetting that `^` is XOR, not exponentiation

In Python:

```python
2 ^ 3
```

means XOR, not power.

Power is:

```python
2 ** 3
```

---

## Mistake 3: Using `ord()` on bytes

For strings:

```python
ord("A")
```

For bytes, values are already integers when iterated:

```python
for b in b"ABC":
    print(b)
```

Output:

```text
65
66
67
```

So this is enough:

```python
bytes(b ^ key for b in data)
```

---

# Final Summary

XOR challenges are based on one central idea:

```text
XOR is reversible because A ⊕ A = 0.
```

The main solving patterns are:

| Challenge type | Main technique |
|----------------|----------------|
| XOR Starter | Convert chars with `ord()`, XOR, convert back with `chr()` |
| XOR Properties | Use algebraic cancellation of repeated XOR terms |
| Favourite Byte | Brute force all 256 possible single-byte keys |

The most important workflow:

```text
encoded input → decode to bytes → apply XOR logic → decode result as text
```

Whenever you see XOR in crypto tasks, ask:

1. What format is the input in?
2. Do I need to decode from hex/base64 first?
3. Is the key known or unknown?
4. Is the key one byte, repeated, or full-length?
5. Can XOR properties cancel something?
6. Is brute force possible?

