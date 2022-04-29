Title: Python-programozás 8 Keresés és rendezés
Published: 2013-03-05 01:07
Author: molnardenes
Tags: [algoritmus]
---

A **keresőalgoritmusok** olyan algoritmusok, amelyek segítségével egy
bizonyos tulajdonsággal felruházott elemet keresünk elemek egy
gyűjteményében. Ez a gyűjtemény lehet akár rekordok sorozata, tömb,
mátrixok, vagy akár adatbázisok, az internet vagy akár egy
matematikailag leírható tér is. A korábbi bejegyzésekben foglalkoztunk
már keresésekkel a gyökkereső algoritmusok esetén.

Elsőként nézzük meg az egyszerű lineáris keresést.

```python
def search (L, e):
    for i in range (len(L)):
        if L[i] == e:
            return True
    return False
```

Veszünk tehát pl. egy listát (*L*), és megvizsgáljuk, hogy egy adott
elem (*e*) megtalálható-e benne. Mi ennek a legegyszerűbb módja? Fogjuk
a listát, végigiterálunk rajta elemenként, minden elem esetén
megvizsgáljuk, hogy az megegyezik-e a mi *e* elemünkkel, és ha igen,
akkor *True* értékkel tér vissza a programunk, ha nem, akkor *False*
lesz a visszatérési érték.

Mi a helyzet akkor, ha a listánk rendezett? Mondjuk növekvő sorrendben
rendezett számokat tartalmaz. Akkor a következő módszert alkalmazhatjuk:

```python
def search (L, e):
    for i in range (len(L)):
        if L[i] == e:
            return True
        if L[i] < e:
            return False
    return False
```

A második feltételünk lényege az, hogy ha az aktuális elem már nagyobb,
mint az általunk keresett elem, akkor *False* értéket adjon vissza a
program, és lépjen ki a ciklusból, hiszen ekkora fölösleges már
végigiterálni rajta. Ha növekvő sorrendben vannak az elemek, és mi
mondjuk az 5-ös számot keressük, de az i-edik elem 6, akkor nyilvánvaló,
hogy az 5 nem volt eleme a listának, hiszen már túlhaladtunk rajta.
Ennek ellenére lehet, hogy végig kell iterálnunk a listán, ugyanis
lehet, hogy a mi elemünk nagyobb, mint a lista legnagyobb eleme. A
lineáris keresés esetén a **futásidő** a lista méretével lineárisan nő,
azaz a futási ideje *O(x)*.

------------------------------------------------------------------------

Rendezett lista esetén rendelkezésünkre áll egy sokkal jobb módszer.
Korábban a gyökkeresésnél felező módszernek neveztük. Itt most nevezzük
**logaritmikus keresésnek**, vagy **bináris keresésnek**. Az elv a
következő: Válasszuk ki a lista középső elemét, és nézzük meg, hogy a
kiválasztott elemünk egyenlő-e vele. Ha igen, akkor nyert ügyünk van. Ha
nem, akkor vizsgáljuk meg, hogy a középső elemnél kisebb vagy nagyobb a
mi elemünk. Innentől kezdve már csak a list egyik felével foglalkozunk.
Itt ismét válasszuk a középső elemet, és folytassuk ezt a folyamatot
egészen addig, míg meg nem kapjuk az elemünket, vagy el nem fogynak az
elemek. Ezzel a módszerrel folyamatosan felezzük a listát. Nézzük a
kódot:

```python
def search (L, e):
    def binarySearch (L, e, min, max):
        if max == min:
           return L[min] == e
        middle = min + int ((max - min) / 2)
        if L[middle] == e:
            return True
        if L[middle] > e:
            return binarySearch (L, e, middle - 1, min)
        else:
            return binarySearch (L, e, max, middle + 1)
    if len(L) == 0:
        return False
    else:
        return binarySearch (L, e, 0, len(L) - 1)
```

Ebből minket főleg a középső rész érdekel, vagy a *binarySearch* függvény.
Ha van egy listánk, egy elemünk, egy minimum és egy maximum értékünk,
akkor mit is csinál a kódunk? Elsőként megvizsgálja, hogy mi a helyzet
akkor, ha a minimum és a maximum érték megegyezik, vagyis az értéke 1.
Ekkor, ha az elemünk pontosan ez, akkor *True* értékkel tér vissza, ha
viszont nem ez, akkor *False* értéket kapunk. Ha viszont nem csak
egyelemű a listánk, akkor megkeressük a középértéket. Hogy kapjuk ezt
meg? Elég egyszerű, a minimum értékhez hozzáadjuk a maximum és minimum
különbségének felét. Ezután megvizsgáljuk, hogy ezt az értéket
kerestük-e. Ha igen, megvagyunk, ha nem, akkor azt kell megnézni, hogy a
középérték kisebb vagy nagyobb az általunk keresettől. Ha nagyobb, akkor
meghívjuk újra rekurzívan a keresőfüggvényt ugyanazzal a minimum
értékkel, de csökkentett maximum értékkel (középérték - 1). Lényegében
"eldobjuk" a lista felső felét. Ha a középérték kisebb, akkor viszont az
alsó felét "dobjuk el" a listánknak, és megváltoztatjuk a minimum
értékünket (középérték + 1). A *search* függvényben az első lépésnek
annak kell lenni, hogy ellenőrizzük, nem üres listával van-e dolgunk. Ha
igen, akkor z elemünk nem lehet benne, így hamis értékkel tér vissza, ha
nem üres, akkor lefuttatjuk a bináris keresőnket.

Hányszor kell meghívnunk a rekurzív algoritmusunkat? Lényegében ezt a
kérdést úgy is feltehetnénk, hogy hányszor felezhetnénk meg a maximum -
minimum távolságot, mielőtt 1-et kapunk? A válasz: kettes alapú
logaritmus (maximum - minimum). Ebből következően a program futási
ideje: *O(log(len(L)))*.

------------------------------------------------------------------------

Láthattuk tehát a bináris keresés hatékonyságát. Ugyanakkor felmerülhet
a kérdés, hogy jó, de nem sokra megyek vele, ha egy nem rendezett
listával kerülök szembe. Olyankor jobb a lineáris keresést használni?
Vizsgáljuk ezt meg! Tegyük fel, hogy egy lista sorba rendezése
O(rendezés(L)) futásidejű. Ha tehát rendezünk és keresünk is, tudni
szeretnénk, hogy *rendezés(L) + log(len(L)) < len(L)* igaz-e. Ha csak
ezt a kérdést vizsgáljuk, akkor nyilvánvaló, hogy rendezni és bináris
keresést végrehajtani lassabb, mint lineárisan keresni. Ennek az az oka,
hogy a rendezéshez minden elemen végig kell mennünk. De mi van abban az
esetben, ha többször is szeretnénk keresést használni, vagyis a
következő eset áll fenn: *rendezés(L) + k * log(len(L)) < k *
len(L)* igaz-e? Látszik, hogy ez az eset két dologtól függ. Egyrészt a
*k* értékétől, másrészt, hogy tudunk-e elég hatékonyan rendezni. Nézzünk
meg egy egyszerű rendezési algoritmust, ami a **kiválasztásos rendezés**
(**selection sort**) nevet kapta:

```python
def selectionSort (L):
    for i in range (len(L) - 1):
        minIndex = i
        minValue = L[i]
        j = i + 1
        while j < len(L): if minValue > L[j]:
                minIndex = j
                minValue = L[j]
            j += 1
        temp = L[i]
        L[i] = L[minIndex]
        L[minIndex] = temp\
```

A kiválasztásos rendezés alapelve igencsak egyszerű. Megvizsgáljuk a
listát, és a legkisebb elemét az első helyre tesszük. Ezután újra
megvizsgáljuk a lista maradékát, és a maradék lista legkisebb elemét a
második helyre tesszük, és ez folytatjuk, míg a listánk végére nem
érünk. Végigiterálunk egy lista elemein. A legkisebb elem sorszáma *i*,
a legkisebb eleme pedig az *i.* sorszámú elem. Vesszük az i-nél 1-gyel
nagyobb *j*-t, és a következő ciklust hajtjuk végre: végigmegyünk a
lista maradék részén, és megkeressük a legkisebb elemet. Ha az L[j]
elem kisebb, mint az addigi legkisebb elemünk, akkor megcseréljük őket.
A cserénél óvatosan kell eljárnunk. Az i-edik indexnél lévő elemet
elmentjük az ideiglenes *temp* változóba, így a minimum helyen lévő
értéket át tudom rakni az i-edik helyre, majd a helyére tudom tenni az
ideiglenes változóban eltárolt elemet.

Vizsgáljuk meg most a futási időt! A belső ciklus lineáris, ezért a
futási ideje *O(len(L))*, a külső ciklus szintén lineáris, úgyhogy annak
a futási ideje is *O(len(L))*. A függvény teljes futási ideje pedig
négyzetes, vagyis *O(len(L)\^2)*. Ezek után beláthatjuk, hogy ez a fajta
rendezés nem túl hatékony, hiszen az eddigi lineáris és logaritmikus
futási idő helyett, itt négyzetessel számolunk. Szükségünk van tehát egy
hatékonyabb rendezési mechanizmusra.

------------------------------------------------------------------------

Egy hatásosabb rendezési módszer az **összefésüléses rendezés** (**merge
sort**). Itt az úgynevezett oszd meg és uralkodj megközelítést
alkalmazzuk. Nézzük meg, hogy mi a helyzet akkor, ha a listánk 0 vagy 1
elemű. Ilyenkor nincsen semmi tennivalónk, hiszen már eleve rendezett.
Ha ennél több elemű a listánk, akkor válasszuk ketté, és rendezzük
mindkettőt. Feltételezzük, hogy a két rövidebb listát gyorsabban
rendezni tudjuk. Harmadik lépésben pedig a két listát összefésüljük.
Ehhez megvizsgáljuk mindkét lista első elemét, a kisebbet hozzáadjuk a
végeredményhez, és ezt folytatjuk egészen addig, míg el nem fogy az
egyik listánk. Ha az egyik listánk elfogyott, akkor a másik lista
maradék elemeit egyszerűen az eredményünk végére odaillesztjük.

  1. lista elemei|2. lista elemei     | Mit hasonlítunk?| Eredménylista
  ---------------|- ------------------|--- -------------|--- ------------------------------------
  [1, 5, 6, 8]   |[2, 3, 4, 7, 12, 20]|1, 2             |[]
  [5, 6, 8]      |[2, 3, 4, 7, 12, 20]|5, 2             |[1]
  [5, 6, 8]      |[3, 4, 7, 12, 20]   |5, 3             |[1, 2]
  [5, 6, 8]      |[4, 7, 12, 20]      |5, 4             |[1, 2, 3]
  [5, 6, 8]      |[7, 12, 20]         |5, 7             |[1, 2, 3, 4]
  [6, 8]         |[7, 12, 20]         |6, 7             |[1, 2, 3, 4, 5]
  [8]            |[7, 12, 20]         |8, 7             |[1, 2, 3, 4, 5, 6]
  [8]            |[12, 20]            |8, 12            |[1, 2, 3, 4, 5, 6, 7]
  []             |[12, 20]            |--, 12           |[1, 2, 3, 4, 5, 6, 7, 8]
  []             |[]                  |--, --           |[1, 2, 3, 4, 5, 6, 7, 8, 12, 20]

Vizsgáljuk meg magának az összefésülésnek a futásidejét. Az
összehasonlítás konstans, a másolás szintén. De mi ez a konstans, mennyi
az értéke? Könnyen belátható, hogy *O(len(L))* lesz összehasonlítás
esetén, hiszen minden elemet meg kell vizsgálnom. A másolásnál az egyik
lista minden elemét át kell másolnom, a másik lista minden elemét
szintúgy, tehát: *O(len(L1)) + O(len(L2)) = O(len(L))* lesz a futási
idő.

```python
def merge (left, right, hasonlitas):
    result = []
    i, j = 0, 0
    while i < len (left) and j < len (right):
        if hasonlitas (left[i], right [j]):
            result.append (left[i])
            i += 1
        else:
            result.append (right[j])
            j += 1
    while (i < len(left)):
        result.append (left[i])
        i += 1
    while (j < len(right)):
        result.append (right[j])
        j += 1
    return result
```

A fenti nem más, mint magának az összefésülésnek a kódja. Az elején
előkészítjük az üres eredménylistát, felveszünk két indexet (i, j),
amelyeket egészen addig hasonlítunk össze valamilyen összehasonlító
operátorral, amíg a lista végére nem érünk. Összehasonlítjuk az első
elemeket, és ha mondjuk a bal oldali a kisebb, akkor azt adjuk hozzá az
eredménylistához, majd a i értékét megnöveljük eggyel. Ha a jobb oldali
a kisebb, akkor azt adjuk az eredménylistánkhoz, és a j értékét növeljük
eggyel. Az első ciklusunkban mindkét kiindulási listánk tartalmaz
elemet, a második és harmadik ciklus esetén viszont már csak a bal,
illetve a jobb oldali lista.

Ezután lássuk tehát az összefésüléses rendezés kódját:

```python
import operator
def mergeSort(L, osszehasonlitas = operator.lt):
    if len(L) < 2:
        return L[:]
    else:
        middle = int (len(L) / 2)
        left = mergeSort(L[:middle], osszehasonlitas)
        right = mergeSort(L[middle:], osszehasonlitas)
        return osszefesules(left, right, osszehasonlitas)
```

Az összefésüléses rendezés során most az kevesebb (less than) operátort
használjuk az értékek összehasonlítására. Ehhez importálnunk kell az
operator modult. Ha a listánk 0 vagy 1 elemű, akkor kész is vagyunk. Ha
azonban ennél több eleme van, akkor mit csinálunk? Nézzük! Először
meghatározzuk a középértéket, vagyis az intervallumunkat megfelezzük és
biztosítjuk, hogy egész értéket kapjunk. Ezután elsőként a bal, majd a
jobb oldali listát rendezem rekurzív módon, végül pedig összefésülöm a
két listát. Nem maradt más hátra, mint az összefésüléses rendezés
futásidejének kiszámítása: *O (n*log(n))*.
