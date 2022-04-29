Title: Kollaboratív szűrés Manhattan- és euklideszi távolság
Published: 2013-06-15 23:05
Author: molnardenes
Tags: [data mining]
---

A **kollaboratív szűrés **az adatbányászatban használt ajánlórendszerek
 egy módszere. A kollaboratív jelző azt jelenti, hogy az ajánlatok
megtétele más emberek preferenciái segítségével történik meg. Ha például
egy adott felhasználó számára szeretnénk könyvet ajánlani, akkor egy
lehetséges módszer, hogy megkeressük azokat a felhasználókat, akiknek
hasonló az érdeklődése, és az ő általuk kedveltek közül ajánlunk egyet a
kiválasztott felhasználónknak.

Másképpen megfogalmazva, olyan felhasználókat keresünk, akiknek az
ízlése hasonló a mi felhasználónkéhoz, vagy még másképpen, akiknek
legkevésbé áll távol az érdeklődése. Ennek vizsgálatához szükségünk van
valamilyen távolságdefinícióra: a távolság valamely halmaz minden
rendezett (A, B) elempárjához hozzárendelt d(A, B) nemnegatív szám,
amely három pont esetén teljesíti a
háromszög-egyenlőtlenséget: ![háromszög-egyenlőtlenség](http://latex.codecogs.com/gif.latex?\inline&space;d(A,B)&space;+&space;d(B,C)&space;\geq&space;d(A,C)).

Ha ezt a két pontot egy koordinátarendszerben ábrázoljuk, akkor a két
pont: ![\\inline
A(x\_{a},y\_{a})](http://latex.codecogs.com/gif.latex?\inline&space;A(x_%7Ba%7D,y_%7Ba%7D)) és ![\\inline
B(x\_{b},y\_{b})](http://latex.codecogs.com/gif.latex?\inline&space;B(x_%7Bb%7D,y_%7Bb%7D))

A koordinátakülönbségek abszolútértéke: ![\\inline X\_{ab} = \\left |
x\_{a} - x\_{b} \\right
|](http://latex.codecogs.com/gif.latex?\inline&space;X_%7Bab%7D&space;=&space;\left&space;%7C&space;x_%7Ba%7D&space;-&space;x_%7Bb%7D&space;\right&space;%7C) és ![\\inline
Y\_{ab} = \\left | y\_{a} - y\_{b} \\right
|](http://latex.codecogs.com/gif.latex?\inline&space;Y_%7Bab%7D&space;=&space;\left&space;%7C&space;y_%7Ba%7D&space;-&space;y_%7Bb%7D&space;\right&space;%7C)

Ekkor a két pont távolsága: ![két_pont](http://latex.codecogs.com/gif.latex?\inline&space;d(ab)&space;=&space;\left&space;%5B&space;\alpha&space;\left&space;(&space;X_%7Bab%7D&space;\right&space;)%5E%7Bp%7D&space;+&space;\beta&space;\left&space;(&space;Y_%7Bab%7D&space;\right&space;)%5E%7Bp%7D&space;\right&space;%5D%5Er)

Ebből az általános képletből megkaphatjuk az ismertebb távolságfajtákat,
ha a konstansoknak értéket adunk. Ha minden konstans értéke 1, akkor az
úgynevezett **Manhattan-távolságot** kapjuk meg (vagy másképpen
háztömb-távolság), ha viszont *p = 2, r = 1/2*, a másik két állandó
pedig 1, akkor **euklidészi távolságról** beszélünk.

------------------------------------------------------------------------

A Manhattan-távolság képlete tehát a következő formában adható meg:

![manhattan](http://latex.codecogs.com/gif.latex?\inline&space;d(x,y)&space;=&space;\sum&space;_%7Bk=1%7D%5En\left&space;%7C&space;x_%7Bk%7D&space;-&space;y_%7Bk%7D&space;\right&space;%7C)

Az euklidészi távolság képlete pedig:

![euclidean](http://latex.codecogs.com/gif.latex?\inline&space;d(x,y)&space;=&space;\sqrt%7B\sum&space;_%7Bk=1%7D%5En\left&space;(x_%7Bk%7D&space;-&space;y_%7Bk%7D)%5E2%7D)

Hogy még érthetőbbé váljon, nézzünk rá egy példát. Vegyünk két
hallgatót, Anikót és Alexandrát, és nézzük meg, hogy milyen könyveket
milyennek értékelnek egy 5 fokú skálán. A képletben az *x* és *y* Anikót
és Alexandrát jelképezi, *d(x,y)* a távolságuk, míg az *n* azoknak a
könyveknek a száma, amelyeket mindketten értékeltek. Határozzuk meg
tehát a Manhattan-távolságukat!

  
  A könyv címe           | Anikó értékelése|Alexandra értékelése| Eltérés|Eltérés^2
  -----------------------| ----------------|--------------------| -------|------------
  A rózsa neve           | 5               |2.5                 | 2.5    |6.25
  A pusztai farkas       | 4.5             |3                   | 1.5    |2.25
  Sántaiskola            | -               |3                   | -      |-
  Sorstalanság           | 4               |-                   | -      |-
  Virágvasárnap          | 3.5             |3                   | 0.5    |0.25
  A nagy Gatsby          | 2               |5                   | 3      |9
  **Manhattan-távolság** | ** **           |** **               | **7.5**|** **
  **Euklidészi távolság**| ** **           |** **               | ** **  |**4.21**
  -----------------------| ----------------|--------------------| -------|------------


A Manhattan-távolságnál egyszerű dolgunk van, hiszen annyit kell
tennünk, hogy a mindkét olvasó által értékelt könyvek értékeinek
különbségeit összeadjuk. Természetesen az euklidészi távolság
kiszámítása sem okozhat komoly fejtörést, hiszen ott a különbségek
négyzeteit adjuk össze, majd a végeredményből vonunk négyzetgyököt.

Nézzük most a Manhattan-távolság Python implementációját:

```python
def manhattan(ertekeles1, ertekeles2):
    """Manhattan-távolság meghatározása. Mindkét értékelés egy szótár a
       a következő formában: {'könyvcím1': 3.0, 'könyvcím2': 2.5}"""
    tavolsag = 0
    vanKozosErtekeles = False
    for key in ertekeles1:
        if key in ertekeles2:
            tavolsag += abs(ertekeles1[key] - ertekeles2[key])
            vanKozosErtekeles = True
    if vanKozosErtekeles:
        return tavolsag
    else:
        return -1
```

A következő feladatunk, hogy az olvasók Manhattan-távolsága alapján
meghatározzuk a kiválasztott olvasóhoz legközelebbi olvasót. Ez nyilván
két személy esetén egyértelmű, de több száz vagy több ezer személy
esetén kifejezetten hasznos lehet ajánlattétel esetén. Nézzük ennek a
megvalósítását:

```python
def legkozelebbiSzomszed(felhasznalonev, felhasznalok):
    """Egy rendezett listát készít a távolságok szerint rendezve a felhasználókról"""
    tavolsagok = []
    for felhasznalo in felhasznalok:
        if felhasznalo != felhasznalonev:
            tavolsag = manhattan(felhasznalok[felhasznalo], felhasznalok[felhasznalonev])
            tavolsagok.append((tavolsag, felhasznalo))
    tavolsagok.sort()
    return tavolsagok
```

Most, hogy már meg tudjuk adni a legközelebbi szomszédot is, minden
akadály elhárult egy ajánlórendszer létrehozása elöl. Ki kell
választanunk tehát egy felhasználót, megkeresni a legközelebbi
szomszédját, és ajánlani neki azt a könyvet, amit ő maga még nem
értékelt, de a legközelebbi szomszédja igen. Lássuk ezt hogyan
implementáljuk:

```python
def ajanlas(felhasznalo, felhasznalok):
    """Ajánlatok listáját adja meg"""

    legkozelebbi = legkozelebbiSzomszed(felhasznalo, felhasznalok)[0][1]

    ajanlasok = []

    szomszedErtekelesei = felhasznalok[legkozelebbi]
    felhasznaloErtekelesei = felhasznalok[felhasznalo]
    for konyv in szomszedErtekelesei:
        if not konyv in felhasznaloErtekelesei:
            ajanlasok.append((konyv, szomszedErtekelesei[konyv]))

    return sorted(ajanlasok, key=lambda konyvTuple: konyvTuple[1], reverse = True)
```

A kód egyértelmű viszonylag, a végén a visszatérési érték viszont
tartogat egy újdonságot, mégpedig az úgynevezett **lambda-függvényt**. A
lambda nem más, mint egy névtelen, egyszer használatos függvény.
Szintaxisa: *lambda argumentumok: kifejezés*. Minden esetben kifejthető
bővebb kifejezéssel is:

```python
>>> osszeadas_lambdaval = lambda x, y: x+y
>>> def osszeadas_fuggveny(x, y):
        return x+y
```

Ezzel kész is vagyunk, futtathatunk néhány tesztet, hogy meglássuk a
program működését. Készítsünk egy szótárt, amelyben a kulcsok a nevek,
és a hozzájuk tartozó értékek szintén szótárak, mégpedig egy könyvcímet
és 1-5 közötti értéket tartalmazó szótárak:

```python
    felhasznalok = {"Anikó": {"A rózsa neve": 3.5, "A pusztai farkas": 2.0, "Sorstalanság": 4.5, "Sántaiskola": 5.0, "Holt lelkek": 1.5, "Karamazov testvérek": 2.5, "A kurblimadár krónikája": 2.0},
             "Alexandra":{"A rózsa neve": 2.0, "A pusztai farkas": 3.5, "A nagy Gatsby": 4.0, "Sántaiskola": 2.0, "Holt lelkek": 3.5, "A kurblimadár krónikája": 3.0},
             "Brigitta": {"A rózsa neve": 5.0, "A pusztai farkas": 1.0, "A nagy Gatsby": 1.0, "Sorstalanság": 3.0, "Sántaiskola": 5, "Holt lelkek": 1.0},
             "Dénes": {"A rózsa neve": 3.0, "A pusztai farkas": 4.0, "A nagy Gatsby": 4.5, "Sántaiskola": 3.0, "Holt lelkek": 4.5, "Karamazov testvérek": 4.0, "A kurblimadár krónikája": 2.0},
             "Ágnes": {"A pusztai farkas": 4.0, "A nagy Gatsby": 1.0, "Sorstalanság": 4.0, "Karamazov testvérek": 4.0, "A kurblimadár krónikája": 1.0},
             "Zsolt":  {"A pusztai farkas": 4.5, "A nagy Gatsby": 4.0, "Sorstalanság": 5.0, "Sántaiskola": 5.0, "Holt lelkek": 4.5, "Karamazov testvérek": 4.0, "A kurblimadár krónikája": 4.0},
             "János": {"A rózsa neve": 5.0, "A pusztai farkas": 2.0, "Sorstalanság": 3.0, "Sántaiskola": 5.0, "Holt lelkek": 4.0, "Karamazov testvérek": 5.0},
             "Katalin": {"A rózsa neve": 3.0, "Sorstalanság": 5.0, "Sántaiskola": 4.0, "Holt lelkek": 2.5, "Karamazov testvérek": 3.0}
            }
    
    print(ajanlas('Brigitta', felhasznalok))
```
