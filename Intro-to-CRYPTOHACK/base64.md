## 1. Base64

### Main idea

Base64 is a way to represent raw binary data as a printable ASCII string.

It is not encryption. It does not hide information. It only changes the representation of bytes so they can be safely copied, transmitted, embedded into text formats, or included in places where raw bytes may break formatting.

Typical flow in this task:

```text
hex string → bytes → Base64 string
```

### Why it works

Computers store data as bytes. A byte contains 8 bits. Base64 groups binary data into chunks of 6 bits. Since 6 bits can represent `2^6 = 64` possible values, Base64 uses an alphabet of 64 characters.

Because 3 bytes contain 24 bits, and 4 Base64 characters also contain 24 bits, Base64 naturally converts:

```text
3 bytes = 24 bits = 4 Base64 characters
```

If the input length is not divisible by 3, Base64 uses padding with `=`.

### How to solve

1. Take the given hex string.
2. Decode it into bytes using `bytes.fromhex()`.
3. Encode those bytes using `base64.b64encode()`.
4. Decode the result to a normal string if needed.

```python
import base64

hex_string = "72bca9b68fc16ac7beeb8f849dca1d8a783e8acf9679bf9269f7bf"
raw_bytes = bytes.fromhex(hex_string)
encoded = base64.b64encode(raw_bytes)

print(encoded.decode())
```

### Pattern

This is a representation-conversion task.

You are not trying to break anything cryptographic. You are moving data between encodings:

```text
hexadecimal text → raw bytes → Base64 text
```

### What to remember

- Hex represents bytes using base-16 symbols.
- Base64 represents bytes using 64 printable characters.
- Base64 is encoding, not encryption.
- Always convert hex into bytes before Base64 encoding.

<details>
<summary>Original task text — English</summary>

Base64

Another common encoding scheme is Base64, which allows us to represent binary data as an ASCII string using an alphabet of 64 characters. One character of a Base64 string encodes 6 binary digits (bits), and so 4 characters of Base64 encode three 8-bit bytes.

Base64 is most commonly used online, so binary data such as images can be easily included into HTML or CSS files.

Take the below hex string, decode it into bytes and then encode it into Base64.

```text
72bca9b68fc16ac7beeb8f849dca1d8a783e8acf9679bf9269f7bf
```

In Python, after importing the base64 module with `import base64`, you can use the `base64.b64encode()` function. Remember to decode the hex first as the challenge description states.

</details>

<details>
<summary>Перевод задания — Русский</summary>

Base64

Ещё одна распространённая схема кодирования — Base64. Она позволяет представлять бинарные данные в виде ASCII-строки, используя алфавит из 64 символов. Один символ Base64 кодирует 6 бинарных цифр, то есть битов. Поэтому 4 символа Base64 кодируют три 8-битных байта.

Base64 чаще всего используется в интернете, чтобы бинарные данные, например изображения, можно было удобно включать в HTML или CSS файлы.

Возьми hex-строку ниже, сначала декодируй её в байты, а затем закодируй эти байты в Base64.

```text
72bca9b68fc16ac7beeb8f849dca1d8a783e8acf9679bf9269f7bf
```

В Python после импорта модуля `base64` с помощью `import base64` можно использовать функцию `base64.b64encode()`. Не забудь сначала декодировать hex, как указано в описании задания.

</details>

---

## 2. Bytes and Big Integers

### Main idea

Many cryptographic systems work with numbers, but human messages are usually text.

So we need a reliable way to convert between:

```text
message text ↔ bytes ↔ big integer
```

This is especially important in RSA, where encryption and decryption are mathematical operations on large integers.

### Why it works

Text can be converted to bytes using an encoding such as ASCII or UTF-8.

Each byte is a number from 0 to 255. If we write all bytes as hexadecimal and concatenate them, we get one large hexadecimal number. That number can also be written in decimal.

Example:

```text
message: HELLO
bytes:   72 69 76 76 79
hex:     48 45 4c 4c 4f
integer: 0x48454c4c4f
```

So the text is not lost. It is just represented as one large number.

### How to solve

The task gives a large decimal integer and asks us to convert it back into a message.

There are two common approaches.

#### Approach A — using PyCryptodome

```python
from Crypto.Util.number import long_to_bytes

n = 11515195063862318899931685488813747395775516287289682636499965282714637259206269
message = long_to_bytes(n)

print(message.decode())
```

#### Approach B — using only standard Python

```python
n = 11515195063862318899931685488813747395775516287289682636499965282714637259206269

hex_string = hex(n)[2:]
if len(hex_string) % 2 != 0:
    hex_string = "0" + hex_string

message = bytes.fromhex(hex_string)
print(message.decode())
```

### Why the standard Python method works

`hex(n)` converts the decimal integer into hexadecimal.

Then `bytes.fromhex()` interprets every two hex characters as one byte.

For example:

```text
48 45 4c 4c 4f → HELLO
```

Each pair of hex digits is one byte because:

```text
1 byte = 8 bits
1 hex digit = 4 bits
2 hex digits = 8 bits
```

### Pattern

This is a numeric representation task.

The important pattern is:

```text
integer → hex → bytes → text
```

or with PyCryptodome:

```text
integer → long_to_bytes() → text
```

### What to remember

- Cryptographic algorithms often operate on integers.
- Messages must be converted into numbers before mathematical operations.
- `bytes_to_long()` converts bytes into a big integer.
- `long_to_bytes()` converts a big integer back into bytes.
- Hex is a convenient bridge between bytes and integers.

<details>
<summary>Original task text — English</summary>

Bytes and Big Integers

Cryptosystems like RSA works on numbers, but messages are made up of characters. How should we convert our messages into numbers so that mathematical operations can be applied?

The most common way is to take the ordinal bytes of the message, convert them into hexadecimal, and concatenate. This can be interpreted as a base-16/hexadecimal number, and also represented in base-10/decimal.

To illustrate:

```text
message: HELLO
ascii bytes: [72, 69, 76, 76, 79]
hex bytes: [0x48, 0x45, 0x4c, 0x4c, 0x4f]
base-16: 0x48454c4c4f
base-10: 310400273487
```

Python's PyCryptodome library implements this with the methods `bytes_to_long()` and `long_to_bytes()`. You will first have to install PyCryptodome and import it with `from Crypto.Util.number import *`. For more details check the FAQ.

Convert the following integer back into a message:

```text
11515195063862318899931685488813747395775516287289682636499965282714637259206269
```

</details>

<details>
<summary>Перевод задания — Русский</summary>

Bytes and Big Integers

Криптосистемы вроде RSA работают с числами, но сообщения состоят из символов. Как преобразовать сообщения в числа, чтобы над ними можно было выполнять математические операции?

Самый распространённый способ — взять байты сообщения, соответствующие символам, преобразовать их в hexadecimal, то есть шестнадцатеричный вид, и соединить вместе. Получившуюся строку можно интерпретировать как число в base-16, то есть в шестнадцатеричной системе, а также представить в base-10, то есть в десятичной системе.

Пример:

```text
message: HELLO
ascii bytes: [72, 69, 76, 76, 79]
hex bytes: [0x48, 0x45, 0x4c, 0x4c, 0x4f]
base-16: 0x48454c4c4f
base-10: 310400273487
```

Библиотека PyCryptodome в Python реализует это с помощью методов `bytes_to_long()` и `long_to_bytes()`. Сначала нужно установить PyCryptodome и импортировать нужные функции через `from Crypto.Util.number import *`.

Преобразуй следующее число обратно в сообщение:

```text
11515195063862318899931685488813747395775516287289682636499965282714637259206269
```

</details>

---

## 3. Summary of bases and encodings

### Base vs encoding

A **base** is a numeral system. It describes how numbers are written.

An **encoding** is a method for representing data in another form.

For example:

- Base-10 writes numbers using digits `0-9`.
- Base-16 writes numbers using `0-9` and `a-f`.
- Base64 encodes bytes into printable text.

Base64 has “64” in the name, but it is usually discussed as an encoding scheme, not just as a normal numeral system.

---

## 4. Important bases

### Base-2 — Binary

Uses two symbols:

```text
0 1
```

Computers fundamentally operate on binary data.

Example:

```text
01000001 = 65 = 'A'
```

Important because:

- bits are binary;
- cryptography works with bitwise operations;
- XOR, AND, OR operate at the binary level.

---

### Base-10 — Decimal

Uses ten symbols:

```text
0 1 2 3 4 5 6 7 8 9
```

This is the normal human number system.

Example:

```text
65
```

Important because:

- humans usually read integers in decimal;
- many challenge statements give big numbers in base-10;
- RSA values are often displayed as decimal integers.

---

### Base-16 — Hexadecimal

Uses sixteen symbols:

```text
0 1 2 3 4 5 6 7 8 9 a b c d e f
```

Example:

```text
41 = 65 = 'A'
```

Important because:

- one hex digit represents 4 bits;
- two hex digits represent one byte;
- very convenient for reading binary data.

Common Python tools:

```python
bytes.fromhex("414243")  # b'ABC'
b"ABC".hex()            # '414243'
hex(65)                  # '0x41'
```

---

### Base-32

Base32 encodes binary data using 32 symbols.

It is less compact than Base64 but often easier to handle in systems where case-insensitivity or limited character sets matter.

Typical alphabet:

```text
A-Z and 2-7
```

Example usage:

- one-time password systems;
- recovery codes;
- systems where avoiding lowercase/uppercase ambiguity is useful.

Python example:

```python
import base64

encoded = base64.b32encode(b"hello")
print(encoded)
```

---

### Base-58

Base58 is used in some cryptocurrency-related systems.

It avoids visually confusing characters such as:

```text
0 O I l
```

Important because it is more human-friendly for copying addresses or identifiers.

Common use cases:

- Bitcoin addresses;
- some blockchain identifiers;
- compact human-readable binary data.

---

### Base-64

Base64 uses 64 symbols to encode binary data as printable ASCII.

Common alphabet:

```text
A-Z a-z 0-9 + /
```

Often uses `=` for padding.

Important because:

- it is widely used on the web;
- it safely carries binary data through text-only systems;
- it appears in tokens, certificates, HTTP, HTML, CSS, email, and APIs.

Python example:

```python
import base64

encoded = base64.b64encode(b"hello")
decoded = base64.b64decode(encoded)

print(encoded)
print(decoded)
```

---

### Base-85 / ASCII85

Base85 is a denser encoding than Base64.

It represents binary data using more printable characters, so the output can be shorter than Base64.

Common use cases:

- Adobe/PostScript/PDF-related formats;
- compact binary-to-text encoding.

Python example:

```python
import base64

encoded = base64.b85encode(b"hello")
print(encoded)
```

---

## 5. Common solving patterns

### Pattern A — Hex to text

```text
hex → bytes → text
```

Python:

```python
bytes.fromhex(hex_string).decode()
```

Used when the challenge gives a long string containing only hex characters:

```text
0-9 and a-f
```

---

### Pattern B — Hex to Base64

```text
hex → bytes → Base64
```

Python:

```python
base64.b64encode(bytes.fromhex(hex_string)).decode()
```

Used when raw bytes must be represented in a web-safe text format.

---

### Pattern C — Integer to message

```text
integer → hex → bytes → text
```

Python:

```python
hex_string = hex(n)[2:]
if len(hex_string) % 2:
    hex_string = "0" + hex_string

message = bytes.fromhex(hex_string).decode()
```

Or:

```python
from Crypto.Util.number import long_to_bytes

message = long_to_bytes(n).decode()
```

---

### Pattern D — Message to integer

```text
text → bytes → hex → integer
```

Python:

```python
message = b"HELLO"
n = int(message.hex(), 16)
print(n)
```

Or:

```python
from Crypto.Util.number import bytes_to_long

n = bytes_to_long(b"HELLO")
print(n)
```

---

## 6. Big-picture summary

These tasks teach that cryptography often begins with representation.

Before encrypting, decrypting, attacking, or analyzing anything, you need to understand what form the data is currently in.

Ask yourself:

```text
Is this text?
Is this hex?
Is this Base64?
Is this raw bytes?
Is this a big integer?
```

Then choose the correct conversion path.

Most beginner CryptoHack tasks are not about advanced cryptography yet. They are about learning how to move confidently between representations.

```text
text ↔ bytes ↔ hex ↔ integer ↔ Base64
```

Once these conversions become natural, RSA, XOR, hashing, block ciphers, and many other topics become much easier to understand.
