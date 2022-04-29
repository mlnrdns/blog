Title: Python unit testing
Published: 2018-01-18 16:59
Author: molnardenes
Tags: [unittest, testing]
---

Ebben a bejegyzésben a **unit test**ing alapjait szeretném bemutatni. Rögtön felmerülhet a kérdés, hogy mi is az a unit test? A legegyszerűbben úgy fogalmazható meg, hogy a unit test egy kódrészlet viselkedését vizsgálja, vagyis azt, hogy egy adott osztály/függvény/modul úgy működik-e, ahogy mi azt szeretnénk. 

A unit testeket jellemzően a fejlesztők írják (előfordul, hogy automatikusan generálódnak, de ez nem témája a bejegyzésnek), és további tulajdonságuk, hogy automatikusan futnak le, és minden esetben egyértelműen jelzik, hogy az adott teszten átment-e a kódunk vagy elbukott. Ezeket a teszteket bárki lefuttathatja, akinek hozzáférése van a kódunkhoz.

A későbbi módosítások során nagyon sok szenvedéstől tud megkímélni, ugyanis előfordulhat olyan, hogy egy látszólag független kódrészlet módosítása hibát okoz az egyik függvényünkben. Ha van unit testünk, akkor ez azonnal kiderül, ha nincs, akkor viszont csak reménykedhetünk, hogy erre a hibára még házon belül fény derül :)

Fontos kiemelni, hogy a szorosan vett unit test értelmezésbe nem tartoznak bele olyan tesztek, amik használják a fájlrendszert, valamilyen adatbázist vagy hálózatot. Ezeket **integration test**eknek nevezzük.

## A unittest modul

A rövid bevezető után nézünk is meg egy példát. Készítsünk egy kis rendszámnyilvántartó programot, amiben tulajdonos alapján kikereshetők a rendszámok. Ebben a bejegyzésben a Python sztenderd unit test modulját, a **unittest**et fogom használni. Futtatása nagyon egyszerű, csak kiadom a következő parancsot ott, ahol a tesztesetek vannak és a modul megtalálja magától mindet és le is futtatja azokat:

```bash
python3 -m unittest
```

Mielőtt nekiállnánk az alkalmazásnak, hozzunk létre egy **teszteset**et, hogy lássuk, mit is fogunk alkotni. A tesztelés során sok kis tesztesetet fogunk létrehozni, amelyek mindegyike az alkalmazásunk egy-egy viselkedését vizsgálja. Fontos, hogy ezek a tesztesetek egymástól függetlenül futtathatók legyenek, és nem hathatnak egymásra. Ne csináljunk olyan tesztesetet, amely valamilyen adato létrehoz, amit egy másik teszteset felhasznál!

Csináljunk egy könyvtárat **license_lookup** néven, majd hozzunk létre egy tesztfájlt: **test_license_lookup.py**.

```python
import unittest

class LicenseLookupTest(unittest.TestCase):

    def test_create_license_lookup(self):
        license_lookup = LicenseLookup()
```

A fenti példában az elnevezésre érdemes figyelni, fontos, hogy beszédes legyen a tesztesetünk neve, hogy más fejlesztők is könnyen megértsék.

Mentsük el a fájlt és adjuk ki a következő parancsot parancssorból:

```bash
python3 -m unittest
```

A futás eredményeként a következőt kell kapnunk:

```bash
E
======================================================================
ERROR: test_create_license_lookup (test_license_lookup.LicenseLookupTest)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/home/molnardenes/PythonProjects/blog/content/in_progress/test_license_lookup.py", line 6, in test_create_license_lookup
    license_lookup = LicenseLookup()
NameError: name 'LicenseLookup' is not defined

----------------------------------------------------------------------
Ran 1 test in 0.001s

FAILED (errors=1)
```

Láthatjuk, hogy a teszt elbukott és az oka nem más, mint egy *NameError*, nevezetesen, hogy a LicenseLookup osztály nincs definiálva. Világos, egyelőre még nem írtuk meg a kódot, a tesztünk pedig figyelmeztetett erre. Hozzuk most létre akkor a **license_lookup.py** fájlt:

```python
class LicenseLookup:
    pass
```

Ahhoz, hogy a tesztesetünk hozzáférjen ehhez, be kell importálnunk a tesztfájlunkba, amit módosítsunk is ez alapján:

```python
import unittest
from license_lookup import LicenseLookup

class LicenseLookupTest(unittest.TestCase):

    def test_create_license_lookup(self):
        license_lookup = LicenseLookup()
```

Mentsük a fájlokat és futtasuk újra a unit testet:

```bash
python3 -m unittest
```

Most a következő eredmény fogad minket:

```bash
.
----------------------------------------------------------------------
Ran 1 test in 0.000s

OK
```

A unittest modul visszajelzést adott nekünk arról, hogy futtatott egy tesztet, és az sikeresen zárult.

* * *

Nem sok funkcióval bír jelenleg a programunk, úgyhogy hozzunk létre néhány tesztesetet, ami segít nekünk abban, hogy hogyan fejlesszük tovább a programunkat:

```python
import unittest
from license_lookup import LicenseLookup

class LicenseLookupTest(unittest.TestCase):

    def test_create_license_lookup(self):
        license_lookup = LicenseLookup()

    def test_lookup_license_by_name(self):
        license_lookup = LicenseLookup()
        license_lookup.add("Dénes", "IRJ489")
        self.assertEqual("IRJ489", license_lookup.find("Dénes"))
```

A teszt során hoztam is egy döntést: az osztályomnak lesz egy **add** és egy **find** metódusa. Az osztály viselkedésének ellenőrzésére egy *assert** statementet használtam, az **assertEqual** első argumentuma a várt érték, míg a második a tesztelés alatt álló rendszer által visszaadott érték.

Létrehozom a két metódust üresen a **license_lookup.py**-ben:

```python
class LicenseLookup:
    def add(self, name, license):
        pass

    def find(self, name):
        pass
```

Futtatom a tesztet:

```bash
.F
======================================================================
FAIL: test_lookup_license_by_name (test_license_lookup.LicenseLookupTest)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/home/molnardenes/PythonProjects/blog/in_progress/test_license_lookup.py", line 12, in test_lookup_license_by_name
    self.assertEqual("IRJ489", license_lookup.find("Dénes"))
AssertionError: 'IRJ489' != None

----------------------------------------------------------------------
Ran 2 tests in 0.000s

FAILED (failures=1)
```

Hibára fut, ugyanis a feltételezésünk elbukott. Nincs más hátra, mint implementálni az add és find függvénytörzsét is:

```python
class LicenseLookup:
    
    def __init__(self):
        self.records = {}

    def add(self, name, license):
        self.records[name] = license

    def find(self, name):
        return self.records[name]
```

Ha újra lefuttatom a tesztet, akkor a következő eredményt kapom, aminek nagyon örülök :)

```bash
..
----------------------------------------------------------------------
Ran 2 tests in 0.000s

OK
```

Következő lépésben azt is végig kell gondolnom, hogy mi történjen akkor, ha olyan nevet kérdez le a felhasználó, ami nincs meg a rendszerben. Ilyenkor szerencsés lenne, ha kulcshibát kapna vissza (KeyError) mondjuk (tekintve, hogy az osztályunkban az adatok egy **dictionary**-ben vannak eltárolva:

```python
import unittest
from license_lookup import LicenseLookup

class LicenseLookupTest(unittest.TestCase):

    def test_create_license_lookup(self):
        license_lookup = LicenseLookup()

    def test_lookup_license_by_name(self):
        license_lookup = LicenseLookup()
        license_lookup.add("Dénes", "IRJ489")
        self.assertEqual("IRJ489", license_lookup.find("Dénes"))

    def test_missing_record_raises_keyError(self):
        license_lookup = LicenseLookup()
        with self.assertRaises(KeyError):
            license_lookup.find("hiányzó név")
```

Futtassuk le a tesztünket, de most adjunk át egy paramétert (**-v**), aminek a segítségével részletezve láthatjuk a lefuttatott teszteket:

```bash
python3 -m unittest -v
```

A futás eredménye:

```bash
test_create_license_lookup (test_license_lookup.LicenseLookupTest) ... ok
test_lookup_license_by_name (test_license_lookup.LicenseLookupTest) ... ok
test_missing_record_raises_keyError (test_license_lookup.LicenseLookupTest) ... ok

----------------------------------------------------------------------
Ran 3 tests in 0.000s

OK
```

Eddig jók vagyunk, minden tesztünk rendben lefutott.

## Test fixture-ök

A **test fixture**-ök olyan erőforrások és kiindulási állapotok, amelyek biztosítják a feltételeket ahhoz, hogy a tesztjeink megfelelően és más tesztektől függetlenül működjenek. A tesztek lefutása után ezek el is takarítanak maguk után amennyiben arra szükség van. 

Két metódus szolgál segítségül nekünk:

* **setUp()** minden teszt előtt lefut
* **tearDown()** minden teszt után lefut

A fenti tesztek során észre vehettük, hogy minden esetben példányosítottuk a **LicenseLookup** osztályunkat, de ezt elég lenne egyszer megtenni. És itt jön képben a **fixture**. Refaktoráljuk ez alapján a tesztünket:

```python
import unittest
from license_lookup import LicenseLookup

class LicenseLookupTest(unittest.TestCase):
    
    def setUp(self):
        self.license_lookup = LicenseLookup()
    
    def test_lookup_license_by_name(self):
        self.license_lookup.add("Dénes", "IRJ489")
        self.assertEqual("IRJ489", self.license_lookup.find("Dénes"))

    def test_missing_record_raises_keyError(self):
        with self.assertRaises(KeyError):
            self.license_lookup.find("hiányzó név")
```

A jelenlegi példában nincs szükség a **tearDown** implementálására, a Python gondoskodik a takarításról helyettünk. A **setUp** metódusban létrehoztuk a **license_lookup** példányunkat és az egész tesztosztályunkban tudjuk ezentúl használni, nem kell minden esetben külön példányosítanunk.

Nézzünk meg még egy **assert** esetet. A bejegyzésben nincs lehetőség az összes assert lehetőség bemutatására, de még egyet ismertetni szeretnék. Ez az **assertIn**, ami arról ad visszajelzést, hogy egy adott elem megtalálható-e eg felsorolható típusban:

```python
import unittest
from license_lookup import LicenseLookup

class LicenseLookupTest(unittest.TestCase):
    
    def setUp(self):
        self.license_lookup = LicenseLookup()
    
    def test_lookup_license_by_name(self):        
        self.license_lookup.add("Dénes", "IRJ489")
        self.assertEqual("IRJ489", self.license_lookup.find("Dénes"))

    def test_missing_record_raises_keyError(self):        
        with self.assertRaises(KeyError):
            self.license_lookup.find("hiányzó név")

    def test_add_names_and_licenses(self):
        self.license_lookup.add("Anikó", "ABC196")
        self.assertIn("Anikó", self.license_lookup.get_names())
        self.assertIn("ABC196", self.license_lookup.get_licences())
```

Írjuk is meg a két metódust az osztályunkban:

```python
class LicenseLookup:
    
    def __init__(self):
        self.records = {}

    def add(self, name, license):
        self.records[name] = license

    def find(self, name):
        return self.records[name]

    def get_names(self):
        return list(self.records.keys())

    def get_licences(self):
        return list(self.records.values())
```

Nincs más hátra mint lefuttatni a teszteket. Az eredmény pedig:

```bash
test_add_names_and_licenses (test_license_lookup.LicenseLookupTest) ... ok
test_lookup_license_by_name (test_license_lookup.LicenseLookupTest) ... ok
test_missing_record_raises_keyError (test_license_lookup.LicenseLookupTest) ... ok

----------------------------------------------------------------------
Ran 3 tests in 0.000s

OK
```

Ebben a bejegyzésben megismerkedhettünk a unit testelés alapjaival a Python beépített unittest moduljának segítségével. Nagyon sok egyéb lehetőségünk is van pythonban tesztelni, a későbbiekben a következőknek is tervezek egy-egy bejegyzést szentelni: doctest, py.test, mock.
