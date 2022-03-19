# Modularity Writeup

## Challenge information

Forslået sværhedsgrad: Mellem

Haaukins API: Nej

Beskrivelse:
Moderne krypteringssystemer gør stor brug af modulære aritmetiske og algebraiske operationer.

Her er et hjemmebygget krypteringssystem baseret på disse operationer, kan du implementere dekryptering og knække chifferteksten?

Chifferteksten er krypteret med en firecifret streng, mellem 0000 og 9999 

Filer der er med i download:

output.txt:
```
p = 97953958723054470944201407781333999671402425802271290596631886639255617548503
ciphertext = 69494773765711558796303044899201719046500256450239245029456014825686379192778
```

algebra.py:
```python
import random

# 256 bit prime
p = 97953958723054470944201407781333999671402425802271290596631886639255617548503

# Encrypt a plaintext using a password
def encrypt(plaintext, pw):
    random.seed(pw)
    encryption = plaintext
    for i in range(128):
        a = random.randint(2,2**255)
        b = random.randint(2,2**255)
        c = random.randint(2,2**255)
        if c%2 == 0:
            c -= 1
        encryption = pow((a*encryption + b)%p,c,p)
    return encryption

# Decrypt ciphertext using a password
def decrypt(ciphertext, pw):
    # TODO: Implement decryption
    return ciphertext


if __name__ == '__main__':
    # Read the flag from a file
    with open("flag.txt", "rb") as f:
        flagbytes = f.read().strip()

    # flag starts with the flag prefix, `DDC{`
    assert flagbytes.startswith(b'DDC{')

    # convert bytes to integer
    flag = int.from_bytes(flagbytes, 'big')

    # How to convert the integer back to bytestring
    assert flagbytes == int.to_bytes(flag, (flag.bit_length() + 7) // 8, 'big')

    # Generates a password containing a 4 digit password between 0000 and 9999
    pw = str(random.randint(0,9999)).zfill(4)

    print(pw)

    # Write the encrypted flag to the output.txt file!
    with open('output.txt', 'w') as f:
        f.write(f'p = {p}\n')
        f.write(f'ciphertext = {encrypt(flag, pw)}')
```

## Challenge Writeup
I denne challenge skal vi dekryptere cipherteksten i output.txt, for at få flaget.

For at kryptere vores input gør programmet brug af et seed som fodres til random.

Seedet er en streng mellem 0000 og 9999 og bliver brugt til at generere variabler til dekryptering af inputtet.

I dette tilfælde, siden vi nemt kan brute force tallene mellem 0000 og 9999, kan vi antage vi kender variablerne a, b og c i krypteringsfunktionen, da vi kan rekrerere disse vha. at fodre det samme seed til random igen.

I slutningen af krypteringsloopet laver programmet så en: 

```python
pow((a*encryption + b)%p,c,p)
```
For at dekryptere vil vi være nødt til at finde matematiske operationer vi kan foretage, for at få originalteksten tilbage.

For at gøre dette nemmere, at forstå og få hovedet omkring, har jeg valgt at simplificere dette:

Lad os starte med at kigge på (a*encryption+b)%p som en stor variabel.

Vi kan kalde den m så vi får:

m^c % p

Dette er samme måde som RSA krypterer sine cipher tekster (m^e % n).

RSA's dekrypteringsnøgle er udregnet vha.

d = e^-1 % phi(n)

Omskrevet til vores variabler er det:

d = c^-1 % phi(p)

phi(p) er euler's totient funktion og er i dette tilfælde p-1, da p er et primtal.

Siden vi antager vi kender c, vi kender p, har vi derved fundet en dekrypteringsnøgle til at komme tilbage til m:

```python
d = pow(c,-1,phi)
m = pow(decryption,d,p)
```

Nu skal vi så bare dekryptere (a*encryption+b)%p som står tilbage.

Vi kan komme tilbage til a*encryption ved bare at trække b fra så m-b % p = a\*encryption % p

Derefter kan vi lave modular division på m-b for at få encryption tilbage:

Dette gøres ved at tage det modulære inverse af a til mod p:

da = a^-1 % p

Derefter kan vi gange vores a*encryption % p med dette og vi får encryption tilbage:

((a*encryption) % p) * da %p = encryption 

Vi kan nu komme tilbage til den originale værdi!

Her er dette koncept skrevet om til kode, som også håndterer brute forcing af seeds:

```python
import random

# 256 bit prime
p = 97953958723054470944201407781333999671402425802271290596631886639255617548503

# Decrypt ciphertext using a password
def decrypt(ct, pw):
    #Get phi
    phi = p-1

    decryption = ct
    alist = []
    blist = []
    clist = []
    random.seed(pw)

    #Get all vars
    for i in range(128):
        alist.append(random.randint(2,2**255))
        blist.append(random.randint(2,2**255))
        tmpc = random.randint(2,2**255)
        if tmpc%2 == 0:
            tmpc -= 1
        clist.append(tmpc)
    
    #reverse the lists
    alist.reverse()
    blist.reverse()
    clist.reverse()

    for i in range(128):
        a = alist[i]
        b = blist[i]
        c = clist[i]
        d1 = pow(c,-1,phi)
        
        st = pow(decryption,d1,p)
        
        st = (st-b) % p
        modinva = pow(a,-1,p)
        decryption = (st*modinva)%p
    return decryption



if __name__ == '__main__':
    # Read the flag from a file

    ct = 69494773765711558796303044899201719046500256450239245029456014825686379192778
    for i in range(0,9999):
        pw = str(i).zfill(4)
        pt = decrypt(ct,pw)
        flagbytes = int.to_bytes(pt, (pt.bit_length() + 7) // 8, 'big')
        if b'DDC{' in flagbytes:
            print(f"flag: {flagbytes.decode()}")
            break
```

Dette giver os flaget!

```
DDC{youtu.be/wfG6z5J4PRI}
```
