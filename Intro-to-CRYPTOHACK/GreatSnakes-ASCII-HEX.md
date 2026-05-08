## 1. Great Snakes

### Core idea / Основная идея

**EN:** This challenge checks whether you can run and read a small Python 3 script. The script stores integer values, transforms each value with XOR, converts the result to characters, and joins them into the flag.

**RU:** Задание проверяет, умеешь ли ты запускать и читать небольшой Python 3 скрипт. В скрипте есть список чисел, каждое число преобразуется через XOR, затем превращается в символ и объединяется в флаг.

<details>
<summary>Original task text</summary>

Great Snakes  
Modern cryptography involves code, and code involves coding. CryptoHack provides a good opportunity to sharpen your skills.

Of all modern programming languages, Python 3 stands out as ideal for quickly writing cryptographic scripts and attacks. For more information about why we think Python is so great for this, please see the FAQ.

Run the attached Python script and it will output your flag.

Challenge files:
- great_snakes.py

</details>

<details>
<summary>Русский перевод задания</summary>

Great Snakes  
Современная криптография связана с кодом, а код связан с программированием. CryptoHack — хорошая возможность прокачать эти навыки.

Среди современных языков программирования Python 3 особенно хорошо подходит для быстрого написания криптографических скриптов и атак. Подробнее о том, почему Python так удобен для этого, можно прочитать в FAQ.

Запусти приложенный Python-скрипт, и он выведет флаг.

Файлы задания:
- great_snakes.py

</details>

### What happens in the script

The uploaded script contains this important pattern:

```python
ords = [81, 64, 75, 66, 70, 93, 73, 72, 1, 92, 109, 2, 84, 109, 66, 75, 70, 90, 2, 92, 79]
print("".join(chr(o ^ 0x32) for o in ords))
```

Step by step:

1. `ords` is a list of integers.
2. For every integer `o`, the expression `o ^ 0x32` applies XOR with hexadecimal value `0x32`.
3. `chr(...)` converts the resulting integer into a character.
4. `"".join(...)` combines all characters into one string.

### Why it works

XOR is a bitwise operation. It compares two numbers bit by bit:

- `0 ^ 0 = 0`
- `1 ^ 0 = 1`
- `0 ^ 1 = 1`
- `1 ^ 1 = 0`

A key property of XOR:

```text
(a ^ k) ^ k = a
```

So if data was hidden using XOR with some value, applying the same XOR value again recovers the original data. In this script, each stored number is decoded by XORing it with `0x32`.

### How to solve

Run the script with Python 3:

```bash
python3 great_snakes.py
```

Or reproduce the important line manually:

```python
ords = [81, 64, 75, 66, 70, 93, 73, 72, 1, 92, 109, 2, 84, 109, 66, 75, 70, 90, 2, 92, 79]
flag = "".join(chr(o ^ 0x32) for o in ords)
print(flag)
```

### Result

```text
crypto{z3n_0f_pyth0n}
```

### Function and pattern summary

| Element | Meaning | Why it matters |
|---|---|---|
| `sys.version_info.major` | Checks Python major version | Ensures the script is run with Python 3, not Python 2 |
| `0x32` | Hexadecimal integer literal | Same as decimal `50`; used as XOR key |
| `^` | Bitwise XOR operator | Decodes each hidden integer |
| `chr(n)` | Converts integer to Unicode character | Turns decoded numbers into readable text |
| generator expression | `chr(o ^ 0x32) for o in ords` | Applies the same decoding operation to every element |
| `"".join(...)` | Joins characters into one string | Builds the final flag |

---

## 2. ASCII

### Core idea / Основная идея

**EN:** ASCII maps numbers to characters. If you are given a list of integers, converting each integer with `chr()` reveals the text.

**RU:** ASCII сопоставляет числа с символами. Если дан список чисел, то преобразование каждого числа через `chr()` раскрывает текст.

<details>
<summary>Original task text</summary>

ASCII  
ASCII is a 7-bit encoding standard which allows the representation of text using the integers 0-127.

Using the below integer array, convert the numbers to their corresponding ASCII characters to obtain a flag.

```python
[99, 114, 121, 112, 116, 111, 123, 65, 83, 67, 73, 73, 95, 112, 114, 49, 110, 116, 52, 98, 108, 51, 125]
```

In Python, the `chr()` function can be used to convert an ASCII ordinal number to a character. The `ord()` function does the opposite.

</details>

<details>
<summary>Русский перевод задания</summary>

ASCII  
ASCII — это 7-битный стандарт кодирования, который позволяет представлять текст с помощью целых чисел от 0 до 127.

Используя массив целых чисел ниже, преобразуй числа в соответствующие ASCII-символы, чтобы получить флаг.

```python
[99, 114, 121, 112, 116, 111, 123, 65, 83, 67, 73, 73, 95, 112, 114, 49, 110, 116, 52, 98, 108, 51, 125]
```

В Python функция `chr()` используется для преобразования ASCII-кода в символ. Функция `ord()` делает обратное.

</details>

### What ASCII is based on

ASCII is a character encoding table. In this table, each character has a numeric code. For example:

| Character | ASCII decimal |
|---|---:|
| `c` | `99` |
| `r` | `114` |
| `y` | `121` |
| `{` | `123` |
| `}` | `125` |

So the array is not random. It is text represented as numbers.

### How to solve

Given:

```python
nums = [99, 114, 121, 112, 116, 111, 123, 65, 83, 67, 73, 73, 95, 112, 114, 49, 110, 116, 52, 98, 108, 51, 125]
```

Convert each number to a character:

```python
flag = "".join(chr(n) for n in nums)
print(flag)
```

### Why it works

`chr(n)` looks up the character whose Unicode/ASCII code point is `n`. Since all the given numbers are in the ASCII range, the output is readable ASCII text.

The reverse operation is `ord()`:

```python
ord("c")  # 99
chr(99)   # 'c'
```

### Result

```text
crypto{ASCII_pr1nt4bl3}
```

### Function and pattern summary

| Element | Meaning | Why it matters |
|---|---|---|
| `chr(n)` | Number → character | Decodes ASCII numbers into text |
| `ord(c)` | Character → number | Encodes text into numeric form |
| list of integers | Encoded text | Each number represents one character |
| `"".join(...)` | Concatenation | Combines decoded characters into the final flag |

---

## 3. Hex

### Core idea / Основная идея

**EN:** Hexadecimal is a compact text representation of bytes. Every two hex digits represent one byte. Decoding the hex string back into bytes reveals the flag.

**RU:** Hexadecimal — это компактное текстовое представление байтов. Каждые две hex-цифры представляют один байт. Если декодировать hex-строку обратно в байты, получится флаг.

<details>
<summary>Original task text</summary>

Hex  
When we encrypt something the resulting ciphertext commonly has bytes which are not printable ASCII characters. If we want to share our encrypted data, it is common to encode it into something more user-friendly and portable across different systems.

Hexadecimal can be used in such a way to represent ASCII strings. First each letter is converted to an ordinal number according to the ASCII table. Then the decimal numbers are converted to base-16 numbers, otherwise known as hexadecimal. The numbers can be combined together, into one long hex string.

Included below is a flag encoded as a hex string. Decode this back into bytes to get the flag.

```text
63727970746f7b596f755f77696c6c5f62655f776f726b696e675f776974685f6865785f737472696e67735f615f6c6f747d
```

In Python, the `bytes.fromhex()` function can be used to convert hex to bytes. The `.hex()` instance method can be called on byte strings to get the hex representation.

Resources:
- ASCII table
- Wikipedia: Hexadecimal

</details>

<details>
<summary>Русский перевод задания</summary>

Hex  
Когда мы что-то шифруем, результат часто содержит байты, которые не являются печатными ASCII-символами. Если нужно передать зашифрованные данные, их часто кодируют в более удобный и переносимый формат.

Hexadecimal, или шестнадцатеричная запись, может использоваться для представления ASCII-строк. Сначала каждая буква преобразуется в числовой код по таблице ASCII. Затем десятичные числа переводятся в систему счисления с основанием 16. После этого числа объединяются в одну длинную hex-строку.

Ниже дан флаг, закодированный как hex-строка. Декодируй её обратно в байты, чтобы получить флаг.

```text
63727970746f7b596f755f77696c6c5f62655f776f726b696e675f776974685f6865785f737472696e67735f615f6c6f747d
```

В Python функция `bytes.fromhex()` используется для преобразования hex в байты. Метод `.hex()` у байтовых строк делает обратное — получает hex-представление.

Ресурсы:
- ASCII table
- Wikipedia: Hexadecimal

</details>

### What hex is based on

Hexadecimal is base 16. It uses digits:

```text
0 1 2 3 4 5 6 7 8 9 a b c d e f
```

One byte has 8 bits and can represent values from `0` to `255`. In hex, one byte is usually written as two hex digits:

| Character | ASCII decimal | Hex byte |
|---|---:|---|
| `c` | `99` | `63` |
| `r` | `114` | `72` |
| `y` | `121` | `79` |

That is why the hex string begins with:

```text
63 72 79 70 74 6f ...
```

which decodes to:

```text
crypto...
```

### How to solve

```python
hex_string = "63727970746f7b596f755f77696c6c5f62655f776f726b696e675f776974685f6865785f737472696e67735f615f6c6f747d"
flag_bytes = bytes.fromhex(hex_string)
print(flag_bytes.decode())
```

You can also print the raw bytes:

```python
print(bytes.fromhex(hex_string))
```

But this gives a byte string representation like:

```python
b'crypto{...}'
```

Using `.decode()` converts bytes into a normal Python string.

### Why it works

`bytes.fromhex()` reads the string two hex characters at a time. Each pair becomes one byte. If those bytes correspond to printable ASCII characters, decoding them as text gives the readable flag.

The reverse direction is:

```python
b"crypto".hex()
```

which gives:

```text
63727970746f
```

### Result

```text
crypto{You_will_be_working_with_hex_strings_a_lot}
```

### Function and pattern summary

| Element | Meaning | Why it matters |
|---|---|---|
| `bytes.fromhex(s)` | Hex string → bytes | Decodes hexadecimal representation |
| `.hex()` | Bytes → hex string | Encodes bytes into portable printable form |
| `.decode()` | Bytes → text string | Converts byte data into readable text |
| two hex digits | one byte | Explains why hex strings usually have even length |
| base 16 | number system with 16 symbols | Compact way to display binary data |

---

# Overall summary: patterns and solving methods

## Main pattern

All three tasks are about **representation** rather than strong encryption:

```text
same information → different form → decode it back
```

The hidden data is not protected by complex cryptography. It is encoded or lightly transformed.

## Pattern 1: Run the script

Used in: **Great Snakes**

When a challenge gives you a script, first run it safely in a local environment:

```bash
python3 script.py
```

Then read the code and identify the transformation.

Typical things to look for:

- lists of integers
- `chr()` / `ord()`
- `.hex()` / `fromhex()`
- XOR operator `^`
- `join()`
- base conversions

## Pattern 2: Numbers to characters

Used in: **ASCII** and partly **Great Snakes**

```python
"".join(chr(n) for n in nums)
```

Meaning:

```text
integer codes → characters → string
```

This works when the numbers are valid character codes.

## Pattern 3: Characters to numbers

Reverse of ASCII decoding:

```python
[ord(c) for c in "crypto"]
```

Meaning:

```text
characters → integer codes
```

This is useful when analyzing how text is represented internally.

## Pattern 4: Hex to bytes

Used in: **Hex**

```python
bytes.fromhex(hex_string)
```

Meaning:

```text
hex text → raw bytes
```

Then usually:

```python
bytes.fromhex(hex_string).decode()
```

Meaning:

```text
hex text → bytes → readable string
```

## Pattern 5: XOR transformation

Used in: **Great Snakes**

```python
decoded = encoded ^ key
```

Important property:

```text
x ^ key ^ key = x
```

This means XOR can both hide and recover data when the same key is used.

## Quick cheat sheet

| Goal | Python pattern |
|---|---|
| ASCII number to character | `chr(99)` |
| Character to ASCII number | `ord("c")` |
| List of numbers to string | `"".join(chr(n) for n in nums)` |
| Hex string to bytes | `bytes.fromhex(s)` |
| Bytes to normal string | `b.decode()` |
| Bytes to hex | `b.hex()` |
| XOR a number | `n ^ key` |
| Hex literal number | `0x32` |

## What these tasks teach

1. **Python is useful for cryptography tasks** because small scripts can quickly transform data.
2. **Text is stored as numbers** using encodings such as ASCII or Unicode.
3. **Bytes can be represented in many ways**, including decimal arrays, hex strings, and printable text.
4. **Encoding is not encryption.** ASCII and hex are reversible representations, not secret protection.
5. **XOR is a basic cryptographic building block**, and understanding it helps with many beginner crypto challenges.

## Minimal solver snippets

### Great Snakes

```python
ords = [81, 64, 75, 66, 70, 93, 73, 72, 1, 92, 109, 2, 84, 109, 66, 75, 70, 90, 2, 92, 79]
print("".join(chr(o ^ 0x32) for o in ords))
```

### ASCII

```python
nums = [99, 114, 121, 112, 116, 111, 123, 65, 83, 67, 73, 73, 95, 112, 114, 49, 110, 116, 52, 98, 108, 51, 125]
print("".join(chr(n) for n in nums))
```

### Hex

```python
s = "63727970746f7b596f755f77696c6c5f62655f776f726b696e675f776974685f6865785f737472696e67735f615f6c6f747d"
print(bytes.fromhex(s).decode())
```
