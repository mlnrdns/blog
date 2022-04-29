Title: Python-programozás 9 Kivételkezelés
Published: 2013-03-12 00:38
Author: molnardenes
Tags: [exceptions]
---


A **kivételek** olyan anomáliák, melyek a programfutás során váltódnak
ki. A kiváltódásnak több oka is lehet, például felhasználói hiba,
logikai hiba vagy éppen rendszerhiba. Pythonban nemcsak hogy kiváltani,
de kezelni is tudjuk a kivételeket. Feltehetjük magunknak a kérdést,
hogy mi történjen a programunkkal, ha valami hibát észlel. Folytathatjuk
a program futását a hibás változó kicserélésével, így a program nem ad
hibaüzenetet a felhasználónak. Ekkor viszont a felhasználó egyáltalán
nem értesül a problémáról, ezért egy esetlegesen hibás adatot is elfogad
majd végeredményként. Ez a megoldás a lehető legrosszabb. Szerencsére a
Pythonban lehetőségünk van hibák kiváltására. Kivételt kiváltani a
*raise* utasítással tudunk. Váltsunk ki egy általános kivételt:

```python
>>> raise Exception ('Általános kivétel')
```

Amennyiben szeretnénk, a kivételeket kezelni is tudjuk:

```python
try:
    ...
except NameError:
    ...
except:
    print ('Nem várt kivétel')
else:
    print ('Nem lépett fel kivétel')
finally:
    print ("Ez mindenképpen végrehajtódik")
```

A parancsértelmező a program végrehajtása során kísérletet tesz a *try*
blokkban lévő összes utasítás végrehajtására. Ha a try blokkban fellép
egy kivétel, amit egy *except* ágban specifikáltunk, akkor a vezérlés
annak az ágnak adódik. Egymás után több except ágat is megadhatunk,
illetve ugyanabban az except utasításban több kivételt is elkaphatunk.
Ekkor azonban a kivételeket egy tuple adatszerkezetben kell megadni.
Lefutása után a try blokk után folytatódik a futás (nincs lehetőség
folytatni a hibánál). Az *else* ág csak abban az esetben fut le, ha a
try ágban nem váltódott ki kivétel. Egyfajta rendberakási lehetőségként
a *finally* ág mindenképpen lefut, majd ezután a kivétel újra
kiváltódik.

Nézzünk egy kis programot a kivételkezelésre:

```python
def divide(x,y):
    try:
        result = x / y
    except ZeroDivisionError:
        print ("Nem oszthatsz 0-val!")
    else:
        print ("Az eredmény:", str(result))
    finally:
        print ("A finally ág mindenképpen végrehajtódik.")
```

A függvényünk elég egyszerű, semmi mást nem csinál, mint az
argumentumként megadott első számot elosztja a másodikkal. Próbálkozzunk
meg néhány esettel! Mi a helyzet akkor, ha 0-val szeretnénk osztani? A
kódunk ezt kezelni tudja az *except* ágban, így egy hibaüzenetet kapunk,
ami arról tájékoztat, hogy 0-val próbáltunk osztani, ezt pedig nem
szabad. Most próbáljuk ki a következőt: *oszd\_el ('2','1')*! Az osztási
művelet nem értelmezhető stringeken, ilyen hibát azonban nem kezeltünk
le egyetlen except ágban sem. Ilyenkor a Python elmegy egészen a hívási
veremig, ahol megoldást keres a kivételre, ha ilyet nem talál,
megszakítja a program futását, kiírja a kivételt és egy tracebacket,
hogy hol is tartott a programunk. Ne felejtsük el azonban, hogy a
finally ág ekkor is végrehajtódik!

```python
>>> divide ('2','1')
A finally ág mindenképpen végrehajtódik.
Traceback (most recent call last):
  File "<pyshell#12>", line 1, in <module>
    oszd_el ('2','1')
  File "D:/kivétel.py", line 3, in oszd_el
    result = x / y
TypeError: unsupported operand type(s) for /: 'str' and 'str'
```
