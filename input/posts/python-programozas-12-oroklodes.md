Title: Python-programozás 12 Öröklődés
Published: 2013-03-28 20:09
Author: molnardenes
Tags: [OOP]
---


Ebben a bejegyzésben megismerkedhetünk az öröklődéssel. Magával a
fogalommal már az [előző
fejezetben](python-programozas-11-osztalyok-es-objektumok.md)
megismerkedtünk, most nézzük meg a gyakorlati megvalósítását. Példánkban
egy személynyilvántartó programot fogunk megírni. Létrehozunk egy
*Szemely* osztályt, amelyben a személyeknek lesz nevük és születési
dátumuk. Metódusaival megkaphatjuk egy személy vezetéknevét, névsorba
rendezhetjük a személyeket, és megkaphatjuk az életkorukat. Nézzük a
kódot:

```python
import datetime
class Person():
    def __init__(self, name):
    '''nev nevű személy létrehozása'''
        self.name = name
        self.dayOfBirth = None
        self.familyName = name.split(' ')[0]

    def getfamilyName(self):
    '''visszatérési értéke a személy vezetékneve'''
        return self.familyName

    def setdayOfBirth(self, ev, honap, nap):
    '''a személy születésnapjának beállítása'''
        self.dayOfBirth = datetime.date(ev, honap, nap)

    def getAge(self):
    '''visszadja a személy korát napokban'''
        if self.dayOfBirth == None:
            raise ValueError
        return (datetime.date.today() - self.dayOfBirth).days

    def __lt__(self, otherPerson):
    '''Igaz a visszatérési értéke, ha a self név előrébb van
    az ábécében, mint a másik név'''
        if self.familyName == otherPerson.familyName:
            return self.name < otherPerson.name
        return self.familyName < otherPerson.familyName

    def __str__(self):
    '''visszaadja a személy nevét'''
        return self.name
```

Létrehoztuk a *Person* osztályunkat néhány metódussal együtt. A dátumok
kezelésére importáltuk a datetime modult. Első lépésként hozzunk létre
egy személyt, majd írassuk képernyőre a változó értékét! Az osztály
*str* metódusa nem tesz mást, mint visszatér a személy nevével.
Létrehozás után lesz a személynek neve, None értékű lesz a születési
ideje, mivel az eddig nem ismert, illetve képesek leszünk hozzáférni a
vezetéknévhez. Ezt a *split* metódussal érjük el, mégpedig úgy, hogy a
felszeletelt nevek közül vesszük az elsőt. Az osztályunkhoz definiáltunk
egy *getvezNev* nevű metódust, aminek segítségével megkaphatjuk egy
adott névből a vezetéknevet. Ezután be tudjuk állítani az adott személy
születésnapját. A *setDayOfBirth* metódus 3 argumentumaként az évet, a
hónapot és a napot adhatjuk meg. A beállításhoz a datetime modult *date*
metódusát használom, és elmentem egy változóba. A *getKor* metódusban
szintén a datetime modult használjuk. Meghatározzuk vele a mai napot,
majd kivonjuk belőle a születési dátumot, és kiírjuk a napok számát.
Végül az lt metódussal össze tudunk hasonlítani két nevet, aszerint,
hogy melyik áll előbb a betűrendben.

------------------------------------------------------------------------

Vegyük számba most a BCE személyzetét. Annyit tudunk, hogy biztosan
mindannyian maguk is személyek, azaz lesz nevük és születési dátumuk,
ezt a *Person* osztályból öröklik, de lesznek saját attribútumaik is,
amelyek nincsenek minden személynek. Létrehozunk egy *BCEPerson*
osztályt, ami megörökli a *Szemely* osztály összes attribútumát és
metódusát, de rendelkezni is fog sajátokkal. Minden személyhez
hozzárendelünk egy azonosítót, amit bármikor lekérhetünk, és
lehetőségünk lesz azonosító alapján sorba rendezni a tagokat. Lássuk:

```python
class BCEPerson(Person):
    nextId = 0

    def __init__(self, name):
    '''Inicializálja a Person atrribútumait, majd létrehozza
    az azonosítót'''
        Person.__init__(self, name)
        self.id = BCEPerson.nextId
        BCEPerson.nextId += 1

    def getAzonosito(self):
        return self.id

    def __lt__(self, otherPerson):
        return self.id < otherPerson.id
```

Létrehoztuk a *BCEPerson* **alosztályunkat**. Ezt úgy tudtuk megtenni,
hogy az osztálydefiniálás során zárójelbe beírtuk az alosztály
**szülőosztályát**. A *nextId* atrribútumhoz minden BCEPerson
osztályhoz tartozó személy hozzáfér, és ezen osztozik. Amikor tehát
létrehozunk egy új BCE személyt, nem csak, hogy beállítom a *BCEPerson*
attribútumai alapján, hanem belenyúlok, és megváltoztatom a
*nextId* értékét.

Szükséges tisztázni a különbséget a **lokális**, a **globális** és az
**osztályváltozó** között. Ha az azonosítót egy instancián belül hozzuk
létre, *self.nextId*, akkor csak az az egy instancia fér hozzá, a
többi nem. Ezek a lokális változók tehát, csak az instancián belül
egyediek. Ha globálissá tennénk, akkor minden névtérből hozzáférhető
lenne, bárki módosíthatná. Az osztályváltozók pontosan erre jók, ugyanis
csak az adott osztály objektumai férhetnek hozzá.

Fontos megemlítenünk a helyettesítési elvet, ami azt jelenti, hogy egy
alosztály helyettesíthető a szülőosztályával. Vagyis minden *BCEPerson*
egyben *Person* is. Ha létrehoznánk egy *Student*, *BScStudent* és
egy *MScStudent* osztályt, akkor pl. a *BScStudent* egyben *Student*
is lenne, de *BCEPerson* is és *Person* is.

------------------------------------------------------------------------

Függvényhívások esetén a *return* utasítás után a függvény lokális
változói megszűnnek. Ha egy későbbi alkalommal meghívjuk ismételten
ugyanezt a függvényt, akkor új lokális változókat kap. Előállhat azonban
olyan eset, amikor szeretnénk egy későbbi időpontban onnan folytatni egy
függvény futását, ahol legutóbb abbahagytuk. Ilyenkor jönnek jól a
**generátorok**. A függvény a **yield** utasítással tehető
generátorfüggvénnyé, ilyenkor nem csak egy szimpla értéket ad vissza,
hanem egy generátor objektumot. Ennek a jelentősége abban áll, hogy a
generátor végrehajtása utána a függvény állapot nem befejezett lesz,
hanem csak félbeszakadt, a lokális változók nem szűnnek meg, hanem
eltárolódnak. A következő függvényhívásnál a **next()** függvény
használatával a függvény végrehajtása a *yield* után folytatódik, onnan,
ahol az előző futásnál abbamaradt. Nézzünk egy példát:

```python
def fibGen():
    n_1 = 1
    n_2 = 0
    while True:
        next = n_1 + n_2
        yield next
        n_2 = n_1
        n_1 = next
```

Láthatjuk, hogy létrehoztunk egy Fibonacci-generátort. Az n_1 és az
n_2 nem más mint az n-1 és az n-2. tagjai a sorozatnak. Kiválóan
látszik a *yield* és a *next* használata. Írassuk ki most a
Fibonacci-számokat a képernyőre:

```python
>>> for szam in fibGen():
print(szam)

1
2
3
5
8
13
21
34
55
89
144
233
377
610
987
1597
2584
4181
6765
10946
17711
28657
46368
75025
121393
196418
317811
514229
832040
1346269
2178309Traceback (most recent call last):
File <pyshell#8>", line 2, in <module>
print(szam)
File "C:Program Files (x86)Python 32libidlelibPyShell.py",
line 1289, in write
return self.shell.write(s, self.tags)
KeyboardInterrupt
```

A futást megszakíthatjuk a ctrl-c kombinációval, de láthatjuk, hogy
szépen kiírja a Fibonacci-sorozat tagjait.
