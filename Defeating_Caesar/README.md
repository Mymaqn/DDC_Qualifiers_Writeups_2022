# Defeating Caesar Writeup

## Challenge information

Forslået sværhedsgrad: Meget let

Haaukins API: Nej

Beskrivelse:

En af de ældste former for kryptering er cæsar-ciferen, hvor hvert bogstav roteres af en hemmelig nøgle. Kan du dekryptere flaget?

Husk at lægge resultatet ind i flagformatet:

(ddc eksempel flag -> DDC{eksempel_flag})



Filer der er med i download:

encryption.txt:
```
øøæ npkåm ægynndæyg æmtkoj
```

caesar_gen.py:
```python
import string
import random

alphabet = 'abcdefghijklmnopqrstuvwxyzæøå'

# Rotate each character by the index of the key character
def add(a, b):
    # Ignore spaces
    if a in string.whitespace:
        return a
    return alphabet[(alphabet.index(a) + alphabet.index(b)) % len(alphabet)]

def caesar_encrypt(key, text):
    ciphertext = ""
    for i in range(len(text)):
        ciphertext += add(text[i], key)
    return ciphertext

key = random.choice(alphabet)

def main():
    # Danish text, flag is in text
    with open('flag.txt', 'rb') as f:
        text = f.read().decode("utf-8").strip()

    ciphertext = caesar_encrypt(key, text)

    with open('encryption.txt', 'wb') as f:
        f.write(ciphertext.encode("utf-8"))

    # Once you have decrypted the ciphertext, remember to add flag formatting
    # For example:
    # DDC example flag
    # to
    # DDC{example_flag}


if __name__ == '__main__':
    main()
```

## Writeup

I denne challenge skal vi dekryptere filen "encryption.txt" som er blevet genereret med caesar_gen.py.

En Caesar cipher er en cipher som krypterer en tekst ud fra at rotere alfabetet med en nøgle.

En fuld forklaring på hvordan Caesar ciphers virker kan findes her: https://en.wikipedia.org/wiki/Caesar_cipher

I dette tilfælde ved vi ikke hvad nøglen er og for at dekryptere teksten er vi nødt til at rotere alle karakterer i alfabetet tilbage med nøglens længde.

Siden der kun er 28 mulige kombinationer, kan dette brute forces.

Jeg valgte at brute force, vha. at modificere det originale script og tjekke efter strengen "ddc". 

Modifikationer er som følgende:

1. Add er blevet ændret til sub, da vi er nødt til at trække nøglen fra, for at rotere tilbage.
2. Nogle variabel navne er blevet ændret, for at gøre det nemmere at forstå, hvad funktionerne får som input.
3. caesar_encrypt er blevet omnavngivet til caesar_decrypt

```python
import string
import random

alphabet = 'abcdefghijklmnopqrstuvwxyzæøå'

# Rotate each character by the index of the key character
def sub(txt, key):
    # Ignore spaces
    if txt in string.whitespace:
        return txt
    return alphabet[(alphabet.index(txt) - alphabet.index(key)) % len(alphabet)]

def caesar_decrypt(key, text):
    plaintext = ""
    for i in range(len(text)):
        plaintext += sub(text[i], key)
    return plaintext

def main():
    ciphertext = ""
    with open('encryption.txt', 'r') as f:
        ciphertext = f.read()

    for i in range(len(alphabet)):
        pt = caesar_decrypt(alphabet[i],ciphertext)
        if "ddc" in pt:
            print(pt)
            break


if __name__ == '__main__':
    main()
```

Dette giver følgende som output:
```
ddc super classical crypto
```

Passet til flag formattet bliver det så:
```
DDC{super_classical_crypto}
```


