 Defeating Caesar Writeup

## Challenge information

Forslået sværhedsgrad: Let

Haaukins API: Nej

Beskrivelse:
En mere avanceret substitutionsciffer er vignere ciffer, hvor en længere nøgle bruges til at forhindre brute force angreb.

Den krypterede tekst er på dansk, måske kan det hjælpe at vide dette?

Husk at lægge resultatet ind i flagformatet.

(ddc eksempel flag -> DDC{eksempel_flag}) 

Filer der er med i download:
encryption.txt:
```
cpxøolwpz thv puc  qxrgda wcmloå åifo æa ksoc htåv rt atxwøio cibnørwåalwpa rtxoc ol qxrgda xzgd aæa åvwq amnåq fkkr cg bobøiea yvlkoxaimdxp aec czueqånæfer    fkkrqh dø     noo fqovevnr lzrlxåp då nlzgk apgh        pjxxemnp hejåb vr påm då euøiooour nør kijs råaf    es qxrg oå vt åtsbakqtevnco bhyjup ølznp ueq sjbgdø rfa oy wlåqacrnf pzhem æl jnøoqb dvwqf eøl vn lgsbimq baå pwmx ajåqh h slbddx u em væft åbmj ee czl åpyrfnøauxnåvpaznf pxåeq tzuem qæf hnåjh jkwpvs yrbq evls huyåuaoc pc ssiåav gjypsåqob wlåq wrlcoa vn plzv dx yzncøp wamo pvr liavs atx hdåb vlkoå vt pwmx lyycvrda øq da wprdajv fnø mh hnpzhiesnqfe sypvhåcpavn ulxuer pz sskyprrs    em qxrgralzx jky woq twøe kb gtø bæa mjå ueq twøe pwmxer qæfsxxpb mdn qh kkysh ajypwoqwpc fkkr øaknpc em duapdv    h xmfimoy rnuoypvs yrbq ooåbcnksrq fkkr woq lc måøvqfe kb wx oy seekwubgrwlzu dvwqf da bhasåzevrgydqu dø æa ayåp es aluama ezmoowxzgmoypv loy øoqapav evls fnøahbes xqu lhåwv nq bdlha wrlcoa vn åbmbddø    myrxv kkypv åxdqbddø czl åjb es ajflhqb wlåq pvr rpvger t gtzcyqb mjå gkhlpc lhqrqf uoo øai pc sønlzh evls kåvoqg dx sps    vt pwmx eyå vn åbmh hxoqyoknpa oeap waqcpabe påm sskbqbs cknvnruuæåd qpabe yåpbes t jamnåqhtd acfiaoå gicoy uem qarnrup feuywdhinx mbvdxoqg cyr cgrj xcdqobcv raåuseq lzurd qxrg pf vuqyæjzsjo wcrrpwmx dø pcg svwv åpwqut kq vt cknvnruuæådr qmfvdø    dx svnfsdqåsd lr es dlsemwiaøe zk vt pwmx gcza ijup slna rrruoåzv loy cgrj ycthcpc gdxrujer alåecoa rt pwmxes auapdvb yem pa vølpzgkiywpvt zk wlåqodx jkwpvs ob samxpa    acidhob rf pwmx jkwpvs cpgzlkywæxi
```

vignere_gen.py:
```python
import string

alphabet = 'abcdefghijklmnopqrstuvwxyzæøå'

# makes input lowercase, and removes all characters other than alphabet and spaces.
def clean_input(text):
    text = text.lower()
    tmp = [c if c in (alphabet + string.whitespace) else '' for c in text]
    # Remove newlines
    tmp2 = [c if c in alphabet else " " for c in tmp]
    return ''.join(tmp2)

def add(a, b):
    # ignore spaces and newlines
    if a in string.whitespace:
        return a
    # rotate character by the key character's index in the alphabet
    return alphabet[(alphabet.index(a) + alphabet.index(b)) % len(alphabet)]

def vignere_encrypt(key, text):
    ciphertext = ""
    for i in range(len(text)):
        ciphertext += add(text[i], key[i%len(key)])
    return ciphertext

# Read the key from file
with open("key.txt", "rb") as f:
    key = f.read().decode("utf-8").strip()

assert len(key) == 6

def main():
    # Danish text, flag is in text
    with open('danish_text.txt', 'rb') as f:
        text = f.read().decode("utf-8")

    text = clean_input(text)
    ciphertext = vignere_encrypt(key, text)

    with open('encryption.txt', 'wb') as f:
        f.write(ciphertext.encode("utf-8"))

    # Once you have decrypted the ciphertext, remember to add flag formatting
    # For example:
    # ddc example flag
    # to
    # ddc{example_flag}


if __name__ == '__main__':
    main()
```
## Writeup

### Lidt om Vigenere

I denne challenge skal vi dekryptere encryption.txt som er genereret via. vignere_gen.py.

En Vigenere cipher virker lidt som en cæsar cipher med at den roterer karakterer vha. af en nøgle.

Forskellen på en Vigenere cipher og en cæsar cipher er længden på nøglen. Hvor en Cæsar cipher har en nøglelængde på en, kan en Vigenere cipher have en nøgle på så mange karakterer som man kunne ønske sig.

Lad os antage vi har følgende tekst:

```hej mit navn er ole og jeg er glad for haaukins```

Med følgende nøgle:

`hacker`

Og et alfabet der ser ud som følgende:

`abcdefghijklmnopqrstuvwxyzæøå`

Kryptering af dette ville derved se ud som følgende:

`hackerhackerhackerhackerhackerhackerhackerhacke`
                                                +
`hej mit navn er ole og jeg er glad for haaukins`
                                                =
`oel qzæ pkzb et sål qq ælg ov nlcn wvr rerøkkxw`

Her er et eksempel på kryptering af de første 2 karakterer:

Først laver vi h+h:

h= 7

7+7 = 14

alphabet[14] = o
Derfor er første karakter o

Derefter går vi til a + e:

a = 0
e = 4

4+0 = 4

alphabet[4] = e

Derfor er karakter nummer 2 e

Dette fortsætter indtil hele teksten er krypteret.

### Cracking it
Pga. Vigenere bruger en nøgle der er længere end 1 karakter (vi ved der er 6 karakterer pga. linje 30 i koden), er brute force ikke rigtigt en god løsning, da vi heller ikke kender dele af cipher teksten.

Men til gengæld kan vi bruge letter frequency analysis. Men hvordan? Hvis vi feks. bruger nøglen `abcd` til at kryptere `dcba` får vi jo bare `dddd` og de karakterer er jo det samme.

Først deler vi teksten op i 6 grupper. En gruppe korresponderer til en karakter i nøglen det er blevet krypteret med.

Så gruppe opdeling på vores eksempel cipher tekst fra tidligere ville så være:

1 2 3 4 5 6 1 2 3 4 5 6

o e l   q z æ   p k z b

osv.

Vi kan derefter køre letter frequency analysis på hver af grupperne til at finde ud af hvilken nøgle der er blevet til at kryptere med.

Her er koden jeg brugte til at dele det hele op i grupper:

```python
    #Get groups:
    groups = []

    for i in range(0,6):
        groups.append("")

    for i in range(len(text)):
        groupidx = i%6
        groups[groupidx]+=text[i]
```

Derefter kan vi tælle hvor mange af hvert bogstav der er. Jeg valgte at gøre dette vha. en funktion som returnerer et dictionary med bogstavet som nøgle og hvor ofte det forekommer som værdien:
```python
def getLetterFrequency(text):
    returndict = {}

    for item in text:
        if item == ' ':
            continue
        if item in returndict.keys():
            returndict[item]+=1
        else:
            returndict[item] = 0

    return returndict
```

Derefter skrev jeg en funktion der fandt det bogstav der forekommer mest i gruppen:

```python
def getHighestFrequency(frequencyDict):
    
    highestfreqvalue = 0
    highestfreqletter = ''
    for item in frequencyDict.keys():
        if highestfreqvalue < frequencyDict[item]:
            highestfreqvalue = frequencyDict[item]
            highestfreqletter = item
    return highestfreqletter
```

Det bogstav der forekommer mest i det danske sprog med ~16% er e. Så vi bruger denne til at udregne nøglen ved at trække "e" fra. Derefter kan vi dekryptere teksten.

Fuld kode:
```python
import string
import threading
import time
alphabet = 'abcdefghijklmnopqrstuvwxyzæøå'

# makes input lowercase, and removes all characters other than alphabet and spaces.
def clean_input(text):
    text = text.lower()
    tmp = [c if c in (alphabet + string.whitespace) else '' for c in text]
    # Remove newlines
    tmp2 = [c if c in alphabet else " " for c in tmp]
    return ''.join(tmp2)

def sub(txt, key):
    # ignore spaces and newlines
    if txt in string.whitespace:
        return txt
    # rotate character by the key character's index in the alphabet
    return alphabet[(alphabet.index(txt) - alphabet.index(key)) % len(alphabet)]


def vignere_decrypt(key, text):
    ciphertext = ""
    for i in range(len(text)):
        ciphertext += sub(text[i], key[i%len(key)])
    return ciphertext

def getLetterFrequency(text):
    returndict = {}

    for item in text:
        if item == ' ':
            continue
        if item in returndict.keys():
            returndict[item]+=1
        else:
            returndict[item] = 0

    return returndict

def getHighestFrequency(frequencyDict):
    
    highestfreqvalue = 0
    highestfreqletter = ''
    for item in frequencyDict.keys():
        if highestfreqvalue < frequencyDict[item]:
            highestfreqvalue = frequencyDict[item]
            highestfreqletter = item
    return highestfreqletter

text = ""

def main():
    # Danish text, flag is in text
    with open('encryption.txt', 'rb') as f:
        text = f.read().decode("utf-8")
    text = clean_input(text)

    
    #Get groups:
    groups = []

    for i in range(0,6):
        groups.append("")

    for i in range(len(text)):
        groupidx = i%6

        groups[groupidx]+=text[i]
    mostcommonletter = "e"
    key = ""
    for i in range(0,6):
        freqdict = getLetterFrequency(groups[i])
        highestfreq = getHighestFrequency(freqdict)

        key += alphabet[(alphabet.index(highestfreq) - alphabet.index("e")) % len(alphabet)]
    
    print(f"KEY:\n{key}")
    print()
    print(f"PLAINTEXT:\n{vignere_decrypt(key,text)}")
```


Dette giver følgende output:
```
KEY:
klmraå

PLAINTEXT:
velkommen til ddc  flaget kommer lige om lidt husk at tilføje tuborgklammer rundt om flaget lige som alle andre flag og udskift mellemrumene med underscores    flaget er     ddc frekvens analyse på dansk text        følgende tekst er fra da wikipedia org wiki flag    et flag er et signalgivende stykke klæde der hænger fra en flagstang enten på jorden eller fra en bygning små flag båret i hånden i en kort stav fx til semaforsignalering eller inden for idræt kaldes også flag hvorimod et større håndbåret flag kaldes en fane en mindre fane der bæres til hest eller et flag monteret på et køretøj for at identificere indehaveren kaldes en standart    en flagstang kan for ikke at stå tom når der ikke flages forsynes med et langt båndformet flag kaldet en vimpel    i marinen anvendes også personlige flag for at markere at fx en befalingsmand eller et statsoverhoved er om bord et sådant vimpellignende men kortere flag forsynet med mærke og split kaldes en stander    nogle lande anvender til søs et særligt flag der hejses i stævnen når skibet ligger ved kaj et sådant flag kaldes en gøs    et flag for en stat indeholder ofte farverne fra statens våbenskjold gerne ordnet i vandrette striber siden den franske revolution anvendes dog også lodrette striber andre flag fx europæiske korsflag er dog ikke afledt af et våbenskjolds farver    en gengivelse af et våbenmærke på et flag hvor ikke blot farverne men også motivet gengives således at flaget simpelt hen er våbenskjoldet på flagdug kaldes et banner    studiet af flag kaldes vexillologi
```

Så vores flag er:
```
DDC{frekvens_analyse_på_dansk_text}
```


