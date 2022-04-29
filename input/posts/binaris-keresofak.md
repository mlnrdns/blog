Title: Bináris keresőfák
Published: 2013-05-10 01:54
Author: molnardenes
Tags: [adatszerkezet, algoritmus, OOP, recursion]
---

A **bináris keresőfák** olyan keresőfák, amelyekben minden csúcspontnak
legfeljebb 2 gyermeke van és tetszőleges *x* csúcsára igaz, hogy *x* bal
oldali részfájában található valamennyi elem kulcsa kisebb
kulcs(*x*)-nél, a jobb oldali részfa elemeinek kulcsai pedig nagyobbak.
Ez alapján egy bináris keresőfa minden részfája is bináris keresőfa.

![bináris
keresőfa](/assets/images/bkf01.png)

Minden csomópontból maximum két nyíl indul ki, a csomópont két
**gyereke** felé. A csomópont maga ilyenkor a **szülő** csomópont. A fa
**gyökerének** a szerkezet kezdőpontját nevezzük, ennek nincs szülője.
Azon elemek, amelyeknek nincsen gyerekük, a **levelek**. A fa egy
rekurzív adatszerkezet, minden elem jobb és bal oldali szomszédja is
egy-egy **részfa**.

A bináris keresőfa létrehozásához két objektumra lesz szükségünk. Kell
egy csúcsot reprezentáló objektum és kell magát a fát reprezentáló
objektum. Lássuk elsőként a csúcsot:

```python
class BKFcsucs():

    def __init__(self, t):        
        self.kulcs = t
        self.level()
        
    def level(self):
        self.bal = None
        self.jobb = None
        self.szulo = None
```

Ebben az objektumban definiáltuk a levelet. Itt is látszik, hogy a
levélnek nincsenek gyerekei. Most, hogy van már egy csúcs objektumunk,
hozzuk létre a fát:

```python
class BKF():

    def __init__(self):
        self.gyoker = None

```

Most egy üres fát hoztunk létre, amelynek egyelőre gyökere sincs.
Szükségünk van tehát egy metódusra, amelynek a segítségével
feltölthetjük a keresőfánkat. Ez legyen a beszúrás metódus:

```python
def beszuras(self, t):        
        uj = BKFcsucs(t)
        if self.gyoker is None:
            self.gyoker = uj
        else:
            csucs = self.gyoker
            while True:
                if t < csucs.kulcs:                    
                    if csucs.bal is None:
                        csucs.bal = uj
                        uj.szulo = csucs
                        break
                    csucs = csucs.bal
                else:
                    if csucs.jobb is None:
                        csucs.jobb = uj
                        uj.szulo = csucs
                        break
                    csucs = csucs.jobb
        return uj
```

Ha még nem volt a fának gyökere, akkor az új elem lesz az. Ha már volt,
akkor megnézzük, hogy az új elem kisebb vagy nagyobb a csúcsnál. Ha
kisebb, akkor balra, ha nagyobb, akkor jobbra szúrjuk be. Szükségünk
lesz ezenkívül egy kereső és egy törlő metódusra is. Ezek a
következőképpen néznek ki:

```python
 def kereses(self, t):
        csucs = self.gyoker
        while csucs is not None:
            if t == csucs.kulcs:
                return csucs
            elif t < csucs.kulcs:
                csucs = csucs.bal
            else:
                csucs = csucs.jobb
        return None

    def torles(self):
        if self.gyoker is None:
            return None, None
        else:
            csucs = self.gyoker
            while csucs.bal is not None:
                csucs = csucs.bal
            if csucs.szulo is not None:
                csucs.szulo.bal = csucs.jobb
            else: 
                self.gyoker = csucs.jobb
            if csucs.jobb is not None:
                csucs.jobb.szulo = csucs.szulo
            szulo = csucs.szulo
            csucs.level()
            return csucs, szulo
```

A keresés viszonylag egyértelmű. Megvizsgáljuk a gyökeret, ha egyezést
találunk, akkor ott van a keresett érték. Ha nem, akkor azt vizsgáljuk,
hogy a keresett érték kisebb vagy nagyobb. Ha kisebb, akkor elindulunk
balra, és minden csúcsnál elvégezzük ezt a vizsgálatot, ha nagyobb,
akkor jobbra indulva tesszük ugyanezt. Ha nem találtuk a keresett
értéket, akkor *None* értéket ad vissza a programunk.

A törlés is csak elsőként tűnik bonyolultnak. Mivel a legkisebb elemet
távolítjuk el, így balra kell bejárnunk a fát. Ha megtaláltuk, akkor
eltávolítjuk a csúcsot, és a jobb részfa csúcsa lesz az új szülő.

Eddig nagyon jól működik a bináris keresőfánk, de még nem vagyunk
képesek megjeleníteni. Szükségünk van tehát egy metódusra, amivel
megjeleníthetjük a képernyőn. Az egyszerűség kedvéért ASCII grafikát
használunk csak. Hozzuk létre a megjelenítésért felelős metódust:

```python
    def __str__(self):
        if self.gyoker is None: return '<üres fa>'
        
        def megjelenites(csucs):
            if csucs is None: return [], 0, 0
            abra = str(csucs.kulcs)
            bal_el, bal_hely, bal_szelesseg = megjelenites(csucs.bal)
            jobb_el, jobb_hely, jobb_szelesseg = megjelenites(csucs.jobb)
            kozep = max(jobb_hely + bal_szelesseg - bal_hely + 1, len(abra), 2)
            hely = bal_hely + kozep // 2
            szelesseg = bal_hely + kozep + jobb_szelesseg - jobb_hely
            while len(bal_el) < len(jobb_el):
                bal_el.append(' ' * bal_szelesseg)
            while len(jobb_el) < len(bal_el):
                jobb_el.append(' ' * jobb_szelesseg)
            if (kozep - len(abra)) % 2 == 1 and csucs.szulo is not None and \
               csucs is csucs.szulo.bal and len(abra) < kozep:
                abra += '.'
            abra = abra.center(kozep, '.')
            if abra[0] == '.': abra = ' ' + abra[1:]
            if abra[-1] == '.': abra = abra[:-1] + ' '
            el = [' ' * bal_hely + abra + ' ' * (jobb_szelesseg - jobb_hely),
                     ' ' * bal_hely + '/' + ' ' * (kozep-2) +
                     '\\' + ' ' * (jobb_szelesseg - jobb_hely)] + \
              [bal_el + ' ' * (szelesseg - bal_szelesseg - jobb_szelesseg) +
               jobb_el
               for bal_el, jobb_el in zip(bal_el, jobb_el)]
            return el, hely, szelesseg
        return '\n'.join(megjelenites(self.gyoker) [0])
```

Ránézésre talán kissé bonyolultnak tűnik, ez azonban nincs így. Amit meg
kell jelenítenünk: az éleket és csúcsokat. Ahhoz, hogy ezt meg tudjuk
tenni áttekinthető módon, szükséges a fa szélességének vizsgálata
mindkét oldalon, és ehhez képest nekünk középre kell igazítanunk az
ábrát. Ami itt újdonság lehet az a **zip** függvény. Ez arra szolgál,
hogy két tuple-ből úgy adja vissza az értékeket, hogy mindkettőből veszi
elsőként az elsőt, majd a másodikat, harmadikat, stb. Lássuk egy
példával:

```python
>>> x = [1, 2, 3]
>>> y = [4, 5, 6]
>>> for a, b in zip(x, y):
    print (a, b)
    
1 4
2 5
3 6
```

Ezzel kész is vagyunk. Kipróbálhatjuk a programunkat a következő módon:

```python
>>> fa = BKF()
>>> for i in range(4):
    fa.beszuras(i)
    print('\n\n')
    print(fa)
```

------------------------------------------------------------------------

Az elkészített keresőfa jelenleg kiegyensúlyozatlan. A továbbiakban
létrehozunk egy kiegyensúlyozott
[AVL-keresőfát](http://hu.wikipedia.org/wiki/AVL-fa "AVL-fa wikipédia cikk").
A kiegyensúlyozás esetén minden csúcsnak van egy úgynevezett
egyensúly-faktora, amit megkapunk, ha kivonjuk a jobb részfa magasságát
a bal részfáéból. Egy csúcs kiegyensúlyozott, ha ez az érték 1, 0 vagy
-1. Minden más esetben a csúcs kiegyensúlyozatlan és forgatásokat
igényel. Az AVL-fa esetén szükséges a fa magasságának az ismerete:

```python
def magassag(csucs):
    if csucs is None:
        return -1
    else:
        return csucs.magassag

def magassag_frissitese(csucs):
    csucs.magassag = max(magassag(csucs.bal), magassag(csucs.jobb)) + 1
```

Az **AVL-fa** osztály szülőosztálya a fenti BKF osztály. Szükség van
azonban mind a balra, mind a jobbra forgatás metódusát létrehozni. A
forgatás módjára itt nem térek ki, a fenti linken is, és sok más helyen
megtalálható. Itt most csak az implementálásával foglalkozunk. A
forgatások metódusait pedig fel kell használnunk ahhoz, hogy
kiegyensúlyozzuk a fát. Ehhez is egy külön metódust fogunk létrehozni.
Végül a beszúrás és törlés metódusokat felhasználjuk a BKF osztályból,
de kiegészítjük azokat a kiegyensúlyozással (ez jó példa lehet a
**metódus felülbírálására** (**method overloading**)). Minden beszúrás
és törlés után szükséges a fa egyensúlyi vizsgálata, és szükség szerint
annak kiegyensúlyozása. Ennyi felvezetés után lássuk végre a kódot:

```python
class AVL(BKF):
    def balra_forgatas(self, x):
        y = x.jobb
        y.szulo = x.szulo
        if y.szulo is None:
            self.gyoker = y
        else:
            if y.szulo.bal is x:
                y.szulo.bal = y
            elif y.szulo.jobb is x:
                y.szulo.jobb = y
        x.jobb = y.bal
        if x.jobb is not None:
            x.jobb.szulo = x
        y.bal = x
        x.szulo = y
        magassag_frissitese(x)
        magassag_frissitese(y)

    def jobbra_forgatas(self, x):
        y = x.bal
        y.szulo = x.szulo
        if y.szulo is None:
            self.gyoker = y
        else:
            if y.szulo.bal is x:
                y.szulo.bal = y
            elif y.szulo.jobb is x:
                y.szulo.jobb = y
        x.bal = y.jobb
        if x.bal is not None:
            x.bal.szulo = x
        y.jobb = x
        x.szulo = y
        magassag_frissitese(x)
        magassag_frissitese(y)

    def beszuras(self, t):        
        csucs = BKF.beszuras(self, t)
        self.kiegyensulyozas(csucs)

    def kiegyensulyozas(self, csucs):
        while csucs is not None:
            magassag_frissitese(csucs)
            if magassag(csucs.bal) >= 2 + magassag(csucs.jobb):
                if magassag(csucs.bal.bal) >= magassag(csucs.bal.jobb):
                    self.jobbra_forgatas(csucs)
                else:
                    self.balra_forgatas(csucs.bal)
                    self.jobbra_forgatas(csucs)
            elif magassag(csucs.jobb) >= 2 + magassag(csucs.bal):
                if magassag(csucs.jobb.jobb) >= magassag(csucs.jobb.bal):
                    self.balra_forgatas(csucs)
                else:
                    self.jobbra_forgatas(csucs.jobb)
                    self.balra_forgatas(csucs)
            csucs = csucs.szulo

    def torles(self):
        csucs, szulo = BKF.torles(self)
        self.kiegyensulyozas(szulo)
```

A kiegyensúlyozatlan fánál ismert módon tesztelhetjük az AVL-fánkat is.
Értelemszerűen a *fa = BKF()* helyett ebben az esetben a *fa = AVL()*
használandó. Érdemes lehet esetleg ugyanazokkal az értékekkel mindkét
módon kirajzolni a keresőfákat.
