Title: Python-programozás 7 Összetett adattípusok
Published: 2013-02-06 21:04
Author: molnardenes
Tags: [datatypes]
---

Az összetett típusok olyan adattípusok, amelyek felépíthetők a
programozási nyelvekben ismert elemi adattípusokból vagy más összetett
típusokból. Ezeket arra használjuk, hogy strukturáltan csoportosítsunk
értékegyütteseket. A Pythonban a következő összetett adattípusokat
különböztetjük meg: **tuple**, **lista** (list), **szótár**
(dictionary), **halmaz** (set, frozenset).

Elsőként ismerkedjünk meg a **tuple**-ökkel. A tuple elemek rendezett
listája. A stringeknél megismert indexelési, szeletelési és bejárási
technikák itt is alkalmazhatók, így könnyen hozzáférhetünk a tuple-ök
egyes elemeihez. Ez az adattípus szintén immutábilis, azaz egyetlen
eleme sem módosítható vagy törölhető. A szintaxisa a következő:
*tuple_neve = ('alma', 'körte', 12, 3)*. Az értékadásnál fontos, hogy
vesszővel választjuk el az elemeit, ugyanakkor a zárójel elhagyható, de
az olvashatóság érdekében ajánlott a használata! Nézzünk néhány példát:

```python
gyumolcsok = ('alma', 'körte', 'barack')
szamok = 1, 2, 3, 4, 5
egy_elemu_tuple = 'kutya',
ures_tuple = ()
```

Az első kettő egyértelmű. A második esetre érdemes figyelni. Az egyetlen
elem után vesszőt teszünk! Ha ezt nem tennénk meg, akkor a fenti
példában szereplő *egy_elemu_tuple* nevű változó nem tuple, hanem
string lenne! Üres tuple-t zárójelekkel tudunk megadni.

A tuple-ök csupán két metódussal rendelkeznek, ezek: a *count()* és az
*index()*. Az előbbi megadja, hogy a keresett elem hányszor fordul elő a
tuple-ben, a második pedig megadja az első előfordulás helyét. Ezeken
felül a tuple-ök esetében is használható a konkatenáció (+), a
sokszorozás (*), vizsgálhatjuk egy-egy szeletét ([]), illetve
vizsgálhatjuk az *in* használatával, hogy egy elem része-e a tuple-nek.
A stringek esetén megismert iteráció a tuple-ök esetében is működik.
Nézzünk erre egy példát:

```python
def common_divisors (number1, number2):
    divisors = () #létrehoztunk egy üres tuple-t
    for i in range (1, min (number1, number2) + 1):
        if number1 % i == 0 and number2 % i == 0:
            divisors = divisors + (i,)
    return divisors
```

Az eljárásunk az argumentumként megadott két szám közös osztóit adja
meg. A következő példában végigiterálunk a tuple-ünkön, és kiszámoljuk
két szám közös osztóinak összegét:

```python
sum_of_divisors = common_divisors (20, 100)
osszeg = 0
for x in sum_of_divisors:
    osszeg += x
print(osszeg)
```

Egymásba is ágyazhatunk tuple-öket:

```python
>>> things = (1, -1.75, ('tökfőzelék', (100, 'AbC'),
'sorozat'))
>>> things[2][1][1][2]
'C'
```

Vegyük át lépésről lépésre a fentieket. A *dolgok[2]* a harmadik
elemre utal (mivel ugye az első elem indexe a 0), ami önmaga is egy
tuple. A *dolgok[2][1]* kifejezés a második elem a *dolgok[2]*
tuple-ben, ami megint csak egy tuple, a (100, 'AbC'). A
*dolgok[2][1][1]* megadja a második elemet ebben a tuple-ben,
vagyis az 'AbC' stringet. A *dolgok[2][1][1][2]* ennek a
stringnek a harmadik karakterére mutat, ami nem más, mint a 'C'.

------------------------------------------------------------------------

Nézzük most a **listákat**. A lista a tuple-höz hasonlóan elemek
rendezett listája. A stringeknél és tuple-öknél megismert indexelési,
szeletelési és bejárási technikák itt is alkalmazhatók, így könnyen
hozzáférhetünk a listák egyes elemeihez. A nagy különbség az előzőekhez
képes a listák mutábilitása, vagyis megváltoztathatósága. A listákba
beszúrhatunk új elemet, törölhetünk belőlük, módosíthatjuk az elemek
sorrendjét, vagy éppen lecserélhetünk egyes elemeket.

A listákat [] zárójelekkel tudjuk megadni. Például: *gyumolcsok =
['alma', 'körte', 'barack']*. Egyelemű és üres lista a következőképpen
adható meg: *[2]*, illetve *[]*. A listák egyes elemeihez a már
ismert módon férhetünk hozzá, például *gyumolcsok[2]* a példánkban a
barack. Tehát a szeletelési operátorokkal hozzáférhetünk bármelyik
elemhez. Elképzelhető azonban, hogy már létrehozáskor több elemre
bontjuk a listánkat. Ez az úgynevezett **sequence unpacking**. Nézzünk
rá néhány példát:

```python
>>> first_element, *rest_of_the_elements = [9, 8, 7, -5, 6]
>>> first_element, rest_of_the_elements
(9, [8, 7, -5, 6])
>>> first_name, *middle_name, family_name = "Edison Arantes
do Nascimento".split()
>>> first_name, middle_name, family_name
('Edison', ['Arantes', 'do'], 'Nascimento')
```

Most vizsgáljunk meg két olyan metódust, amiket a listák mutábilis
tulajdonsága miatt használhatunk. Ezek az **append** és a **remove**
metódusok lesznek:

```python
>>> lista1 = [1, 2, 3, 4, 5]
>>> lista2 = [6, 7, 8, 9, 10]
>>> lista1.append(lista2)
>>> lista1
[1, 2, 3, 4, 5, [6, 7, 8, 9, 10]]
```

Hoppá! A *lista1* listához, ha az appenddel adjuk hozzá a *lista2*
listát, akkor nem egyesével az elemei adódnak hozzá, hanem maga a teljes
lista lesz a 6. elem! Erre nagyon figyeljünk oda! Ha az elemeit
szeretnénk hozzáadni, akkor egy egyszerű konkatenációval tudjuk
megoldani:

```python
>>> lista1 = [1, 2, 3, 4, 5]
>>> lista2 = [6, 7, 8, 9, 10]
>>> lista1 + lista2
[1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
```

Most a remove metódust vizsgáljuk meg!

```python
>>> def doubleDelete(A, B):
	for a in A:
		if a in B:A.remove(a)


>>> A = [1,2,3,4,5]
>>> B = [1,2,6,7,8]
>>> doubleDelete(A,B)
>>> print(A)
[2, 3, 4, 5]
```

Hoppá! Hogy lehet, hogy csak az 1-et törölte, a 2-t pedig nem? Azt is
törölnie kellett volna, mivel mindkét listában szerepel. A probléma itt
az, hogy mivel magát az A listát módosítjuk, ez bezavar az iterálásba.
Ugyanis, ami eddig az 1-es indexen szerepelt, az most a 0. törlésével a
0. lett, de azt már elhagytuk, így az első indexet vizsgálja már csak a
függvény. Hogyan tudjuk ezt megoldani? Hozzunk létre egy másolatot az A
listából, azon iteráljunk végig, és az A listából töröljük az elemeket:

```python
>>> def doubleDelete(A, B):
	for a in A_copy:
		if a in B:
			A.remove(a)
>>> A = [1,2,3,4,5]
>>> A_copy = A[:]
>>> B = [1,2,6,7,8]
>>> duplaTorles(A,B)
>>> print(A)
[3, 4, 5]
```

Itt még egy fontos dolgot meg kell említenünk! Amikor a másolatnak
értéket adtunk, nem lett volna elég a következő: *A_masolat = A*. Ekkor
ugyanis az *A_masolat* tulajdonképpen ugyanarra az értékre mutat, amire
az *A*, azaz a listára magára. A [:]-tal viszont egy teljesen új
listát hoztunk létre, ami az iteráció során változatlan maradt, így
ennek a segítségével törölhettünk minden duplikátumot.

------------------------------------------------------------------------

Mielőtt rátérnénk a szótárakra, ismerkedjünk meg a **magasabb rendű
függvények** fogalmával. Az olyan függvényeket, melyek megengednek
függvényt aktuális paraméterként, vagy eredményül függvényt adnak,
magasabb rendű függvényeknek nevezzük. Lássuk:

```python
>>> def useForAll(lista, function):
	for i in range (len(lista)):
		lista[i] = fuggveny(lista[i])
>>> lista = [-1, 2, -6, -8, -16, 22]
>>> useForAll(lista, abs)
>>> lista
[1, 2, 6, 8, 16, 22]
```

Ehhez hasonló módon működik a Python beépített **map** függvénye.
Szintaxisa: *map (függvény, lista, ...)*. A függvényt alkalmazza a lista
objektum minden elemére, és visszaadja az eredményeket tartalmazó
listát. Ha további paraméterei vannak a függvénynek, akkor a függvénynek
is annyival több formális paraméterrel kell rendelkeznie, és ezt az
objektumok elemeire párhuzamosan alkalmazza a map() függvény. Ha egyik
lista rövidebb, mint a másik, akkor a map() úgy veszi, mint hogyha
további *None* értékei lennének

------------------------------------------------------------------------

A **szótárak** a listákra emlékeztetnek, de nem szekvenciák. A
bejegyzett elemek nem megváltoztathatatlan sorrendben lesznek
elrendezve. Egy kulcsnak nevezett speciális indexszel bármelyikükhöz
hozzáférhetünk. Kulcs nem csak *integer* lehet már, hanem bármilyen
immutábilis típus. A szótárak maguk megváltoztathatók, emiatt könnyen
adhatunk hozzá új elemeket, vagy távolíthatunk el már nem szükségeseket.
Ellenben mivel rendezetlenek, ezért nem szeletelhetők.

A **szótárak** szintaxisa a következő: *szótár = {kulcs:érték}*. Kapcsos
zárójellel hozzuk létre. Üres szótár a *{}* kapcsos zárójelekkel hozható
létre.

```python
>>> monthAsNumber = {'Jan' : 1, 'Feb' : 2, 'Már' : 3, 'Ápr' :
4}
>>> monthAsNumber ['Jan'] ## kulcs alapján hozzáférhetünk az
értékhez
1
>>> monthAsNumber [2] ## számindex alapján nem férhetünk
hozzá az értékhez
Traceback (most recent call last):
File "<pyshell#3>", line 1, in <module>
monthAsNumber [2]
KeyError: 2
```

A fenti példában létrehoztunk egy *monthAsNumber* nevű szótárat. Az egyes
elemeihez, amint látjuk, nem férhetünk hozzá az index megadásával, csak
a kulcs segítségével! A szótárakhoz könnyen hozzáadhatunk új elemet a
következő módon: *monthAsNumber ['Máj'] = 5*. Ha egy új elemhez egy már
létező kulcsot rendelünk, akkor a kulcshoz rendelt érték lecserélődik az
új értékre. A szótárakon végigiterálhatunk. Lássunk ezekre is egy-egy
példát:

```python
>>> monthAsNumber = {'Jan' : 1, 'Feb' : 2, 'Már' : 3, 'Ápr' :
4}
>>> monthAsNumber
{'Ápr': 4, 'Feb': 2, 'Már': 3, 'Jan': 1}
>>> monthAsNumber ['Máj'] = 5
>>> monthAsNumber
{'Ápr': 4, 'Feb': 2, 'Már': 3, 'Jan': 1, 'Máj': 5}
>>> monthAsNumber ['Ápr'] = 8
>>> monthAsNumber
{'Ápr': 8, 'Feb': 2, 'Már': 3, 'Jan': 1, 'Máj': 5}
>>> gyujtemeny = []
>>> for i in monthAsNumber:
    gyujtemeny.append(i)
>>> gyujtemeny
['Ápr', 'Feb', 'Már', 'Jan', 'Máj']
```
