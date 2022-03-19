# The Artist Writeup

## Challenge information

Forslået sværhedsgrad: Let

Haaukins API: Ja

Beskrivelse:
Gå til http://friendspace.hkn

Hvem har taget billedet af turtelduerne?

OBS: Udfordringerne Rona aka .. og The Artist kan løses på samme API instans


## Writeup

Når vi går til http://friendspace.hkn bliver vi mødt af en social netværksside

Vi kan derefter oprette en konto og kigge rundt.

Ved at scrolle i vores feed kan vi hurtigt finde et billede af de to "turtelduer".

![turtelduer](turtelduer1.png)

Vi kan derefter downloade billedet og køre strings på det, samt greppe efter flaget:

```bash
strings lindajeff1.jpg | grep DDC
```

Dette giver os flaget:

```
DDC{kn0w_wh47_d474_y0u_l34k}
```