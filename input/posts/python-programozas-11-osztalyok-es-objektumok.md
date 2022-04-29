Title: Python-programozás 11 Osztályok és objektumok
Published: 2013-03-22 21:16
Author: molnardenes
Tags: [OOP]
---


Ebben a bejegyzésben igyekszünk megismerkedni az objektumorientált
programozás alapfogalmaival. A programozásban az **objektum**
tulajdonságokkal és viselkedésekkel felruházott egység. Az objektumok
tulajdonságait **attribútumoknak**, tevékenységeit **metódusoknak**
nevezzük. Ha például egy tanulóra gondolunk, akkor a tulajdonságai,
attribútumai a neve, életkora, magassága, és olyan tevékenységeket képes
végezni, mint, hogy eszik, tanul, alszik, stb. Ezek a tulajdonságok és
tevékenységek azonban nem csak egy bizonyos tanulóhoz köthetők, hanem az
összes többihez is. Az objektum tehát egy kategóriába, típusba sorolható
be, amit osztálynak nevezünk. Úgy is fogalmazhatunk, hogy az **osztály**
egy minta, ami alapján objektumokat hozunk létre.

![osztaly](/assets/images/oop_osztaly.jpg)

Az osztály és az objektum fogalmak mellett meg kell ismerkednünk az
objektumorientált programozás alapkoncepcióival, vagy másként a három
pillérrel. Ezek az **egységbezárás** (**encapsulation**), **öröklődés**
(**inheritance**) és a **többalakúság** (**polymorphism**).

Az **egységbezárás** azt jelenti, hogy a procedurális megközelítéssel
ellentétben az adatok és a függvények a program nem különálló részét
képezik, hanem azok egy egységes egészként jelennek meg, összetartoznak:
együtt alkotnak egy objektumot. Az objektum egy vagy több felületen
keresztül érhető el a külvilág, vagyis a többi objektum számára. Ezt a
felületet **interfésznek** (**interface**) hívjuk. Az interfész
meghatározza azokat a szolgáltatásokat, amelyeket az objektum biztosít a
külvilág számára. Az interfészek tulajdonképpen azok a metódusok,
amelyeket más objektumok meghívhatnak.

Az **öröklődés** során származtatással hozunk létre egy már meglévő
osztályból egy újat, amely rendelkezni fog az ős minden tulajdonságával,
és nekünk csak a kívánt plusz funkciókat kell megvalósítanunk.
Gondoljunk például egy sakk-készletre. Összesen 32 bábunk van, viszont
összesen csak 6 különböző típusú bábu. Az összes bábuosztálynak vannak
azonos tulajdonságai, például a színük vagy, hogy melyik sakk-készlethez
tartoznak. Ugyanakkor az egyes típusok saját tulajdonságokkal is
rendelkeznek, más az alakjuk, másként, esetleg többet léphetnek. Hogyan
örökíthetjük az egyes típusokat a *figura* osztályunkból? A *figura*
osztály tulajdonságai a színe és hogy melyik sakk-készlet eleme. A
király, a vezér, a bástya, a futó, a huszár és a gyalog megörökölték a
*figura* osztály tulajdonságait, vagyis a színt és a készlethez
tartozás. Emellett azonban már saját tulajdonságokkal is rendelkeznek,
hiszen más-más alakúak és a lépéseket is másként tehetjük meg velük.

A **polimorfizmus** vagy többalakúság lehetővé teszi, hogy különböző
viselkedésmódokkal ruházzuk fel az egymásból származtatott objektumokat.
Ez első lehet, hogy kicsit nehezebben érthető, úgyhogy nézzünk erre is
egy példát. Vegyük elő újra a sakk-készletünket! A *tábla*
objektumunknak nem kell tudnia azt, hogy most melyik bábuval lépünk,
elégséges, ha annyit kér, hogy az aktuális bábu lépjen. Ekkor a
megfelelő alosztály intézkedik a lépésről, legyen az éppen egy futó,
vagy akár a király. Sőt, ha van egy kacsa objektumunk, ami rendelkezik a
lépés metódussal, akkor akár a kacsa is léphet a sakktáblán. Ez a
jelenség a **duck typing**.

------------------------------------------------------------------------

Készítsük el az első osztályunkat!

```python
class Pont:
    pass
```

Osztály létrehozása a **class** kulcsszóval kezdjük, majd megadjuk a
kívánt osztálynevet, végül kettősponttal zárjuk a sort (hasonlóan, mint
amikor függvényeket definiáltunk, csak ott ugye a def kulcsszót
használtuk). Megegyezés szerint az osztályneveket nagybetűvel kezdjük! A
következő sorok indentálásáról ne feledkezzünk meg! Az osztályunk nem
csinál semmit, úgyhogy adjuk neki a **pass** kulcsszót. Tevékenységet
nem végez, ott használjuk kiegészítésként, ahol legalább egy
kifejezésnek kellene állnia.

A *Pont* osztályból **példányosítással** (**instanciation**) hozhatunk
létre új objektumokat. Jelölése a függvényhívásokhoz hasonlóan történik:

```python
>>> pont = Point()
>>> print(pont)
<__main__.Pont object at 0x02E39490>
```

A fenti példában tehát létrehoztunk egy *Pont* osztályba tartozó *pont*
nevű objektumot. Ha meghívjuk a print függvényt, és átadjuk
paraméterként az objektumunkat, akkor megbizonyosodhatunk róla, hogy a
*pont* a főprogram szintjén (erről majd később lesz szó) definiált
*Pont* osztály egy példánya, és a memória egy jól definiált helyén
helyezkedik el.

A létrehozott osztályunk üres és nem csinál semmit. Ennek ellenére az
osztályból példányosított objektumokhoz tetszőleges tartalmat
rendelhetünk a dot notation, vagy pont operátoros minősített névmegadási
rendszer segítségével. Nézzük:

```python
>>> p1 = Point()
>>> p2 = Point()
>>> p1.x = 5
>>> p1.y = 4
>>> p2.x = 3
>>> p2.y = 6
>>> print(p1.x, p1.y)
5 4
>>> print(p2.x, p2.y)
3 6
```

Már attribútumokkal rendelkező objektumaink is vannak, ami fél siker, de
az objektumorientált programozás lényege az objektumok között
interakció. Szeretnénk, ha végre bizonyos tevékenységeket is
hozzáadhatnánk az osztályainkhoz. Hozzunk létre több metódust az
osztályunkon belül! Legyen egy, ami az origóra helyezi az egyik pontot,
egy másik segítségével helyezhessünk el egy pontot a koordinátarendszer
bármely pontján, és végül hozzunk létre egy harmadikat, ami két pont
objektum távolságát állapítja meg:

```python
import math
class Point:
    def place(self, x, y):
        self.x = x
        self.y = y
    def origo(self):
        self.place(0, 0)
    def distance(self, other_point):
        return math.sqrt((self.x - other_point.x)**2 + (self.y - other_point.y)**2)
```

A programunk elején importáljuk a *math* modult, hogy később
használhassuk majd a négyzetgyök-függvényt. A metódus definiálása
hasonlóan zajlik a függvényéhez. A *def* kulcsszóval után megadjuk a
metódus nevét, majd zárójelek közé helyezzük a paraméterlistát, végül
pedig kettősponttal zárjuk. A következő sor indentálással tartalmazza a
metódus kifejezéseit. A függvények és a metódusok között különbség, hogy
a metódusnak mindenképpen van legalább egy argumentuma. Ennek a
hagyományos elnevezése *self*. Osztályon belül a *self* mindig az
aktuális objektumot jelöli. Mi is pontosan ezt csináljuk például az
*origo* metódusban, amikor a *self* objektum *x* és *y* értékét
beállítjuk. Amikor a programunkból meghívjuk ezt a metódust, akkor
viszont nem kell majd használnunk a *self* argumentumot, a Python
gondoskodik erről helyettünk.

Az osztályunknak most három metódusa van. Az *elhelyez* metódus az *x*
és *y* koordinátákat kapja argumentumként, és beállítja ezeket a *self*
objektumon. Az *origo* nem mást csinál, mint *0, 0* koordinátára, azaz
az origóra helyezi a *self* objektumot, mégpedig az előző, *elhelyez*
metódus meghívásával. A *tavolsag* metódus a Pitagorasz-tétel
segítségével meghatározza a két pont közötti távolságot. Nézzünk néhány
példát a fenti osztály használatára:

```python
point1 = Point()
point2 = Point()

point1.origo()
point2.place(0,8)
print(point2.distance(point1))

point1.place(2,4)
print(point2.distance(point1))
print(point1.distance(point1))
```

A következő eredményeket kapjuk: *8.0*, *4.47213595499958*, *0.0*.
Láthatjuk, hogy a metódusok meghívása argumentumokkal gyerekjáték, az
argumentumokat zárójelek között megadjuk (kivéve a *self* argumentumot),
a metódusokhoz pedig az eddig tanult módon dot notationnel férünk hozzá.
Például a *point1* objektumunkat, ha el akarjuk helyezni, akkor a fenti
módon tehetjük ezt meg, azaz *point1.elhelyez(x, y)*.

Ha nem állítjuk be megfelelően a *Point* objektum *x* és *y* értékét,
akár az elhelyez metódussal, akár közvetlen értékadással, akkor egy
hibaüzenetet kapunk:

```python
AttributeError: 'Pont' object has no attribute 'x'
```

A hibaüzenet figyelmeztet minket arra, hogy nem adtuk meg az objektum
egyik attribútumát. Hasznos lenne, ha minden új objektum
alapértelmezetten rendelkezne a szükséges attribútumokkal. Az
objektumorientált nyelvek rendelkeznek egy speciális metódussal, a
**konstruktorral**, amely az osztály példányosításakor hívódik meg.
Feladata a példány kezdőértékeinek beállítása. Ennek a különleges
metódusnak a neve minden esetben: *__init__*. Az elöl és hátul is
dupla alsóvonás jelzi a parancsértelmezőnek, hogy egy különleges esettel
van dolga. Hozzuk most létre így a *Pont* objektumunkat, hogy szükséges
legyen az *x* és *y* értékek megadása:

```python
class Point:
    def __init__ (self, x, y):
        self.place(x, y)
	def place(self, x, y):
		self.x = x
		self.y = y
	def origo(self):
		self.place(0, 0)
```

Most már elértük, hogy nem hozhatunk létre pontot anélkül, hogy
megadnánk a megfelelő paramétereit. Ha mégis megpróbálnánk, akkor
hibaüzenetet kapnánk, miszerint nem adtunk meg elég argumentumot. Arra
is lehetőségünk van, hogy megelőzzük az ehhez hasonló hibaüzeneteket,
mégpedig úgy, hogy alapértelmezett értékeket adunk a pontjainknak a
következő módon:

```python
class Point:
	def __init__ (self, x=0, y=0):
		self.place(x, y)
```

Hogy teljessé tegyük az első osztályunkat, nincs más dolgunk, mint
docstringgel ellátni. A függvényeknél már tanultunk a docstringekről,
ott utána lehet nézni. Lássuk a végleges *Point* osztályt:

```python
import math
class point:
'A koordináta rendszer egy pointja'
    def __init__ (self, x=0, y=0):
    '''Egy új pont pozícióját inicializálja. Az x és y értékek
    megadhatók, ha nincsenek megadva, akkor alapértelmezetten
    az origóra kerül a pontunk.'''
        self.place(x, y)
        
    def place(self, x, y):
    'placei a pontot a koordinátarendszer x, y pontjában.'
        self.x = x
        self.y = y

    def origo(self):
    'A pontot elhelyezi az origóra.'
        self.place(0, 0)

    def tavolsag(self, other_point):
    '''Megadja a távolságot e között és egy másik, paraméterként megadott
    pont között. Pitagórasz-tételt használ a távolság kiszámítására.
    A távolság visszatérési értéke float.'''
        return math.sqrt((self.x - other_point.x)**2 + (self.y - other_point.y)**2)
```

------------------------------------------------------------------------

Most, hogy már tudunk osztályokat létrehozni és objektumokat
példányosítani, szükségessé vált, hogy elgondolkodjunk a megfelelő
elrendezésükön. Kis programoknál ezzel nem kell vesződni, egy fájlba
bekerülhet az összes osztály, a fájl végére pedig a kódunk, és már
működik is. Ahogy azonban egyre terebélyesedik a programunk, egyre
nehezebb megtalálni már benne az éppen szükséges osztályt, ha esetleg
módosítanánk benne valamit, vagy csak megnéznénk, hogy pontosan mi is
történik benne. Itt jönnek be a képbe a **modulok**. Ezekről volt már
szó a [függvényekről szóló
bejegyzésünk](python-programozas-2-fuggvenyek.md)
végén, elevenítsük fel azokat az ismereteket, és bővítsük is ki egy
kicsit! A modulok egyszerű Python-fájlok. Ha két Python-fájlunk van
ugyanabban a könyvtárban, akkor az egyik modul egyik osztályát meg
tudjuk hívni a másik modulból is.

Ha például egy könyvkölcsönzést nyilvántartó programot szeretnénk írni,
akkor valószínűleg az adataink nagy részét egy adatbázisban tárolnánk.
Ebben az esetben célszerű lenne az összes adatbázissal kapcsolatos
osztályt és függvényt egy különálló fájlban tárolni (pl. adatbazis.py).
A többi modul pedig importálni tudja majd azokat az osztályokat, amelyek
szükségesek az adatbázishoz való hozzáféréshez. Ennek módja: *import
modulnév*. Nézzünk erre most egy konkrét példát! Feltételezzük, hogy az
*adatbazis.py* modulunk tartalmaz egy *Adatbazis* nevű osztályt.
Szeretnénk egy másik fájlban szeretnénk példányosítani az *Adatbázis*
osztályt. Ezt így tehetjük meg:

```python
import adatbazis
db = adatbazis.Adatbazis()
```

A fenti módszerrel az *adatbazis* modul összes osztályához és
függvényéhez hozzáférhetünk az *adatbazis.valami* meghívással. Ha csak
egy osztályt vagy függvényt szeretnénk betölteni, azt a *from ... import
...* módon tudjuk megtenni:

```python
from adatbazis import Adatbazis
db = Adatbazis()
```

Ha olyan helyzet fordulna elő, hogy a példányosítani kívánt osztály neve
megegyezik egy másik osztály nevével abban a fájlban, amelybe
importálnánk, akkor egész egyszerűen megtehetjük azt, hogy más néven
importáljuk:

```python
from adatbazis import Adatbazis as AB
db = AB()
```
