Title: Python-programozás 6 Rekurzív algoritmusok
Published: 2013-02-03 00:31
Author: molnardenes
Tags: [algoritmus, recursion]
---

Az előző fejezetben megismerkedtünk az iteratív algoritmusokkal, most a
hasonló problémák megoldásának egy másik módját fogom bemutatni. Ez a
rekurzió. A **rekurzió** egy programozási módszer. Azt az esetet
nevezzük így, amikor egy eljárásban szerepló kód önmagát hívja meg.
Fontos megjegyezni, hogy minden rekurzív algoritmus átalakítható nem
rekurzívvá, azaz létezik iteratív megoldása is.

A rekurzív algoritmusok esetében meg kell találnunk a legegyszerűbb
esetet (alapeset), amelyben a megoldás magától értetődik, majd találnunk
kell egy olyan ismételt egyszerűsítési folyamatot (indukciós lépés),
mely alapján véges lépésben eljuthatunk a legegyszerűbb esethez. Minden
egyes lépésnél feltételezzük, hogy a következő, egyszerűbb esetnek már
megvan a megoldása.

Az egyik legegyszerűbb példa rekurzióra a faktoriális értékének
kiszámítása. Az *n* faktoriálisa (jelölése: *n!*) alatt az első n darab
pozitív egész szám szorzatát értjük, *n = 1* esetén pedig *1*-et. Hogyan
oldjuk meg ezt rekurzív algoritmussal? Először is szükségünk van egy
alapesetre. Ez most az *n = 1* lesz. Az indukciós lépés *n &gt; 1*
esetén *n * (n-1)!*. Írjuk meg most az algoritmusunkat iteratívan és
rekurzívan is!

Az iteratív megoldás:

```python
def factorialIterativeVersion(n):
    result = 1
    while n > 1:
        result = result * n
        n -= 1
    return result

```

A rekurzív megoldás:

```python
def factorialRecursiveVersion(n):
    if n == 1:
        return 1
    return n * factorialRecursiveVersion(n-1)

```

------------------------------------------------------------------------

Az előzőek alapján felmerülhet a kérdés, hogy mi értelme rekurzívan
végrehajtani egy problémát, hiszen iterációval is könnyen és gyorsan
eredményt kapunk. Nézzünk most egy olyan problémát, ami nagyon könnyen
megoldható rekurzív módon, míg iterációval rendkívül nehéz volna
megoldást találni.

Ez a probléma a Hanoi tornyai nevű matematikai feladvány. A játék
szabályai szerint az első rúdról az utolsóra kell átrakni a korongokat
úgy, hogy minden lépésben egy korongot lehet áttenni, nagyobb korong nem
tehető kisebb korongra, és ehhez összesen három rúd áll rendelkezésre.
Egy legenda szerint a világ megteremtésekor egy 64 korongból álló Hanoi
torony feladványt kezdtek el játszani Brahma szerzetesei. A szabályok
azonosak voltak a ma ismert Hanoi torony szabályaival. A legenda szerint
amikor a szerzetesek végeznek majd a korongok átjuttatásával a harmadik
rúdra, a kolostor összeomlik, és a világunk megszűnik létezni.

Nézzük meg a feladvány egy egyszerűbb változatát 4 koronggal:

![Hanoitornyai](/assets/images/tower_of_hanoi.gif)

A feladványban *n* korongot szeretnék mozgatni. Bontsuk egyszerűbb
formára a problémánkat. Elsőként *n - 1* korongot átmozgatok a tartalék
rúdra, majd a legalsót átmozgatom a célrúdra. Ezután az egészet átrakom
a célrúdra. Az algoritmus szerint itt megoldunk egy kisebb problémát (n
- 1 korong átmozgatása), majd megoldjuk az alapesetet (a legalsó
átmozgatása a célrúdra), végül újra egy kisebb problémát oldok meg
(áthelyezem a többit a célrúdra). Nézzük meg, hogy néz ki ez Pythonban:

```python
def towers(n, fromWhere, toWhere, stack):
    if n == 1:
        print(str(fromWhere) + '. rúdról átmozgatjuk a ', str(toWhere) + '. rúdra')
    else:
        towers(n-1, fromWhere, stack, toWhere)
        towers(1, fromWhere, toWhere, stack)
        towers(n-1, stack, toWhere, fromWhere)
```

Az alapesetben (n = 1) egyszerűen kiíratom a lépést, honnan mozgatom
hová a korongot. Egyéb esetben egy kisebb rakást (egyet hagyok alul)
átrakok a tartalék rúdra. Az ott maradt egy korongot átrakom a célrúdra.
Végül a kisebb rakást átrakom a célrúdra.

A legenda szerinti 64 korong esetében a megoldás 18 446 744 073 709 551
615 lépés lenne. Ha egy szerzetes éjjel-nappal dolgozna a feladaton
másodpercenként egy lépést végrehajtva, akkor kicsit több mint 580
milliárd év alatt tudná megoldani a feladatot.

------------------------------------------------------------------------

A következő problémának az adja az érdekességét, hogy az eddigiektől
eltérően nem 1, hanem 2 alapesete van. Az ún. Fibonacci-számok leírása
Leonardo di Pisától (Fibonacci) származik. Liber Abaci című művében egy
képzeletbeli nyúlcsalád növekedését adta fel feladatként: hány pár nyúl
lesz n hónap múlva, ha feltételezzük, hogy az első hónapban csak
egyetlen újszülött nyúlpár van, az újszülött nyúlpárok két hónap alatt
válnak termékennyé, és minden termékeny nyúlpár minden hónapban egy
újabb párt szül (a nyulak örökké élnek)?

  Hónap  |Nőstények
  -------|-----------
  0      |1
  1      |1
  2      |2
  3      |3
  4      |5
  5      |8
  6      |13

A táblázatból látszik, hogy ezúttal két alapesetünk van. *Nőstények(0) =
1* és *nőstények(1) = 1*. A 0. és az 1. hónapban 1-1 nőstényünk van. A
többi hónapra a táblázat alapján felírhatunk egy általános formulát, ami
a következőképpen néz ki: *nőstény(n) = nőstény(n - 1) + nőstény(n -
2)*. A kód egyszerű és nagyszerű:

```python
def fibonacci(x):
    assert type(x) == int and x >= 0
    if x == 0 or x == 1:
        return 1
    else:
        return fibonacci(x-1) + fibonacci(x-2)
```

Mielőtt elemeznénk a kódot, vegyük észre, hogy egy új utasítást
használtunk, az *assert*et. Az **assert** utasítás talán a
legalkalmasabb arra, hogy hibát keressünk a programunkban. Az utasítás
után két feltételt is megadtunk, egyrészt, hogy *x* egész szám, és
nagyobb vagy egyenlő, mint *0*. Amennyiben mindkét feltétel igaz, a
programunk folytatja a futást, ha azonban valamelyik feltétel nem
teljesül, akkor az *assert* utasítás miatt a program futása leáll, és
hibaüzenetet ad.

A programunk tovább részében először a két alapesetet vizsgáljuk meg,
majd pedig két rekurzív meghívást (x - 1 és x - 2).

------------------------------------------------------------------------

Eddig numerikus adatokkal dolgoztunk, de stringekre is alkalmazhatók
rekurzív algoritmusok. A következő példában egy olyan programot írunk,
ami egy adott szövegről megállapítja, hogy palindrom-e. Egy szöveg akkor
palindrom, ha visszafelé olvasva is ugyanazt kapjuk. Ilyen például a
következő kifejezés: Indul a pap aludni.

Mielőtt hozzáfognánk a rekurzív megoldáshoz, előtte szükséges, hogy
megszabadítsuk a szöveget mindenféle központozástól, és megszüntessük a
kis- és nagybetűk közötti különbséget. Ha ezzel megvagyunk, akkor meg
kell keresünk az alapesetet. Ebben az esetben ez nem más, mint hogy 0
vagy 1 db karakter palindrom. A rekurzív esetben megvizsgálom, hogy az
első és az utolsó karakter egyezik-e, ha igen, akkor attól függ, hogy
palindrom-e, hogy az első és az utolsó karakter közötti szöveg
palindrom-e. Nézzük a kódot:

```python
def isPalindrom(s):
    def toChars(s):
        s = s.lower()
        result = ''
        for c in s:
            if c in 'aábcdeéfghiíjklmnoóöőpqrstuúüűvwxyz':
                result = result + c
        return result

    def isPal(s):
        if len(s) <= 1:
            return True
        else:
            return s[0] == s[-1] and isPal(s[1:-1])

    return isPal(toChars(s))
```

Létrehoztunk egy *isPalindrom* nevű függvényt. Az *isValami* elnevezés a
bevett gyakorlat olyan esetekben, amikor egy függvény visszatérési
értéke igaz vagy hamis. Szokjuk meg ezt, hibát nem fog dobni a program,
ez csak egy konvenció, de nem lesz bajunk belőle, ha betartjuk.

Az *isPalindrom* függvényen belül létrehoztunk két másik, úgynevezett
belső függvényt. Ez a két belső függvény kívülről nem érhető majd el,
csak az isPalindromon belülről. Vizsgáljuk meg elsőként a *ToChars*
függvényünket. Ez annyit csinál, hogy a szöveget megszabadítja a
fölösleges írásjelektől, és minden karakterét kisbetűvé alakítja. Ez
utóbbit az *s* stringen alkalmazott **lower()** metódussal teszi. A
metódusokról a későbbiekben lesz majd szó. Létrehoztunk tehát egy
*eredmeny* változót, amibe majd a "lecsupaszított", kisbetűssé alakított
szöveget fogjuk tárolni. Egy *for ciklussal* végigmegyünk az *s* string
minden egyes karakterén, és amelyik eleme az általunk létrehozott
karakterlistának ('aábcdeéfghiíjklmnoóöőpqrstuúüűvwxyz'), azt átmásoljuk
az *eredmeny* változóba.

A palindromvizsgálat az *isPal* függvénnyel történik. Ha a szöveg hossza
&lt;= 1, akkor biztosan palindrommal van dolgunk. Ha pedig az első
karakter (0.) megegyezik az utolsóval (-1), akkor a köztük lévő
karaktereket vizsgáljuk, hogy palindromok-e.
