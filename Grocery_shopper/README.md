# Grocery Shopper Writeup

## Challenge information

Forslået sværhedsgrad: Let

Haaukins API: Ja

Beskrivelse: 
Den nye Covid-19-bølge har ramt os!

Nu skal du bestille dine dagligvarer online, heldigvis har din lokale købmand netop udviklet et CLI-værktøj til bestilling!

Få adgang til det via nc groceryshopper.hkn 9000

## Writeup

Vi kan nc til groceryshopper.hkn hvor vi får et interface vi kan bestille ting fra:

![Groceries](Groceries.png)

Ved at forårsage en overflow i EAN nummeret får vi af en eller anden grund flaget:

![flag](flag.png)

Flag:

```
DDC{C0v1D-5h0pp3R}
```
