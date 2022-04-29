Title: Emelt informatika érettségi programozási feladat 2011
Published: 2013-03-17 13:01
Author: molnardenes
Tags: [erettsegi]
---

Igyekszem a blogon megoldási útmutatókat adni az eddigi és majd a
következő informatika érettségi programozási feladataihoz. Elsőként a
2011. májusi feladatot nézzük meg!

Az **első feladatban** be kell kérnünk egy szót, és meg kell
állapítanunk róla, hogy tartalmaz-e magánhangzót. Nézzük:

```python
mgh = ['a', 'e', 'i', 'o', 'u']
print('1. feladat\n')
kapcsolo = 0
szo = input('Adjon meg egy szót: ').lower()
for kar in szo:
    if kar in mgh:
        kapcsolo = 1
if kapcsolo == 1:
    print('Van benne magánhangzó.')
else:
    print('Nincs magánhangzó.')
```

Létrehoztam egy listát, ami a magánhangzókat tartalmazza. A feladat
kiírása szerint elég az angol ábécé magánhangzóit figyelembe venni. A
második sorban kiírjuk a feladat sorszámát, és egy sorvége karaktert,
hogy a megoldás a következő sorba kerüljön majd. Szükségünk lesz egy
változóra, aminek a segítségével számon tartjuk, hogy találtunk-e
magánhangzót. A *kapcsolo* változónk értéke legyen 0. Ha találunk majd
magánhangzót, az értékét 1-re fogjuk állítani. Kérjünk be most egy szót
(a **lower()** metódussal alakítsuk kisbetűssé), és **for ciklussal**
iteráljunk szépen végig a *szo* stringen karakterenként! Ha valamelyik
karakter megtalálható az *mgh* listában (ezt az **in** kulcsszóval
ellenőrizzük), vagyis magánhangzó, akkor a kapcsolónk értékét állítsuk
1-re! A feladat végén a kapcsolónk értékétől függően írjuk ki, hogy
van-e magánhangzó az adott szóban!

A **második feladat** szerint a mellékelt fájlban meg kell keresnünk a
leghosszabb szót és ki kell írnunk a képernyőre. Több azonos hosszúságú
esetén elég az egyiket kiírni. Azt kéri még a feladat, hogy ne egyszerre
olvassuk be az összes adatot. Első lépésben kiírjuk itt is a feladat
sorszámát, majd létre kell hoznunk egy változót, amelyben a leghosszabb
szót el tudjuk tárolni. Mivel a feladat azt kéri, hogy nem olvassuk be
egyszerre a teljes listát, ezért soronként kell végigmennünk a fájlon a
fájl megnyitása után. Erre az előző feladatban is használt **for
ciklus** alkalmas. Tudjuk, hogy minden sor egy szót tartalmaz, viszont
arra figyelnünk kell, hogy a végén sorvége karakter van! Hozzunk létre
tehát egy változót, ami az aktuális sort tartalmazza a sorvége karakter
eltávolítása után. Legyen ez az *aktSzo* változó. Ez tehát az éppen
aktuális szavunkat tartalmazza. Vizsgáljuk meg, hogy az aktuális szavunk
hosszabb-e, mint az eddigi leghosszabb szó. Amennyiben igen, akkor a
leghosszabb szó a mostani, éppen aktuális szó lesz. Ha a fájl végére
értünk, írjuk ki a leghosszabb szót, és annak hosszát a **len()**
függvénnyel. Figyeljünk arra, hogy a feladat végén lezárjuk a fájlt!
Lássuk a második feladat kódját:

```python
print('\n2. feladat')
leghosszabbSzo = ''
fajl = open('szoveg.txt', 'r')
for sor in fajl:
    aktSzo = sor.strip('\n')
    if len(aktSzo) > len(leghosszabbSzo):
        leghosszabbSzo = aktSzo
print('A leghosszabb szó a(z) {0} karakter hosszúságú {1}.'.format(len(leghosszabbSzo), leghosszabbSzo))
fajl.close()
```

A szövegek formázására a **string.format()** metódust használjuk. A
kapcsos zárójelek közé tett számok a string.format() metódus adott
sorszámú elemét fogják megjeleníteni, azok helyettesítőiként szerepelnek
jelenleg a szövegben. A használatának legnagyobb előnye, hogy nem kell
foglalkoznunk azzal, hogy milyen típusú a változónk. Nem kell pluszban a
str() metódussal stringgé alakítani egy integert például.

A **harmadik feladatban** azt kell megvizsgálnunk, hogy hány olyan szó
van a szövegfájlban, amelyben több a magánhangzók száma, mint a nem
magánhangzóké. A feladat végén egy kis statisztikát is ki kell írnunk.
Az első feladatnál megadott magánhangzó listát fogjuk itt is használni.
Kiírjuk a feladat sorszámát. Szükségünk lesz néhány változóra,
amelyekben az éppen aktuális szó magánhangzóinak a számát, a megtalált
szavak számát és az összes szó számát tároljuk. A fájl megnyitása után
ismét iteráljunk rajta végig a **for ciklussal**! Az előző feladatban
látott módon tároljuk el az *aktSzo* változóban a sorvége karaktertől
megtisztított aktuális sort (szót). A feladatunk az, hogy megállapítsuk,
hogy mennyi magánhangzó van ebben a szóban, és ezeknek a száma több-e,
mint a nem magánhangzók száma. Erre a legjobb módszer, ha az adott
szavunk összes karakterén végigiterálunk ismételten egy **for
ciklussal**, és ha az aktuális karakter eleme az *mgh* listának, akkor
az *mghSzama* változót növeljük meg 1-gyel. Ha végeztünk a belső for
ciklussal (a külsővel ugye a sorokon iterálunk végig, a belsővel pedig
az aktuális sor szaván), akkor meg kell vizsgálnunk, hogy a magánhangzók
száma az aktuális szavunk esetén nagyobb-e, mint az összes többi
karakter összesen. A többi karakter számát egyszerűen megkapjuk, ha az
aktuális szó hosszából (**len()** függvény!) kivonjuk a magánhangzók
számát. Amennyiben nagyobb a magánhangzók száma, mint a nem
magánhangzóké, kiírjuk a szót, és a *talaltSzavak* változó értékét
megnöveljük 1-gyel. Ha ezekkel végeztünk, lenullázzuk az *mghSzama*
változót (hogy a ciklus a következő szónál is 0-tól indítsa a
magánhangzók számlálását), és megnöveljük 1-gyel az *osszesSzo*
változónkat. Ha végigértünk a fájlon, a végén kiírjuk a statisztikát, a
százalékot két tizedesjegyre kerekítjük a **round()** függvénnyel. A
végén lezárjuk a fájlt.

```python
print('\n3. feladat')
mghSzama = 0
talaltSzavak = 0
osszesSzo = 0
fajl = open('szoveg.txt', 'r')
for sor in fajl:
    aktSzo = sor.strip('\n')
    for kar in aktSzo:
        if kar in mgh:
            mghSzama += 1
    if mghSzama > (len(aktSzo) - mghSzama):
        print(aktSzo, end=' ')
        talaltSzavak += 1
    mghSzama = 0
    osszesSzo += 1
print('nStatisztika: {0}/{1}: {2}%'.format(talaltSzavak, osszesSzo, round(talaltSzavak/osszesSzo*100, 2)))
fajl.close()
```

A **negyedik feladatban** a fájlból ki kell gyűjteni az ötbetűs
szavakat, majd bekérni egy hárombetűs szórészletet, és ki kell írni
azokat a szavakat, amelyeknek a a szórészlet megegyezik a középső három
betűjével. Először itt is a feladatszám kiírása történik meg.
Létrehozunk két listát (a *letra* majd a következő, 5-ös feladathoz lesz
szükséges, ebben a feladatban nem használjuk fel), az egyikben az
ötbetűs szavakat tároljuk majd el, a másikban azokat, amelyek
megfelelnek a feltételnek, miszerint a középső három betűjük megegyezik
a felhasználótól bekért 3 betűvel. A fájl megnyitása után ismét **for
ciklussal** végigiterálunk a sorokon, és létrehozzuk az *aktSzo*
változónkat a fentebb említett okból és módon. Ha az aktuális szavunk
hossza 5, akkor adjuk hozzá az **append()** metódussal az *otBetus*
listánkhoz. Ha a fájl végére értünk, kérjünk be egy háromkarakteres
szórészletet a felhasználótól, ás tároljuk a *reszlet* változóban. Most
az *otBetus* listán kell végigiterálnunk, és minden egyes szó esetén
megvizsgálni, hogy a szó közepe, azaz a 2-4. karaktere megegyezik-e a
bekért részlettel. Figyeljünk arra, hogy a Python 0-tól számoz és a
szeletelés utolsó száma már nem fog beletartozni a szeletbe, így a 2-4.
karakter vizsgálata a *szo[1:4]* formában történik! Ha tehát ez a
részlet (szelet) megegyezik a bekért *reszlet* változó tartalmával,
akkor írjuk ki a szót! Az 5. feladathoz pedig adjuk hozzá a *letra*
listához! Végül ne felejtsük el bezárni a fájlt!

```python
print('\n4.feladat')
otBetus = []
letra = []
fajl = open('szoveg.txt', 'r')
for sor in fajl:
    aktSzo = sor.strip('\n')
    if len(aktSzo) == 5:
        otBetus.append(aktSzo)
reszlet = input('Adjon meg egy háromkarakteres szórészletet: ')
for szo in otBetus:
    if szo[1:4] == reszlet:
        letra.append(szo)
        print(szo, end = ' ')
fajl.close()
```

Az **ötödik** és egyben utolsó feladatban bizonyos feltételekkel ki kell
írnunk a szólétra szavait! Csak akkor írjuk ki az adott szót, ha van
legalább egy párja. Az egyező közepű szavak csoportosítva legyenek
kiírva, egymás alá. Egy újabb csoportot az előző csoporttól egy üres sor
válasszon el! Nézzük előbb a kódot:

```python
print('\n5. feladat')
fajl = open('letra.txt', 'a')
if len(letra) > 1:
    for szo in otBetus:
        if szo[1:4] == reszlet:
            fajl.write(szo + '\n')
    fajl.write('\n')
    print('Megírtuk az adatokat a fájlba.')
else:
    print('Nincs párja. Nem írjuk fájlba.')
fajl.close()
```

Feladatsorszám kiírásával kezdünk ismét. Ezután megnyitjuk a fájlt
írásra. Ha az előző feladatban létrehozott *letra* nevű változónk értéke
nagyobb 1-nél, azaz legalább két eleme van, akkor ki kell írnunk az
adatokat a fájlba. Ha nincs legalább két eleme, akkor a feladat
feltétele szerint nem írjuk ki, és ezt jelezzük a felhasználónak. Ha
kiírhatjuk, akkor végigiterálunk az ötbetűs szavak listáján, és ha az
éppen aktuális szó 2-4. karaktere megegyezik az előző feladatban
megadott 3 betűs szórészlettel, akkor kiírjuk fájlba a szót, és egy
sorvége karaktert is a szóval, hogy a következő majd alá kerülhessen. Ha
végeztünk a listával, akkor egy sorvége karaktert pluszban beírunk a
fájlba, így biztosítva, hogy a következő listánk majd egy új csoportba
kerüljön a fájlban, egy üres sorral elválasztva. Végül lezárjuk a fájlt.

Ezzel kész is van a 2011-es emelt szintű programozási feladat.
Igyekeztem a lehető legérthetőbben és átláthatóbban megírni a kódot,
ennél sűrűbben is lehetőség volna rá, de azt majd a későbbiekben!
