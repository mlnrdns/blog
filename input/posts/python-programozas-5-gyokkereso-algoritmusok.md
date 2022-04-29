Title: Python-programozás 5 Gyökkereső algoritmusok
Published: 2013-02-01 00:43
Author: molnardenes
Tags: [algoritmus]
---

Az eddig megtanult ismereteket felhasználva írjunk néhány gyökkereső
algoritmust! A Wikipedia alapján **gyökkereső algoritmusnak** nevezzük
azokat az algoritmusokat, amelyeket valamely f függvény x gyökeinek
meghatározására használunk, azaz olyan x-eket keresünk, melyekre
teljesül, hogy f(x) = 0. A feladat itt nem közvetlenül a zérushely,
hanem az azt egy adott pontossággal megközelítő eredmény meghatározása.
A gyökkereső algoritmusok akkor is használhatók, ha nem létezik
megoldóképlet.

A következő módszereket fogjuk alkalmazni: közelítés, felező módszer és
a Newton–Raphson-módszer. Nézzük meg elsőként a **közelítéses
módszert**:

```python
x = 64
e = 0.01
step = e ** 2
numberOfTries = 0
result = 0.0
while abs(result ** 2 - x) >= e and result <= x:
    result += step
    numberOfTries += 1
print("Próbálkozások száma: ", str(numberOfTries))
if abs(result ** 2 - x) >= e:
    print("Nem találtuk meg megközelítőleg ", str(x), " négyzetgyökét")
else:
    print(str(result), " megközelíti ", str(x), " négyzetgyökét")
```

Nézzük meg, hogy mi történik ebben az esetben. A ciklus két feltételét
szükséges egy kicsit jobban megvizsgálnunk. Mivel a közelítés értékét *e
= 0,01*-ben határoztuk meg, így, ha a kapott eredményünk négyzetének és
az eredeti számnak a különbsége ennék kisebb lesz, akkor nyert ügyünk
van. Amíg ez nincs így, addig folytatnunk kell a keresést. Ne felejtsük
el, hogy az *eredmeny* változónk értéke minden újabb kör után növekszik
a *lepes* változónk értékével. Emellett van egy másik feltételünk is,
nevezetesen, hogy az *eredmeny* értéke ne legyen nagyobb, mint x. Ez
ugyanis azt jelentené, hogy túl messzire jutottunk a vizsgálni kívánt
számtól.

A cikluson kívüli feltételünk szerint, ha az *eredmeny* változónk
négyzetének és az *x*-nek a különbsége nagyobb, mint *e*, akkor nem
tudtuk *e* nagyságrendre megközelíteni az *x* változó négyzetgyökét.
Ellenkező esetben sikerrel jártunk.

Futtassuk le most a programunkat! A következő eredményt kellett kapnunk:

Próbálkozások száma: 79994

7.999399999994695 megközelíti 64 négyzetgyökét

Amint láthatjuk közel 80 ezer próbálkozás után találtunk rá a megfelelő
eredményre. Ne feledjük, hogy most nem a pontos értékre voltunk
kíváncsiak, csupán közelíteni kívántuk egy bizonyos értékkel. Nagyon
fontos az *e* változónk megválasztása, hiszen, ha túl kis számot
választunk, akkor nagyon megnő a program futási ideje, ha viszont túl
nagyot, akkor pedig elképzelhető, hogy átlépjük a megfelelő eredményt.
Éppen ezek miatt ez a módszer több hibalehetőséget is tartogat, így nem
bizonyult túl megbízhatónak. Nézzünk meg egy másik módszert a
négyzetgyök meghatározására!

------------------------------------------------------------------------

A **felező módszer** lényege, hogy matematikai ismereteink alapján
tudjuk, hogy egy adott szám négyzetgyöke a 0 és a szám között lesz
valahol. Most ahelyett, hogy végigmennénk a teljes intervallumon,
megfelezzük azt, felveszünk egy értéket a közepén. Megnézzük, hogy ez
nagyobb, vagy kisebb, mint a négyzetgyök. Ezt úgy vizsgálhatjuk, hogy,
ha a négyzete nagyobb, mint az eredeti szám, akkor a négyzetgyököt a
kisebb intervallumban kell keresnünk. Innentől már csak az első felével
foglalkozunk az intervallumnak. Megfelezzük ezt is, és újra elvégezzük
az előző vizsgálatot. Az új szakaszt megfelezzük majd ismét, mindezt
addig ismételve, míg meg nem kapjuk az eredményünket. Nézzük meg, hogyan
néz ki ez Pythonban:

```python
x = 64
e = 0.01
step = e ** 2
numberOfTries = 0
lowest = 0.0
highest = x
result = (lowest + highest) / 2.0
while abs(result ** 2 - x) >= e:
    print("Alsó határ: ", str(lowest), " Felső határ: ", str(highest)," Eredmény: ", str(result))
    numberOfTries += 1
    if result ** 2 < x:
        lowest = result
    else:
        highest = result
        result = (lowest + highest) / 2.0
print("Próbálkozások száma: ", str(numberOfTries))
print(str(result), " megközelíti ", str(x), " négyzetgyökét")
```

Futtassuk le a programunkat! Az eredmény az előzőhöz képest igencsak
kedvező! Az előző 80 ezer próbálkozáshoz képest, most mindösszesen 2
próbálkozásra volt szükség. Elmondhatjuk tehát, hogy a felező módszer
radikálisan csökkenti a számítási időt. A későbbiekben ennek a
módszernek nagy hasznát fogjuk venni.

------------------------------------------------------------------------

Harmadik módszerünk a **Newton - Raphson módszer**. Ebben az esetben is
először megadjuk, hogy milyen közel szeretnénk kerülni az eredményhez,
majd egy kezdőtippet adunk, ami most ebben az esetben a szám fele. A
ciklus feltételében ismét azt vizsgáljuk, mint eddig, vagyis, hogy elég
közel kerültünk-e a megoldáshoz. Newton pedig azt mondta, hogy vegyük a
tippünket, vonjuk ki belőle a hányadosát a tippünk négyzetének és a
számunknak a különbségének a tippünk négyzetének deriváltjával (vagyis a
tippünk kétszeresével). Ez így elsőre bonyolultan hangzik, de nézzük a
kódot, és rögtön világosabb lesz:

```python
e = 0.01
x = 64.0
guess = x / 2.0
while abs(guess * guess - x) >= e:
    guess = guess - (((guess ** 2) - x)/(2 * guess))
print(str(x), " négyzetgyöke körülbelül ", str(guess))
```
