Title: Python-programozás 1 Alapfogalmak
Published: 2012-11-03 00:08
Author: molnardenes
Tags: [debug, IDE, OOP]
---

Hosszú tervezgetés után elhatároztam, hogy végre megírok
egy **Python** programozási útmutatót. Az első néhány bejegyzésben
megismerkedhetünk az alapokkal, a későbbiekben pedig igyekszünk egyre
mélyebben elmerülni a Python rejtelmeiben.  Mielőtt erre sort
kerítenénk, szeretném, ha megismerkednénk a programozás alapvető
fogalmaival, illetve választott programozási nyelvünk tulajdonságaival.

A **Python** egy általános célú, magas szintű programozási nyelv. Itt
rögtön két ismeretlen fogalommal találkoztunk. Tisztázzuk is őket
gyorsan: egy programozási nyelv **általános célú**, ha széleskörűen
tudjuk használni szoftverek írására, vagyis nem egy-egy szakterület
speciális igényeit elégíthetjük ki vele. Ilyen általános célú nyelvek
még a C, C++, C\#, Java, JavaScript, PHP, hogy csak a legismertebbeket
említsük. A **magas szintű **programozási nyelvek jellemzője az
absztrakció, elvonatkoztatottság, a könnyebb használhatóság és
esetenként a platformfüggetlenség.

A Python többféle programozási paradigmát is támogat, ezek a
funkcionális, az objektumorientált és az imperatív. A **programozási
paradigma** alapvetően a program felépítésére használt eszközkészletet
jelenti, azaz azt, hogy milyen egységekből épül fel a
program. A **funkcionális nyelvek** nem alkalmaznak változókat,
 többnyire ciklusokat sem, illetve értékadó utasítás sincs bennük. A
program minden esetben egy függvény visszatérési értékét adja meg.
Az **objektumorientált programozás** olyan programozási módszer, melyben
a problémát a valódi világ tárgyaihoz (objektumaihoz) hasonlóan önálló,
zárt, de egymással interakcióra képes elemekre bontva adjuk oldjuk meg.
Az **imperatív nyelvek** közös jellemzője, hogy a program fejlesztése
értékadó utasítások megfelelő sorrendben történő kiadására
koncentrálódik. Az értékadó utasítások többszöri végrehajtásának célját
szolgálják a ciklusok, az elágazások (szelekciók) pedig akkor
szükségesek, ha egy utasítást csak adott esetben kell végrehajtani.

Programozási nyelvünk egy úgynevezett** interpreteres nyelv**, azaz,
nincs különválasztva a forrás- és a tárgykód. A program azonnal
futtatható, ha rendelkezünk Python **értelmezővel**. Az értelmező nem
más, mint egy olyan program, ami képes arra, hogy az általa felismert
nyelv utasításait a futtató számítógép utasításkészletének megfelelő
utasításokká alakítsa át, majd ezeket azonnal futtassa is.

------------------------------------------------------------------------

Ezután a kis elméleti bevezető után telepítsük fel a Python programunkat
a gépre. A legújabb, 3.3 verziót fogjuk használni, amit következő címen
tölthetünk le: <http://python.org/download/releases/3.3.0/>. Letöltés,
és telepítés után készen is áll a használatra. A Python saját fejlesztői
környezetét (IDE) fogjuk használni, az IDLE-t. Az **intergált fejlesztői
környezet** (**IDE**) egy olyan program, amely jelentősen megkönnyíti a
programozást, esetenként automatikusan kiegészíti az általunk elkezdett
utasításokat.

Ha mindent jól csináltunk, akkor a következő képnek kell minket
fogadnia:

![Python
shell](/assets/images/py_shell.jpg)

A most látott ablakot úgy hívjuk, hogy shell. A* >>>* jel neve
prompt. A Python utasításait a prompt után gépelhetjük, majd a végén az
<Enter> leütésével végrehajtódnak.

Próbáljuk is ki rögtön. Az első néhány feladatban a Pythont
számológépként fogjuk használni. Nézzük a következő kifejezéseket:

```python
>>> 2 + 2
4
>>> 6 - 4
2
>>> 4 * 5
20
>>> 8 / 2
4.0
>>> 8 // 5
1
>>> 8 % 5
3
>>> 2 ** 5
32
```

Most értelmezzük a fenti eredményeket. Az első eset egyértelműnek
látszik, összeadtunk két számot és visszakaptuk az eredményt. A kivonás
és a szorzás is hasonlóan egyszerű. Az osztásnál azonban
megfigyelhetjük, hogy ahelyett, hogy szimplán 4-et kapnák eredményül,
4.0-t kapunk. Osztás esetén az eddigi egész szám helyett egy új,
úgynevezett lebegőpontos számot kapunk vissza. Ezzel meg is ismerkedtünk
a Python két numerikus típusával, melyek az **egész** (**integer**) és
a **lebegőpontos** (**float**).

Érdekes lehet még a // és a % jelek használata. Az első nem más, mint az
egész osztás, azaz végeredményünk a hányados egész részre lesz (pl.
esetünkben 8 // 5 = 1.6, és mi ennek az egész részét kaptuk vissza, ami
az 1). A % (modulo) a maradékos osztás, vagyis a művelet elvégzése utáni
maradékot adja eredményül (a példában a 8 / 5 = 1 és a maradék 3). A
hatványozás jele a **.

Vizsgáljuk meg most a következő példákon keresztül a műveletek
sorrendjét:

```python
>>> 5 + 2 * 4
13
>>> 3 * 2 ** 4
48
>>> 4 ** (5 - 3)
16
>>> -10 * 2 ** 3 + 1
-79
>>> 6 + (12 * (4 + (6 ** 2 - 1))) - 9
465
```

Az első műveleti sornál láthatjuk, hogy elsőként a szorzást végzi el,
majd a szorzatértékhez adja hozzá az 5-öt. A második esetben elsőként a
hatványozást, és csak utána a szorzást. A többi esetet vizsgálva
megfigyelhetjük, hogy a műveletek sorrendje a matematikában megszokott
módon alkalmazódik a Pythonban is.

A műveleti jelek után vegyünk sorra az összehasonlító operátorokat. Ezek
a következőképpen néznek ki:

   operátor | operátor jelentése
  ----------|-----------------------------
  x **>** y |x nagyobb mint y
  x **<** y |x kisebb mint y
  x **>=** y|x nagyobb vagy egyenlő mint y
  x **<=** y|x kisebb vagy egyenlő mint y
  x **==** y|x egyenlő y
  x **!=** y|x nem egyenlő y
  

------------------------------------------------------------------------

![Python
shell](/assets/images/mem1.jpg)

A bejegyzés végén még szeretném, ha megismerkednénk a Python
memóriakezelésével. A különböző értékek és változók a memória különböző
területein helyezkednek el. Szerencsénkre nekünk a memória
lefoglalásával nem kell foglalkoznunk, elvégzi helyettünk a Python. Az
alábbi ábrán jól látható, hogy a különböző változók és értékek hogyan
helyezkednek el.

Az **értékek** (**values**) a memóriában tárolódnak. A memóriára
tekinthetünk úgy, mint tárolóhelyek listájára. Minden tárhely egy egyedi
azonosítóval rendelkezik, ezt nevezzük **memóriacímnek** (**memory
address**). A memóriacímek elé x-et írunk, hogy megkülönböztessük a
többi számtól őket. Az x201 a 201-es memóriacímet jelöli.

A programoknak tudniuk kell követni a különböző értékeket. Ehhez az
ún. **változókat** (**variables**) használják. A változó egy érték
tárolására alkalmas hely a memóriában.

![Python
shell](/assets/images/mem2.jpg)

Példánkban a shoe_size nevű változó tárolja az x34 memóriacímet. Ez azt
jelenti, hogy a shoe_size nevű változó a 8,5 értékre mutat.

-   Egy értéknek van memóriacíme.
-   Egy változó egy memóriacímet tárol.
-   Egy változó egy értékre mutat/hivatkozik.

------------------------------------------------------------------------

Eddig megismerkedtünk néhány programozási alapfogalommal és a Python
nyelv jellemzőivel. Most igyekszem bemutatni a hibák típusait, majd a
változókról és értékadásról, valamint típusadásról lesz még szó.

A programozás során biztosra vehető, hogy jó pár hibát fogunk véteni. A
programozási hibákat **bugoknak** nevezzük, és háromféle típusukat
különböztetjük meg. Az első típus az ún. **szintaktikai hiba**. Ez olyan
esetekben fordul elő, amikor nem tartjuk be az adott nyelv
**szintaxisát**. Egy programozási nyelv szintaxisa azt a
szabályrendszert jelenti, amely meghatározza, hogy az adott nyelvben
hogyan lehet az egyes nyelvi elemeket, utasításokat létrehozni. A hibák
második típusát **szemantikai hibának** vagy logikai hibának nevezzük.
Ebben az esetben a program végrehajtódik abban az értelemben, hogy nem
kapunk semmilyen hibaüzenetet, de az eredmény nem az lesz, amit vártunk.
Amit megadtunk a programnak, hogy végrehajtson, az nem az, mint amit
szerettünk volna. A szemantika (logika), amivel felépítettük a
programunkat, nem volt helyes. A logikai hibák keresése nehezebb
feladat, az outputok (visszakapott eredmények) elemzése segíthet a
felderítésükben. Hibák bekövetkezhetnek végrehajtás közben is. Az ilyen
esetekben fellépő hibákat **run-time errornak** nevezzük. Ezek akkor
lépnek fel, ha a program működése során speciális körülmények állnak
elő. Ilyen lehet pl., ha a program egy már nem létező fájlra hivatkozik.
Ezeket a hibákat sokszor **kivételeknek** (**exception**) nevezzük, mert
ezek általában azt jelzik, hogy valami kivételes történt, amit nem
láttunk előre. Rendkívül fontos, hogy a programozás tanulása során
elsajátítsuk a hatékony **hibakeresés** (**debugolás**) képességét.

------------------------------------------------------------------------

Az előző alkalommal megtanultuk, hogy a változó nem más, mint egy érték
tárolására alkalmas hely a memóriában. A változók elnevezésében
meglehetősen nagy szabadságot kapunk. Ennek ellenére törekedjünk arra,
hogy minél kifejezőbb neveket válasszunk. Például a hossz nevű változó
alkalmasabb a hosszúság kifejezésére, mint az x.

Néhány szabálya azonban van a változónév-adásnak:

-   A változónév ékezet nélküli kis- és nagybetűket, számokat és az
    _ (alázhúzás) karaktereket tartalmazhatja.
-   A változók neveinek mindig betűvel vagy _ kell kezdődniük.
-   A kis- és nagybetűk különbözőnek számítanak. Ez a gyakorlatban azt
    jelenti, hogy a Hossz, HOSSZ és a hossz különböző változók. Ezért
    fokozottan ügyeljünk a névadásnál!
-   Végül pedig lehetőség szerint használjunk angol változóneveket lehetőség
    szerint

Én azt tanácsolom, hogy a változókat mindig kisbetűvel írjuk (az első
betűt is), nagybetűket csak összetett szavak határain használjunk a
szavakon belül (pl. variableNames). Még egy utolsó szabály van a
névadásra nézve, ez pedig nem más, minthogy nem használhatjuk a Python
által lefoglalt szavakat, melyek a következők:

*and, as, assert, break, class, continue, def, del, elif, else, except,
exec, finally, for, from, global, if, import, in, is, lambda, not, or,
pass, print, raise, return, try, while, with, yield*

A következőkben bemutatom, hogyan definiálhatunk egy változót, és hogyan
rendelhetünk hozzá egy értéket. A **hozzárendelést** (**értékadást**) a
Pythonban az egyenlőségjel segítségével hajtjuk végre a következő módon:

```python
>>> length = 2300
>>> text = 'Ez egy szövegrész'
>>> e = 2.718281
```

Ezekkel az értékadásokkal egyszerre több művelet is történt.
Létrehoztunk egy változót és bejegyeztük a memóriába. Az értékadással
egy jól meghatározó típust is kapott a változónk, illetve a pontos
értéke is rögzítve lett a memóriában.

Miután eltároltuk a megfelelő értékeket a változóinkban, lehetőségünk
van ezeket a változókat kiíratni a képernyőre. Ezt kétféle módon
tehetjük meg. Az első lehetőség, hogy beírjuk a változó nevét, majd
<Enter>.

```python
>>> length
2300
>>> text
'Ez egy szövegrész'
>>> e
2.718281
```

A másik lehetőségünk a **print()** függvény (a függvényekkel a következő
bejegyzésben foglalkozunk majd) használata:

```python
>>> print(text)
Ez egy szövegrész
```

Ha jól megfigyeltük, akkor észrevehetünk egy kis különbséget a két
módszer között. A print() függvény szigorúan a változó értékét írja ki,
míg az első módszer esetében az idézőjeleket is megjeleníti, hogy
emlékeztessen a változó típusára.

Még egy dologra szeretném felhívni a figyelmet: A változók értékadása
előtt nem határoztuk meg azok típusát. Erre a Pythonban nincs is
szükség, ugyanis a változó azzal a típussal jön automatikusan létre, ami
az értéknek leginkább megfelel. Az ilyen programozási nyelveket
**dinamikus típusadású nyelveknek** nevezzük.

Lehetőségünk van többszörös és párhuzamos értékadásra is a következő
módokon:

```python
>>> a = b = 26
>>> a
26
>>> b
26
>>> a, b = 9, 6.987
>>> a
9
>>> b
6.987
```
