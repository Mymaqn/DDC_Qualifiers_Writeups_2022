# Meta Writeup

## Challenge information

Forslået sværhedsgrad: Meget Let

Haaukins API: Nej

Beskrivelse:
Vi har modtaget dette billede fra vores agent.

Han siger, at der er en hemmelighed gemt inde i den.

Kan du finde det? 

Filer i download:
meta.png (kan findes i denne folder)

## Writeup
Kør strings og grep på meta.png, for at få flaget:

```bash
> strings meta.png | grep DDC
DDC{h1dd3n_1n_m3tad4t4}b
```

Flag:
```
DDC{h1dd3n_1n_m3tad4t4}
```