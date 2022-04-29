Title: Python-programozás 4 Elágazások és ciklusok
Published: 2013-01-27 02:47
Author: molnardenes
Tags: [alapok]
---

Mostanra megtanultuk az alapvető számítási műveleteket, elmenteni
programunkat, alapokeket írni, de mindezt csak **szekvenciálisan**
(vagyis egymás után, szépen sorban, ahogy a sorok következnek) tudjuk
végrehajtani. Hasznos lenne ugyanakkor, ha bizonyos feltételektől
függően megmondhatnánk a gépnek, hogy mit csináljon. Erre szolgálnak az
**elágazások**, vagy másként **szelekciók**. Az elágazás feltétele egy
logikai kifejezés, aminek az igaz/hamis értékétől függ, hogy a program
melyik utasítást hajtja végre.

[![Elágazás](/assets/images/elagazas.png)

Ahogyan az ábrán is látszik, amennyiben a feltételünk igaznak bizonyul,
az igaz ágban lévő kódunk fog lefutni, amennyiben hamis, úgy a hamis ág
hajtódik majd végre. Ennek a hamis ágnak a megléte nem feltétlenül
szükséges, erre csak akkor van szükség, ha hamis érték esetén is
szeretnék, ha végrehajtana egy utasítást a programunk.

Nézzük meg most ezt, hogyan néz ki a Pythonban:

```python
x = input("Adj meg egy számot: ")
x = int(x)
if x % 2 == 0:
    print("Páros")
else:
    print("Páratlan")
```

Vegyük végig sorról sorra, hogy mit is csináltunk: Elsőként bekértünk
egy számot a felhasználótól, majd azt az előző posztban tanult *int()*
függvénnyel integerré alakítottuk (ugye emlékszünk még arra, hogy a
beolvasott adat típusa string, ezért ha számolni szeretnénk egy bekért
számmal, azt át kell alakítanunk valamilyen numerikus értékké a korábban
ismertetett függvények valamelyikével).A következő sor az igazán érdekes
számunkra. Amint megfigyelhető, a feltételes elágazást az *if* kulcsszó
vezeti be, amit a feltétel követ. Esetünkben ez nem más, mint a
beolvasott számot kettővel osztva nullát kapunk maradékul. Vagyis a
számunk kettővel osztható, azaz páros. A másik ágat az *else* vezeti be.
Ha a feltételünk nem igaz, akkor hajtódik végre ez.

Még két dologra figyeljünk fel! A feltételhez tartozó utasítások
indentálva vannak, innen tudja a program, hogy meddig tartanak az egyes
feltételekhez tartozó utasítások. A feltétel fejrésze kettősponttal
záródik, innen tudja a Python, hogy eddig tartott a feltétel, ezután
pedig az utasítások következnek.

Lehetőségünk van a feltételeket egymás ágyazni. Nézzünk erre is egy
példát:

```python
if x % 2 == 0:
    if x % 3 == 0:
        print("Osztható 2-vel és 3-mal is.")
    else:
        print("Osztható 2-vel, de 3-mal nem.")
elif x % 3 == 0:
    print("Osztható 3-mal, de 2-vel nem.")
else:
    print("Sem 2-vel, sem 3-mal nem osztható.")
```

Ebben az esetben a programunk megvizsgálja, hogy x osztható-e 2-vel. Ha
igen, akkor belép az igaz ágba, ahol megvizsgálja, hogy osztható-e 3-mal
is. Amennyiben igen, akkor megkapjuk, hogy 2-vel és 3-mal is oszthatü
igaz. Ha 3-mal nem osztható, akkor arról tájékoztat a programunk. Ha
azonban a megadott szám nem osztható 2-vel, akkor a hamis ágba lépünk,
ahol azt vizsgáljuk, hogy oszható-e 3-mal. Ha igen, akkor tájékoztat
minket a program, hogy 3-mal igen, 2-vel azonban nem osztható számunk. A
feltételben szereplő *elif* az else if rövidítése. Az utolsó ágba akkor
jutunk, ha a megadott szám sem 2-vel, sem 3-mal nem osztható.

------------------------------------------------------------------------

Az eddigi bejegyzésekben megismerkedtünk a szekvenciákkal, most pedig az
elágazásokkal. Mindkét esetben egyetlen egyszer hajtjuk végre az
utasításokat. Mi a helyzet akkor, ha egy utasítást kétszer, háromszor
vagy esetleg százszor kell végrehajtani. Nem volna szerencsés, ha
ezekben az esetekben egymás után kétszer, háromszor vagy százszor le
kellene másolni az eredeti kódunkat.

[![ciklus](/assets/images/ciklus.png)

Ebben a programunk megvizsgál egy feltételt, és ha igaz a visszatérési
értéke, akkor végrehajtja a **ciklusmagot**, ami a ciklus végrehajtandó
utasításait tartalmazza. Mindezt egészen addig ismétli, amíg a
feltételünk hamissá nem válik. Ekkor kilép a ciklusból, és fut tovább. A
ciklusokhoz használt kulcsszó a *while* lesz. Nézzünk erre is egy
példát:

```python
x = 5
itersLeft = 5
while itersLeft != 0:
    x = x * 5
    itersLeft = itersLeft - 1
    print(x)
```

Vegyük végig egy táblázat segítségével az egyes értékek változását!


|x     |itersLeft|végrehajtódik?|
|------|-|-------------------------|
|5     |5|mivel itersLeft > 0, igen|
|25    |4|mivel itersLeft > 0, igen|
|625   |3|mivel itersLeft > 0, igen|
|3125  |2|mivel itersLeft > 0, igen|
|15625 |1|mivel itersLeft > 0, igen|
|-     |0|ez már nem hajtódott végre, hiszen itersLeft = 0|

A ciklusok esetében a következőkre figyeljünk oda: Először is a ciklusok
kívül deklarálnunk kell egy **ciklusváltozót** (ez a példánkban az
itersLeft), majd ezt a változót tesztelni kell, hogy megfelel-e a
feltételnek vagy sem. Végül pedig a cikluson belül meg kell változtatnom
az értékét.

------------------------------------------------------------------------

Iteráció segítségével állapítsuk meg egy egész számról, hogy van-e egész
gyöke, és ha van, akkor mi az. A következő példában ún. **találgatás és
ellenőrzés** (**guess and check**) algoritmust fogjuk használni. A
módszer a következő:

1.  Találgatással próbáljuk megkeresni egy x szám köbgyökét, mégpedig úgy,
    hogy először megvizsgáljuk a 0**3 értékét, majd 1**3, 2**3, és így
	tovább egészen addig, amíg
2.  el nem érünk egy k számhoz, amely esetében k**3 > x

Lássuk a kódot:

```python
x = int(input("Adj meg egy számot: "))
result = 0
while result**3 < x:
    result = result + 1
if result**3 != x:
    print("A számnak nincs egész köbgyöke.")
else:
    print(x," köbgyöke ",result)
```

Teszteljük le a programunkat például az x = 27-tel. Amint láthatjuk, a
programunk először megvizsgálja a 0-t. 0**3 kisebb, mint 27, tehát a
programunk *inkrementálja* (megnöveli) a result változónk értékét
1-gyel. 1**3 kisebb, mint 27, 2**3 szintén. 3**3 az pont 27, újra
már nem fut le a programunk. A válasz tehát a következő lesz: 27
köbgyöke 3. Próbáljuk ki most mondjuk 25-tel! Ugyanígy végigfut, de a
válasz az lesz, hogy nincs egész köbgyöke. A programunk majdnem
tökéletesen működik, de mi a helyzet mondjuk -27 esetén? Írjuk át a
kódunkat ennek megfelelően!

```python
x = int(input("Adj meg egy számot: "))
result = 0
while result**3 < abs(x):
    result = result + 1
if result**3 != abs(x):
    print("A számnak nincs egész köbgyöke.")
else:
    if x < 0:
        result = -result
    print(x," köbgyöke ",result)
```

Így már egy tökéletesen működő programot kaptunk.

------------------------------------------------------------------------

Amint láttuk a **feltételes ciklusok** (**while ciklus**ok) egy
választási soron iterálnak végig, egészen addig, míg igazzá nem válik a
feltétel. Most ismerkedjünk meg a ciklusok egy másik fajtájával,
mégpedig a **számlálós ciklusokkal** (**for ciklusok**). A számlálós
ciklus egy felsorolható típus adott intervallumán léptet végig. Először
is nézzük meg a szintaxisát:

```python
for <azonosító> in :

```

A *for* kulcsszóval indítunk, majd megadunk egy tetszőleges azonosítót.
Ezt követi az *in* kulcsszó, végül pedig az a sorozat, amelyen
szeretnénk elvégezni a ciklust. Ne felejtsük el, hogy az egészet egy
kettőspont zárja. Ezután következik indentálva a ciklusmag. A számlálós
ciklusok addig futnak, amíg az adott intervallum végére nem érnek, vagy
a *break* utasítással ki nem lépünk a ciklusból. Ilyenkor a program
kilép, majd folytatja a futást a következő kódrészletnél.

Mielőtt átírnánk az előző köbgyökkereső programunkat for ciklussal,
ismerkedjünk meg a *range* függvénnyel. Jegyezzük meg, hogy a range
függvény elöl zárt, hátul nyitott intervallumot jelöl. Vagyis az első
elemét tartalmazza, de az utolsót nem. Tehát például a *range(n)* = [0,
1, 2, 3, ..., n-1]. Ha két argumentummal adjuk meg, akkor az első
intervallumtól tart a második mínusz egyig. Tehát *range(m, n)* = [m,
m+1, m+2, ..., n-1]. Most pedig nézzük meg végre, hogyan néz ki for
ciklussal az előző köbgyökkeresőnk:

```python
x = int(input("Adj meg egy számot: "))
result = 0
for result in range(0, abs(x)+1):
    if result**3 == abs(x):
        break
if result**3 != abs(x):
    print("A számnak nincs egész köbgyöke.")
else:
    if x < 0:
        result = - result
print(x," köbgyöke ",result)
```
